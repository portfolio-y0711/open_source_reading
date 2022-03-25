```
# 아젠다가 특정 시간에 실행할 작업을 저장(생성)하는 방법

- 아젠다 에이전트에 위임하는 방법
now [-> create -> schedule -> save -> saveJob]
every [-> create -> repeatEvery -> save -> saveJob]
schedule [-> create -> save -> saveJob]

- 직접 생성 후 속성을 변경하는 방법
job 생성후 시간 관련 속성을 직접 변경하기
시간 관련 속성

repeatAt 특정 시간에 실행
(연관 속성: repeatAt)
repeatEvery 특정 주기로 반복
(연관 속성: repeatInterval,startDate,endDate,skipDays,skipImmediate)

```