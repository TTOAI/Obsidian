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
