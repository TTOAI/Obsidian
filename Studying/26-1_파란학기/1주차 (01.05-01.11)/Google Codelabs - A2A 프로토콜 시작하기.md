
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

# 2. 워크숍 개발 설정 준비

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

- **purchasing-concierge-a2a** 디렉터리를 기본 작업 디렉토리로 설정.

- 다음 명령어를 실행하여 **.venv** 디렉터리의 가상 환경에 필요한 종속 항목을 설치
	```
	uv sync --frozen
	```

- 아래의 명령어를 통해 필요한 API를 사용 설정.
```
gcloud services enable aiplatform.googleapis.com \
                       run.googleapis.com \
                       cloudbuild.googleapis.com \
                       cloudresourcemanager.googleapis.com
```

- 명령어가 성공적으로 실행되면 아래와 유사한 메시지가 표시됨.
```
Operation "operations/..." finished successfully.
```

# 3. 버거 판매자 에이전트 - A2A 서버 배포

![[Pasted image 20260110140105.png]]
- 빨간색 상자로 표시된 두 개의 원격 판매자 에이전트 중 햄버거 에이전트 배포.
- 햄버거 에이전트는 CrewAI 에이전트 프레임워크로 구동됨.

- 다음 명령어를 실행하여 배포.
	```
	gcloud run deploy burger-agent \
	    --source remote_seller_agents/burger_agent \
	    --port=8080 \
	    --allow-unauthenticated \
	    --min 1 \
	    --region us-central1 \
	    --update-env-vars GOOGLE_CLOUD_LOCATION=us-central1 \
	    --update-env-vars GOOGLE_CLOUD_PROJECT=a2a-agent-engine-483904
	```

- 새 브라우저 탭을 열고 브라우저를 통해 배포된 버거 에이전트 서비스의 `https://burger-agent-732259039336.us-central1.run.app/.well-known/agent.json` 경로로 이동.
	- 배포된 A2A 서버 에이전트 카드에 액세스하는 URL.
- 아래와 같은 응답이 표시됨.
	![[Pasted image 20260110141141.png]]
- `url` 값이 여전히 `http://0.0.0.0:8080/`로 설정되어 있음.
- 이 `url` 값은 A2A 클라이언트가 외부에서 메시지를 전송하는 데 필요한 기본 정보임.
- 환경변수 `HOST_OVERRIDE`를 추가하여 이 값을 버거 에이전트 서비스의 URL로 업데이트해야 함.

- Cloud 콘솔 상단의 검색창에서 Cloud Run을 검색함.
- 이전에 배포된 burger-agent Cloud Run 서비스를 클릭.
- 버거 서비스 URL을 복사한 다음 새 버전 수정 및 배포를 클릭.
- 변수 및 보안 비밀 섹션을 클릭.
- 변수 추가를 클릭하고 `HOST_OVERRIDE` 값을 서비스 URL(`https://burger-agent-732259039336.us-central1.run.app`)로 설정.
	![[Pasted image 20260110142115.png]]
- - 새 브라우저 탭을 열고 `https://burger-agent-732259039336.us-central1.run.app/.well-known/agent.json` 경로로 이동하여 url 값 확인.
	![[Pasted image 20260110142227.png]]

# 4. 피자 판매자 에이전트 배포 - A2A 서버

- 아래의 명령어를 수행하여 배포.
	```
	gcloud run deploy pizza-agent \
		--source remote_seller_agents/pizza_agent \
		--port=8080 \
		--allow-unauthenticated \
		--min 1 \
		--region us-central1 \
		--update-env-vars GOOGLE_CLOUD_LOCATION=us-central1 \
		--update-env-vars GOOGLE_CLOUD_PROJECT=a2a-agent-engine-483904
	```

- 버거 에이전트와 동일하게 url 값 수정.

# 5. 구매 컨시어지 - A2A 클라이언트를 Agent Engine에 배포

- 아래의 명령어로 Cloud Storage에 스테이징 스토리지를 만듦.
	```
	gcloud storage buckets create gs://purchasing-concierge-a2a-agent-engine-483904 --location=us-central1
	```

- `.env.example` 파일 복사 후 알맞은 값으로 수정.
```
cp .env.example .env
```

- 에이전트 엔진 배포.
```
uv run deploy_to_agent_engine.py
```

- 콘솔에서 `Agent Engine` 리소스 이름 확인 후 `.env` 파일의 `AGENT_ENGINE_RESOURCE_NAME` 업데이트

