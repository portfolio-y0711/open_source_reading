```
# 프로세스 강제 종료시 발생 가능한 시나리오

종료 상황:

● 라이프 사이클 (processJobs - [jobQueueFilling - findAndLockNextJob - [jobProcessing - runOrRetry - run - processJobResult]])에서 run에 진입한 이후 작업이 강제 중단되는 경우


(
    # processJobs

    jobQueueFilling 단계를 거치면서
    lockedAt이 작업 수행 시점으로 갱신됨
)

# run

job의 lastRunAt이 작업 수행 시점의 시간으로 갱신
1회성 작업이므로 nextRunAt은 갱신되지 않음
위의 변경 사항이 mongodb에 저장됨

작업이 수행되는 시간이 오래 걸리므로
프로세스 강제 종료는 lastRunAt을 기록하였으나
lastFinishedAt이 현재시간으로 갱신되고 lockAt = null으로 갱신되지 않았을 가능성이 높음

실제로 "621ed9196d42b5158a91f05f"의 아이디를 가진 작업은
lastRunAt이 "2022-03-20T00:51:43.477+00:00"이나
lastFinishedAt 속성이 없이
lockedAt: "2022-03-20T00:51:43.447+00:00" 기록만  남아있음

● 재시작시:

작업 수행중에 실패한 작업은 agenda가 재시작하면서 retry가 시작됨
이는 작업 수행중에 실패한 작업의 경우 lockedAt 시간이 기록되어 있기 때문임
findAndLockNextJob은 잠금 유효기간이 지난 작업을 검색($lte: lockDeadline)하여 jobQueueFilling의 대상으로 포함시키기 때문임

```
