## Clickhouse

- horizontal scaling은 자동
- replica(vertical scaling)는 수동으로 조절
---
## 카카오에서 clickhouse 쓰며 느낀 점

8:00 PM **ClickHouse 활용 최적화 여정**  
발표자: 최영록, Kakao 데이터 엔지니어

- OpenTelemetry + clickhouse
- 프로메테우스 + 그라파나
- clickhouse -> 하나의 스토리지, 한 뷰로 보고자

- Telemerty data를 clickhouse에 적재
- 단일 observility 플랫폼으로 제공

- 프록시 서버로 multi tenancy with proxy
	- dictionary 성능 이슈
		- 권한 정보를 가지고 role row policy 동기화해주는 role manager 제작
	- view 성능 이슈, distributed table with role
		- view가 최적화되어 있지 않음 -> distributed table로 변경

- ai native ops
	- ai 활용한 clickhouse 운영
	- 모니터링

- pivot to map, json type 적용 실패
	- json이 map보다 성능이 좋음
	- json 쓰면
		- field 늘어날수록 sub column 많아짐 이슈
		- hyperdx, grafana가 json 제대로 지원 안함
		- * 사용에 취약
	- 하지만 조회 성능이 좋음

- 프로메테우스 promql

- 안정성?
---

## ​5년의 고민, 5일의 구현: LLM과 함께한 데엔의 행복한 서비스 개발기
발표자: 박장호, Data Engineering Group

- 5년 기획, 5일 구현
- 왜 만듦?
	- 투자하려고. 낙폭 과대 구간을 공략하는 전략
	- 물리적 모니터링의 한계, 폭락 못 봄
- 왜 오년?
	- 오버 엔지(kafka, spark, hadoop)
	- 고민하다 오년
- mvp 원칙
	- 실시간 변동성 추적
	- 운영 부담 제로 -> serverless 기반 자가치유 시스템
- 5백만 데이터 -> 40 데이터 sparsity에서 버린 것
	- 시계열 저장 X -> 실시간 집중
	- kafka, replay로 데이터 재처리 X
- 이벤트브릿지 -> serverless -> sqs -> 텔레그램
- 오토 힐링, sqs 격리, graceful shutdown
---
- 폴리곤, 알파카 -> 웹소켓 기빈
