
# 1️⃣ Agent 간 통신은 어떻게 구현되는가?

## 결론부터

> **v0.1에서는 서버를 띄우지 않는다.  
> 하나의 프로세스 안에서 “객체 + 함수 호출”로 구현한다.**

이건 타협이 아니라 **의도적인 설계**야.

---

## 왜 서버(URL) 기반이 아닌가?

### ❌ 서버 기반 (HTTP / gRPC / A2A 서버)

- 비동기
    
- 네트워크
    
- 직렬화/역직렬화
    
- 장애/타임아웃
    
- 로그 분산
    

👉 **v0.1의 실험 질문을 흐린다**

> “왜 실패했는지”가  
> 네트워크/타이밍/재시도/스트리밍에 묻힘

---

## v0.1에서의 올바른 구현 방식

### ✅ 단일 프로세스 + 명시적 호출

```text
main.py (single process)
 ├─ MasterAgent
 │    ├─ call(CodeAgent)
 │    ├─ call(ExecutionLayer)
 │    ├─ call(TestAgent)
 │    ├─ call(DecisionLayer)
 │    └─ call(FinalAgent)
```

### 코드 수준에서는 이렇게 된다

```python
class MasterAgent:
    def run_iteration(self):
        code_result = self.code_agent.run(mcp)
        exec_result = self.executor.run()
        test_result = self.test_agent.run(exec_result)
        decision = self.decision_layer.decide(test_result)
```

### 핵심 포인트

- **Agent = 객체**
    
- **A2A = 함수 호출**
    
- **통신 = MCP 스키마로 제한된 인자 전달**
    

👉 “통신 프로토콜”이 아니라  
👉 **“인지 경계(contract)”가 핵심**

---

## 그럼 A2A는 없는 건가?

아니다. **논리적 A2A**는 있다.

> A2A =  
> “Agent A가 Agent B의 내부 상태를 모르고  
> 오직 정의된 입력/출력으로만 상호작용한다”

이건 **네트워크가 아니라 인터페이스 문제**다.

---

# 2️⃣ 이 프로젝트에서 MCP는 어떻게 쓰이고, Tool은 뭐가 있나?

여기서 중요한 오해 하나부터 짚자.

> ❌ MCP = “외부 API 호출용”만 아님  
> ⭕ MCP = “LLM이 접근 가능한 능력의 경계”

---

## Code2E v0.1에서 MCP의 위치

```text
LLM
 ↑
Agent (Code / Test / Final)
 ↑
MCP (입력/출력 계약)
```

MCP는 **Agent 내부에서 LLM의 행동 반경을 고정**하는 역할이다.

---

## 그럼 “Tool”은 뭐냐?

> Tool = **LLM이 직접 실행하지 못하고,  
> 반드시 시스템을 통해서만 수행해야 하는 행동**

---

## v0.1에서 구현 가능한 Tool 목록 (현실적 + 의미 있음)

### ✅ Code Agent용 Tool

> Code Agent는 “파일 쓰기”만 가능해야 한다.

```python
Tool: write_file(path, content)
Tool: update_file(path, diff)
```

❌ 금지

- 테스트 실행
    
- 로그 접근
    
- 서버 실행
    

👉 Code Agent는 **“코드 생성기”로만 고정**

---

### ✅ Test Agent용 Tool

> Test Agent는 “테스트 코드 작성 + 결과 해석”만

```python
Tool: write_test_file(path, content)
Tool: parse_test_result(raw_output)
```

❌ 금지

- 애플리케이션 코드 접근
    
- 구현 의도 추측
    

---

### ✅ Final Agent용 Tool

```python
Tool: collect_commits()
Tool: summarize_logs()
Tool: generate_report()
```

Final Agent는 **쓰기 권한 없음**, 정리만.

---

### ❌ MCP로 안 하는 것 (중요)

- pytest 실행
    
- FastAPI 실행
    
- Docker 실행
    

이건 **Execution Layer의 고유 권한**이다.

> LLM은 실행 결과를 “보는 것”만 가능  
> 실행은 절대 불가

---

# 3️⃣ Code2E v0.1 전체 아키텍처 & 실제 구현 구조

이제 **추상 말고, 바로 코드로 옮길 수 있게** 정리한다.

---

## 3.1 디렉토리 구조 (권장)

```text
code2e/
├─ main.py                 # entry point
├─ master/
│   └─ master_agent.py
├─ agents/
│   ├─ code_agent.py
│   ├─ test_agent.py
│   └─ final_agent.py
├─ execution/
│   └─ executor.py
├─ decision/
│   └─ decision_layer.py
├─ protocol/
│   ├─ mcp.py              # MCP schema
│   └─ a2a.py              # agent call wrapper
├─ workspace/
│   ├─ app/                # FastAPI code
│   └─ tests/              # pytest code
├─ logs/
└─ reports/
```

---

## 3.2 Master Agent (핵심 컨트롤러)

```python
class MasterAgent:
    def run(self):
        for iteration in range(MAX_ITER):
            mcp = self.build_code_mcp()
            code_out = call_agent(self.code_agent, mcp)

            exec_out = self.executor.run()

            test_mcp = self.build_test_mcp(exec_out)
            test_out = call_agent(self.test_agent, test_mcp)

            decision = self.decision_layer.decide(test_out)

            self.commit_if_needed()

            if decision != CONTINUE:
                break

        self.final_agent.generate_report()
```

👉 **Master만 상태를 가진다**  
👉 Agent는 Stateless

---

## 3.3 Execution Layer (Truth)

```python
class Executor:
    def run(self):
        run_server()
        test_output = run_pytest()
        return {
            "server_log": ...,
            "test_output": ...,
            "exit_code": ...
        }
```

- subprocess
    
- Docker (선택)
    
- stdout/stderr raw capture
    

👉 **LLM이 해석할 뿐, 판단하지 않는다**

---

## 3.4 Decision Layer (Rule-based)

```python
def decide(test_result):
    if repeated_failure > N:
        return STOP
    if pass_rate_decreasing:
        return STOP
    return CONTINUE
```

- LLM ❌
    
- 규칙만 ⭕
    

---

## 3.5 Final Agent

```python
class FinalAgent:
    def generate_report(self):
        # commit history
        # failure timeline
        # requirement mapping
```

---

# 4️⃣ 이 구조를 한 문장으로 요약하면

이 문장은 **구현 철학**이다.

> **“v0.1의 Code2E는 분산 시스템이 아니라,  
> 통제된 실험 장치이며,  
> 에이전트는 서비스가 아니라 역할이다.”**
