postgreSql

pg_cron 설정하기
1. postgresql.conf 파일에서 library, cron 작업할 데이터베이스 지정해주기
2. pg_cron library 다운 받기

-내부 아키텍처, TimescaleDb 확장에서 사용
1. _timescaledb_cache : 캐싱
2. _timescaledb_catalog : 카탈로그
3. _timescaledb_internal : 내부 함수
