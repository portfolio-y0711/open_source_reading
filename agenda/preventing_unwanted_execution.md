```
# 강제 종료된 작업이 원치 않는 시간에 실행되는 것을 방지하는 방안

원치 않는 시간에 대한 정의

1. 요청 시간 =/ 실행 시간

2. 실행 유효시간? 세그먼트

통제 방법

* 통제 구간

1. agenda의 정보를 이용하고, presentation layer(agenda가 수행할 작업을 정의하는 어댑터 계층)에서 방지할 것인지

- 가용한 agenda 정보들

  * data
   . campaign 

  * lockedAt

  * lastRunAt

  * lastFinishedAt

  * nextRunAt

장점: 
   스케쥴러의 정보를 이용해 좀더 세밀한 조정이 가능 

단점: 
   로직이 agenda의 구현에 종속됨. 

2. campaign의 정보를 이용하고, application layer에서 방지할 것인지

- 가용한 campaign 정보들

    * campaign
      . cron
      . schedule

장점:
    제어 로직을 단순하게 유지할 수 있음

단점: 
   실행 방식에 대한 가정이 비즈니스 로직에 침투함

```
