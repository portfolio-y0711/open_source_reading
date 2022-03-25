```
# 아젠다가 실행할 작업의 시간을 계산하고 이를 소비하는 과정

run - computeNextRunAt 

interval (repeatInterval 속성이 있는 경우) - 
    cron-parser로 파싱
    [cronTime.]next().toDate()로 다음 시간 계산
repeatAt (repeatAt 속성이 있는 경우) - 
    date.js로 Date로 변환
    []

```