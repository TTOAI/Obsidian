                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            ㅊ## 1. 소프트웨어 테스팅이란?

### 소프트웨어 테스팅의 정의

- 소프트웨어 항목을 분석하여  기존 조건(existing) 과 요구 조건(required) 사이의 차이(= 결함)를 발견하고, 소프트웨어 항목의 기능과 품질을 평가하는 과정
- 단순 실행이 아닌, 결함 발견과 품질 수준 판단이 목적임

### 기본 용어 정리

**Error / Fault / Failure**
- 오류(Error)  
    → 개발자가 실수로 "+" 대신 "-" 기호를 사용하는 행위 자체
- 결함(Fault / Defect / Bug)  
    → 그 실수로 인해, 계산기 프로그램이 덧셈 대신 뺄셈을 수행하는 상태
- 실패(Failure)  
    → 사용자가 7과 5의 합을 계산하려 했지만, 프로그램이 2(7-5)를 출력한 상태
- Error(원인, 사람) → Fault(결함, 코드/산출물) → Failure(실패, 실행 결과)

**Testing / Debugging**
- 테스팅(Testing)  
    → 소프트웨어가 요구사항대로 정확히 작동하는지 확인하는 과정
- 디버깅(Debugging)  
    → 발견된 버그나 오류를 찾아서 수정하는 과정

**Test case / Test suite / Test script / Test procedure**
- 테스트 케이스(Test case)  
    → 특정 기능이 올바르게 작동하는지 확인하기 위한 테스트 조건의 집합
- 테스트 스위트(Test suite)  
    → 관련된 테스트 케이스들의 집합
- 테스트 스크립트(Test script)  
    → 테스트를 자동화하기 위해 사용되는 코드나 명령어의 집합
- 테스트 절차(Test procedure)  
    → 테스트를 실행하는 단계를 설명하는 문서

**SUT (Software / System Under Test)**
- 테스트 중인 소프트웨어 또는 시스템

**Actual result / Expected result / Test oracle**
- 실제 결과(Actual result)  
    → 테스트 실행 시 실제 출력이나 동작
- 예상 결과(Expected result)  
    → 요구사항에 따라 테스트에서 기대하는 결과
- 테스트 오라클(Test oracle)  
    → 실제 결과와 예상 결과를 비교하는 기준

**테스트 커버리지(Test coverage)**  
- 소프트웨어 코드 중 얼마나 많은 부분이 테스트로 검증되었는지를 나타내는 지표

 **Pre-condition / Post-condition**
- 전제 조건(Pre-condition)  
    → 테스트를 시작하기 전에 충족되어야 하는 조건
- 후속 조건(Post-condition)  
    → 테스트 실행 후 예상되는 상태나 조건

**V&V (Verification & Validation)**
- 검증(Verification)  
    → 제품이 설계 문서와 일치하는지 확인하는 과정
- 확인(Validation)  
    → 최종 제품이 사용자의 요구와 목적에 부합하는지 확인하는 과정

### 테스트 분류

**테스트 설계 방법에 따른 분류**
- 화이트박스 테스트(White-box Testing)
- 그레이박스 테스트(Gray-box Testing)
- 블랙박스 테스트(Black-box Testing)

**테스트 단계에 따른 분류**
- 단위 테스트(Unit Testing)
	- 소프트웨어를 구성하는 가장 기본적인 단위(예: 함수, function)를 시험
	- 시험 대상 외에, 이와 연결되어 입력/출력을 주고받는 대상은 스텁(Stub) 으로 처리할 수 있음
- 통합 테스트(Integration Testing)
	- 소프트웨어를 구성하기 위한 기본 단위들을 통합할 때 필요한 인터페이스(interface) 를 시험
- 시스템 테스트(System Testing)
	- 소프트웨어만이 아니라 **하드웨어(HW)** 까지 통합된 전체 시스템
