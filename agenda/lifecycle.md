```
라이프 사이클 (start-stop)
# start

setInterval -> processJobs를 주기적으로 반복
_processInterval에 setInterval 참조를 기록하여 stop시에 clearInterval로 제거

# stop

_processInterval에 저장해 둔 setInterval을 clearInterval로 해제함
_lockedJobs에 저장된 lock 목록을 가져와 mongodb에서 lockedAt 필드를 null로 바꾸고
_lockedJobs에 빈 배열 할당


라이프 사이클 (processJobs - )

```
