# Lakehouse 아키텍처 계층 정리
---

```
처리 엔진 (Execution / Processing Engine)
	- Hive (오픈소스, 전통 SQL-on-Hadoop 엔진)
	- Trino (오픈소스, 고속 분산 SQL 엔진)
	- Spark (오픈소스, 범용 분산 처리 엔진: ETL/SQL/ML)
	- Flink (오픈소스, 실시간 스트리밍 엔진)
	- Athena (AWS 서비스, Trino 계열 서버리스 SQL 엔진)
        ↓
테이블 엔진 (Table Engine / Transaction Layer)
	- Iceberg (오픈소스, 테이블 포맷 + 트랜잭션 레이어)
        ↓
카탈로그 (Catalog / Metastore)
	- Hive Metastore (오픈소스, 테이블 메타데이터 저장소)
	- AWS Glue Data Catalog (AWS 서비스, 관리형 Hive Metastore)
	- Nessie (오픈소스, Iceberg 전용 Git-like 카탈로그)
        ↓
저장소 (Storage Layer)
	- S3 (AWS 서비스, 객체 스토리지 / 데이터 레이크 표준)
	- HDFS (오픈소스, 온프레미스 분산 파일 시스템)
```
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
	- MPP DW는 Lakehouse의 downstream BI serving layer로 사용됨
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
---

# 역할 기반 데이터 시스템 계층 정리
---

## OLTP — System of Record (운영 시스템)

- 정체성
	- 서비스의 원본 데이터
	- 사용자 요청 처리
	- 트랜잭션, 정합성, 안정성 최우선

- 담당 역할
	- 주문, 결제, 회원, 재고, 배송 상태 관리
	- CRUD, 비즈니스 로직 실행
	- ms 단위 응답

- 대표 기술
	- MySQL, PostgreSQL, Aurora
	- DynamoDB, MongoDB
	- 마이크로서비스 DB들

- 특징
	- 강한 트랜잭션 (ACID)
	- 서비스 장애와 직결
	- 분석/대용량 스캔에 부적합

- Lakehouse와의 관계
	- CDC(Debezium, DMS)나 이벤트(Kafka)로 데이터 전달
	- 분석용 복제본을 만드는 데이터 공급원
```
OLTP
	↓
(CDC / Event)
	↓
Lakehouse
```
---

## Lakehouse — System of Insight (분석·관리·학습 플랫폼)


- 정체성
	- 회사의 중앙 데이터 플랫폼
	- 데이터를 “운영”하지 않고, 이해·관리·학습하는 시스템

- 담당 역할
	- 원천 데이터 축적
	- 히스토리 관리
	- DW 구축
	- BI 분석
	- ML 학습 데이터 관리
	- Feature 생성
	- 실험 재현성, backfill, 품질 관리

- 대표 구조
	- Storage: S3 / HDFS
	- Table engine: Iceberg
	- Processing: Spark / Flink / Athena / Trino
	- Catalog: Glue / Hive Metastore / Nessie

- 특징
	- 대용량, 저비용
	- 배치 + 스트리밍 통합
	- ACID 테이블(Iceberg)로 데이터 관리
	- 분석/ML에 최적
	- Object Storage 중심

- 하지 않는 것
	- 사용자 API 직접 처리
	- ms 단위 실시간 서빙
	- 서비스 트랜잭션

- 전체 위치
```
System of Record(OLTP)
	↓
Lakehouse
	↓
BI / ML / Feature
	↓
Serving Store
```
---

## Serving store — System of Action (결과 제공 시스템)

- 정체성
	- 서비스(API)가 직접 조회하는 전용 저장소
	- Lakehouse/ML 결과를 실시간으로 제공

- 담당 역할
	- 추천 결과 제공
	- 검색 인덱스 제공
	- 실시간 feature 제공
	- 집계 결과 캐싱
	- 랭킹/후보군 서빙

- 대표 기술
	- Redis, Aerospike, DynamoDB (KV / Cache)
	- Elasticsearch / OpenSearch (Search / Filter)
	- Vector DB (Milvus, Pinecone, FAISS)
	- Online Feature Store (Feast online, Tecton)

- 특징
	- ms 단위 응답
	- 읽기 중심
	- 특정 쿼리 패턴 최적화
	- 서비스 트래픽 감당

- Lakehouse와의 관계
	- Lakehouse에서 만든 결과를 export
	- Serving 로그는 다시 Lakehouse로 환류
```
Lakehouse → (Batch / Streaming export) → Serving Store → API → User
Serving logs → Kafka → Lakehouse
```
---

## (참고) 전형적인 현대 구조

```
[ System of Record ]
OLTP / Transaction systems
        ↓
[ System of Insight ]
Lakehouse (S3 + Iceberg + Spark/Flink)
        ↓
[ System of Action ]
Serving Store (Redis / Elastic / Vector DB)
        ↓
API / Service / User
```
---

