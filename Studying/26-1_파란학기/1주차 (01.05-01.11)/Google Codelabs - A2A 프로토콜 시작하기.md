
참고: - [Google Codelabs - A2A 프로토콜 시작하기](https://codelabs.developers.google.com/intro-a2a-purchasing-concierge?hl=ko#1)

---

# 1. 소개

- A2A 프로토콜: 외부 시스템에 배포된 AI 에이전트 간의 통신을 표준화하도록 설계된 프로토콜.
- MCP(Model Context Protocol): 데이터 및 리소스와 LLM을 연결하는 프로토콜.

- A2A와 MCP의 차이점:
	- MCP:
		- 에이전트를 도구 및 데이터에 연결하는 복잡성을 낮추는 데 중점을 둠.
	- A2A:
		- 에이전트가 자연스러운 방식으로 공동작업할 수 있도록 하는 방법에 중점을 둠.
		- 에이전트가 도구가 아닌 에이전트로 소통할 수 있음.
		- [공식문서](https://a2a-protocol.org/latest/)에서는 애플리케이션이 도구에는 MCP를 사용하고 에이전트에는 A2A를 사용할 것을 권장함.

![[Pasted image 20260110123605.png]]

- A2A는 클라이언트-서버 원칙을 활용함
- 이 튜토리얼에서 예상되는 일반적인 A2A 흐름은 아래와 같음
	![[Pasted image 20260110123811.png]]
	1. `A2A 클라이언트`는 먼저 액세스 가능한 모든 `A2A 서버` `에이전트 카드`에서 검색을 실행하고 해당 정보를 활용하여 연결 클라이언트를 빌드함.
	2. 필요한 경우 A2A 클라이언트는 A2A 서버에 메시지를 전송하고 서버는 이를 완료해야 하는 작업으로 평가함. 푸시 알림 수신자 URL이 A2A 클라이언트에 구성되어 있고 A2A 서버에서 지원되는 경우 서버는 클라이언트의 수신 엔드포인트에 작업 진행 상태를 게시할 수도 있음.
	3. 작업이 완료되면 A2A 서버가 응답 아티팩트를 A2A 클라이언트에 전송함.

### 아키텍처 개요

![[Pasted image 20260110130256.png]]
- A2A 서버 역할을 하는 두 가지 서비스(CrewAI 에이전트 프레임워크로 지원되는 Burget 에이전트, LangGraph 에이전트 프레임워크로 지원되는 Pizza 에이전트)를 배포함.
- 사용자는 A2A 클라이언트 역할을 하는 에이전트 개발 키트(ADK) 프레임워크를 사용하여 실행되는 구매 컨시어지와만 직접 상호작용함.
- 각 에이전트는 자체 환경과 배포를 가짐.

# 워크숍 개발 설정 준비

- Google Console에서 프로젝트 생성.
- 무료 체험판 결제 계정 연결.
- Cloud Shell 연결.
	1. 셸이 계정으로 이미 인증되었는지 확인.
		```
		gcloud auth list
		```
	2. 다음과 같이 뜨면 정상.
		```
		Credentialed Accounts
		
		ACTIVE: *
		ACCOUNT: 2ju1230@gmail.com
		
		To set the active account, run:
		    $ gcloud config set account `ACCOUNT`
		```
- Cloud Shell에서 아래 레포지토리에 대해 git clone 수행.
	```
	git clone https://github.com/alphinside/purchasing-concierge-intro-a2a-codelab-starter.git purchasing-concierge-a2a
	```
- Cloud Shell에서 편집기(Editor) 열기.