```
라이프 사이클 (start-stop)
# start

setInterval -> processJobs를 주기적으로 반복
_processInterval에 setInterval 참조를 기록하여 stop시에 clearInterval로 제거

# stop

_processInterval에 저장해 둔 setInterval을 clearInterval로 해제함
_lockedJobs에 저장된 lock 목록을 가져와 mongodb에서 lockedAt 필드를 null로 바꾸고
_lockedJobs에 빈 배열 할당


라이프 사이클 (processJobs - [jobQueueFilling - findAndLockNextJob - [jobProcessing - runOrRetry - run - processJobResult]])

# processJobs

_definitions 객체에 사전에 정의된 작업명을 순회
jobQueueFilling을 병렬 처리함

# jobQueueFilling

_nextScanAt에 다음 실행 시간을 기록(processDbResult에서 사용:check요망)
_isJobQueueFilling에 작업명 별로 boolean 값을 저장(lockOnTheFly에서 사용:check요망)
_findAndLockNextJob으로 작업 탐색 
더이상 남은 작업이 없을 때까지 탐색된 작업을 enque하고 jobQueueFilling하는 것을 반복
jobQueueFilling이 끝나면 jobProcessing 호출

# findAndLockNextJob

disabled되지 않은 작업 중
잠긴 작업이 아니거나(lockedAt: null)
금번 작업 대상이거나(nextRunAt: { $lte : _nextScanAt })
잠겼지만 잠금 유효기간이 지난 경우($lte : lockDeadline )
lockedAt을 현재 시간으로 갱신하고
검색된 작업 단건 반환

# jobProcessing

returnNextConcurrencyFreeJob으로 작업 가능(설정된 컨커런시보다 작업 중인 프로세스 개수가 적은)한 job을 반환 받고
nextRunAt이 경과된 경우 runOrRetry 실행


(
    # returnNextConcurrencyFreeJob

    정의된 작업에 설정된 concurrency 수준보다 실행중인 작업의 수가 적을 경우
    큐에 있는 작업을 꺼내서 반환
)

# runOrRetry

jobQueue에서 작업을 디큐하고 정의된 작업의 concurrency 수준보다 실행중인 작업의 수가 적고, maxConcurrency보다 전체 작업의 수가 적을 경우

디큐된 작업의 lock 유효기간이 만료되었을 경우 락을 해제하고 실행중인 작업(_runningJobs)에 작업을 추가한뒤
해당 작업의 running 카운트 증가

작업 실행 (job.run) 후 processJobResult로 결과 전달


# run

작업 실행시 job의 lastRunAt을 현재 시간으로 갱신하고
nextRunAt을 갱신 (computeNextRunAt)하고 
변경 사항을 mongodb에 저장

작업 완료시 callback을 통해
lastFinishedAt이 현재 시각으로 갱신되고
lockedAt = null 설정됨


(
    # computeNextRunAt
    job의 반복 주기와 nextRunAt 설정 시간을 이용하여
    다음 번 실행 시점(nextRunAt)을 갱신
)

# processJobResult

작업이 성공했으므로 _runningJobs에서 작업을 제거하고
running 카운터에서 차감
마찬가지로 _lockedJobs에서 작업을 제거하고
locked 카운터에서 차감
jobProcessing을 호출하여 [jobProcessing - runOrRetry - run - processJobResult] 구간 반복 수행

```
