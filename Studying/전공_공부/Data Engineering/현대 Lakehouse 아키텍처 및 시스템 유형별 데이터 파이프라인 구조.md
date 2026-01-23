# Lakehouse 아키텍처 계층 정리
---

**처리 엔진 (Execution / Processing Engine)**
- Hive (오픈소스, 전통 SQL-on-Hadoop 엔진)
- Trino (오픈소스, 고속 분산 SQL 엔진)
- Spark (오픈소스, 범용 분산 처리 엔진: ETL/SQL/ML)
- Flink (오픈소스, 실시간 스트리밍 엔진)
- Athena (AWS 서비스, Trino 계열 서버리스 SQL 엔진)
        ↓
**테이블 엔진 (Table Engine / Transaction Layer)**
- Iceberg (오픈소스, 테이블 포맷 + 트랜잭션 레이어)
        ↓
**카탈로그 (Catalog / Metastore)**
- Hive Metastore (오픈소스, 테이블 메타데이터 저장소)
- AWS Glue Data Catalog (AWS 서비스, 관리형 Hive Metastore)
- Nessie (오픈소스, Iceberg 전용 Git-like 카탈로그)
        ↓
**저장소 (Storage Layer)**
- S3 (AWS 서비스, 객체 스토리지 / 데이터 레이크 표준)
- HDFS (오픈소스, 온프레미스 분산 파일 시스템)
---
---

# (쿠팡을 예시로 한) 시스템 유형별 데이터 파이프라인
---

## 핵심 서비스 & 트랜잭션 시스템

- 주문 / 결제 / 회원 / 재고 / 배송

- 특징
	- 사용자 요청 처리
	- 돈/재고/상태 관리
	- ms 단위 응답, 강한 트랜잭션
	- 안정성과 트랜잭션이 최우선

- 표준 구조
```
App / Web
   ↓
API / Microservices
   ↓
OLTP DB (MySQL, Aurora, DynamoDB)
```

- 분석을 위한 Lakehouse와의 연계 과정
```
OLTP DB
   ↓ CDC (Debezium / DMS)
Kafka
   ↓
Flink / Spark
   ↓
Lakehouse
```
---

## 로그 / 이벤트 / 행동 데이터 시스템

- 클릭 / 검색 / 노출 / 장바구니 / 추천 반응

- 정체성
	- 초대량
	- 실시간 발생
	- 모델/분석의 핵심 재료

- 표준 구조
```
Client / Service
    ↓
Event SDK
    ↓
Kafka / Kinesis
    ↓
Flink (streaming ETL)
    ↓
Lakehouse (Iceberg on S3)
```
---

## 배치 DW / 중앙 데이터 플랫폼

- 매출 / 전환율 / 물류 KPI / 운영 지표

- 정체성
	- 전사 데이터 통합
	- 히스토리 관리
	- DW 모델링

- 표준 구조
```
[Upstream]
OLTP / Logs / Events
        ↓
     Lakehouse
 (S3 + Iceberg + Spark/Flink)

[Downstream]
        ↓
Snowflake / BigQuery (MPP DW)
        ↓
BI / KPI / 리포트
```
---

## 추천 시스템

- 학습 파이프라인
```
Logs / Orders / Products
            ↓
        Lakehouse
            ↓
      Spark (feature ETL)
            ↓
      Training / Embedding
```

- 서빙 파이프라인
```
Model output / embedding
        ↓
Redis / Vector DB / Feature Store
        ↓
Recommendation API
        ↓
User
```

- 환류
```
Recommendation API
   ↓
Click / Conversion log
   ↓
Kafka
   ↓
Lakehouse
```
---

## 검색 시스템

- 인덱스 구축
```
Products / Reviews / Embeddings
              ↓
          Lakehouse
              ↓
        Spark batch
              ↓
   Elasticsearch / Vector DB
```

- 실시간 검색 API
```
User
   ↓
Search API
   ↓
Elastic / Vector DB
   ↓
User
```

- 환류
```
Search API
   ↓
logs
   ↓
Kafka
   ↓
Lakehouse
```
---

## 사기 탐지 / 이상 탐지 / 실시간 분석

- 실시간 판단
```
Payment Event
   ↓
Kafka
   ↓
Flink
   ↓
Realtime model / rule
   ↓
Block / Allow
```

- 학습/검증
```
결제 로그
   ↓
Lakehouse
   ↓
Spark
  ↓
모델 재학습
```
---

## 물류 / 수요 예측 / 운영 최적화

- 정체성
	- 예측
	- 시뮬레이션
	- 최적화

- 표준 구조
```
Orders / Inventory / Delivery logs
                 ↓
              Lakehouse
                 ↓
         Spark / ML / Optimization
                 ↓
        운영 시스템에 반영
```
---

## 마케팅 / 실험 / 성장 분석

- 표준 구조
```
Campaign logs / user behavior
                ↓
             Lakehouse
                ↓
      Feature / Cohort / A/B
                ↓
      Snowflake / BigQuery
                ↓
         Dashboard / Decision
```
---
