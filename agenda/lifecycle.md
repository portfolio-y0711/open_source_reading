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




```