- 인수 테스트(Acceptance Testing)
	- 최초 시스템 요구사항을 기준으로 요구사항이 제대로 반영되어 구현되었는지 시험

**테스트 목적에 따른 분류**
- 기능 테스트(Functional Testing)
- 비기능 테스트(Non-functional Testing)

**소프트웨어 테스트 유형 구조도**
![[Pasted image 20260122164726.png]]

## 2. 소프트웨어 개발 프로세스와 테스팅

- IEC 61508을 기반으로 자동차, 항공, 철도 등 각 Domain별 맞춤 표준이 발전
- 자동차 산업: ISO 26262
- 항공 산업: DO-178C
- 로봇: ISO13482

## 3. 코드 기반 정적/동적 시험 개요

### 코드 기반 시험 요소 기술

![[Pasted image 20260122171929.png]]
-  기능적으로 정적 기술(코드 실행 X)과 동적 기술(코드 실행 O)로 구분할 수 있음

### 코드 정적 분석 기술

**실행 시간 오류 검출(Semantic analysis)**
- 실행 의미 분석
- 런타임 오류 가능성 검출
- 산술 오류
	- 복잡한 산술 연산
	- 타입 변화 관련 오류
- 메모리 오류
	- 잘못된 메모리 접근
	- 메모리 누수 (Memory leak)
	- NULL 참조 (NULL dereference)
	- 잘못된 메모리 해제
- 배열 경계 오류
	- buffer overflow
	- buffer underflow
	- 보안 취약점 관련 오류

**코딩 규칙 검사(Syntactic analysis)**
- 코딩 표준 기반 검사
	- 산업별(자동차, 국방, 철도, 원자력, 조선·해양 등) 코딩 표준(MISRA, IEC, DAPA 등)을 기준으로 검사함
- 코딩 규칙 위반 탐지

### 코드 동적 분석 기술

**테스트 케이스 자동 생성**
- 테스트 케이스 자동 생성
	- Partition-based
	- Heuristic Search
	- Symbolic Execution
- 테스트 결과 관리
	- 성공 (Pass)
	- 실패 (Fail)
	- 오류 (Error)
- 커버되지 않은 소스코드 확인
	- 실행된 코드
	- 실행되지 않은 코드 표시

**테스트 코드 자동 생성**
- 테스트 코드 자동 생성
	- Test driver
	- Test stub
- 스텁을 이용한 테스트
	- 원본 함수 대신 스텁(Stub)으로 원하는 값 반환
		- 외부 시스템(DB 등) 없이도 테스트 가능
		- 스텁 기능으로 반환값을 설정
- 정의가 없는 함수의 스텁 자동 생성
	- 라이브러리 함수 등
	- 소스가 없고 링크가 필요한 경우
	- .lib 파일이 없어도 스텁 자동 생성
	- 원하는 값 반환 가능
- 함수 타입 / 스텁 타입
	- 함수 타입
		- 정의가 없는 함수
		- 정의가 있는 함수
	- 스텁 타입
		- 사용자 스텁 (User stub)
		- 빌드 스텁 (Build stub, 자동 생성)

**자동 실행 & 커버리지**
- 테스트 자동 실행
- 테스트 커버리지 자동 측정

##            4. 테스트 커버리지

- 구조적 커버리지 -> 단위테스트
	- 테스트가 소수 코드 구조를 최소 한 번 실행했는지 측정
	- Statement Coverage: 모든 문장이 실행
	- Branch Coverage: 모든 분기가 실행
	- MC/DC: 각 조건이 독립적으로 결과에 영향을 미치도록 실행
- 요구사항 커버리지 -> 통합테스트
	- 모듈, 컴포넌트, 또는 레이어 간의 의존성, 호출 관계, 데이터 흐름이 실행되는지 측정
	- Function Coverage: 함수가 최소 한 번 실행
	- Call Coverage: 모든 함수 호출이 최소 한 번 실행

 