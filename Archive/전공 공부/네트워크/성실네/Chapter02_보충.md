# 소켓 프로그래밍

## 기존 모델
---
---
### Blocking I/O
- 애플리케이션이 socket()으로 연결을 맺음
- read()나 recv()로 호출 후 데이터가 도착할 때까지 멈춤 (blocking)
#### 단점
- 다중 처리를 위해 멀티프로세스/멀티스레드 모델 필요
- context switching 비용 높음
---
### Non-Blocking I/O
- recv() 호출 시 데이터가 없으면 즉시 -1 반환
- polling 방식: 프로그램이 멈추지 않고 반복적으로 확인
#### 단점
- CPU가 계속 소켓을 확인하므로 busy-waiting -> 비효율적
---
### I/O Multiplexing
- select()나 poll()을 사용해 여러 소켓을 하나의 스레드에서 감시
- CPU가 계속 소켓을 확인하는 대신 OS가 준비된 소켓을 알려줌
- select()
	- fd 집합을 커널에 넘기면 준비된 소켓만 알려줌
	- 단점:
		- 감시할 수 있는 소켓 수 제한
		- 매번 전체 fd 집합을 커널에 복사해야 함
- poll()
	- 감시할 수 있는 소켓 수에 제한이 없음
	- 구조체 배열로 fd를 관리하여 유연한 관리 가능
		- struct pollfd { fd, events, revents }
	- 커널은 events를 보고 감시하며, 발생한 이벤트를 revents에 기록
	- 프로그램은 poll()이 반환되면 revents를 순회하여 이벤트 처리
	- 단점:
		- 모든 fd를 매번 검사해야 함
		- 여전히 서버 규모가 커질수록 느려짐
- epoll()
	- 리눅스 전용 고성능 I/O 다중화 API
	- poll()의 문제를 해결한 이벤트 기반 모델
	- 커널 이벤트 큐 방식으로 fd 감시
	- 대규모 서버에 최적화
	- select/poll: 매번 전체 소켓 목록을 커널에 전달
	- epoll: 커널에 한 번 등록해두고, 이벤트만 알려주는 방식

---
---
## 그 후

### io_uring
### Async/Await