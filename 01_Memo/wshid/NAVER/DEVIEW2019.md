# DEVIEW 2019
- 19.10.30
- 코엑스 그랜드볼룸
---

### 2일차

## 네이버 로그를 지탱하는 힘 (DataStore 로그 저장소)
### 로그 저장 이야기
- Cuve에 대한 내용
- 생산자(producer)와 소비자(consumer)의 분리
  - DataStore 로그 저장소(Cuve) 사용
- Cuve 처리 Flow
  - ![image](https://media.oss.navercorp.com/user/13278/files/6abfe280-fa87-11e9-9567-662aebf159ca)
  - Hbase의 데이터와 hive MetaStore와 연동되어, Hive상에서 테이블 형태로 데이터 조회가 가능하다
- **At-least-once Delivery**
  - ![image](https://media.oss.navercorp.com/user/13278/files/d0ac6a00-fa87-11e9-99af-e6db77751d29)
  - 최소 한번 이상 저장
  - 데이터 저장 이후 ACK를 보내는 방식
  - 유실은 없으나 **중복**존재
  - 중복 해결 방법
    - 외부 저장소의 고유 식별자를 이용한 **idempotent write**
    - 고유식별자를 바탕으로 **Hbase RowKey**를 생성한다
- 고유 식별자를 선정하는 방법
  - 1. Hash : 내용이 동일하면, 결국 해시값이 같아지므로 사용 불가
  - 2. 시퀀스 값 발급 : 해당 서버가 병목 지점이 될 수 있다. 사용 불가
  - 3. UUID의 사용 : OSF(개방 소프트웨어 재단)에서 분산 컴퓨팅 환경(DCE)의 일부로 표준화
    - v1 ~ v5의 다섯가지 버전 존재
    - Cuve에서는 v1의 변형을 사용함
- UUID v1의 변형
  - v1 : 네트워크 카드의 **MAC 주소**와 **시간**을 기반으로 유니크한 ID를 생성
  - v1' : 네트워크 카드의 **로그 생성 시간(ns)**와 **Sender 서버 IP**를 기준으로 유니크한 ID 생성
    - 추가적으로 **Variant 값** 운용
      - 로그 파일 라인 위치 사용(v1의 단점 개선)
    - v1' unique key = **로그 생성 시간** + **Sender 서버 IP** + **로그 파일 라인 위치**
- RowKey 디자인 : RGM의 사용
  - `RowKey = R + G + M + unique_key`
- hot spot 문제
  - unique에서 `로그 생성 시간`이라는 시간 값이 존재하므로, **Hot Spot**이 존재한다
  - **Salt**값을 주어 해결
  - `RowKey = Salt + R + G + M + unique_key`
- Hbase의 저장 공간
  - **Table** / **Region** / **CF** 단위로 구분하여 저장
    - **Table**을 특정 범위로 쪼개어 **Region**에 나누어 저장
    - CF단위로 파일을 생성하므로, 데이터 읽는 패턴이 동일할 경우, 동일한 **CF**에 넣어 주어야 함
- 저장 공간의 구분 규칙(3)
  - 원본 저장 CF(원본 로그용) : 원본 데이터가 필요할경우 해당 CF만 이용
  - 필드 저장 CF(검색 쿼리용) : 파싱된 필드만 저장하는 곳, 특정 필드만 조회할 경우 해당 CF만 이용
  - 보안정보 CF : 유효 기간이 지난 데이터 삭제시, 해당 CF에서만 데이터 삭제
- Cuve Catalog
  - 사용자 역할 관리, 데이터 접근 제어, 데이터 필드 정보 관리
- Ticket
  - 리소스 관리 및 접근제어
  - 티켓 별로 사용량 제어가 가능함
  - 마이그레이션과 같은 특정 큰 규모의 작업일 경우, 해당 티켓의 용량만 늘려주는 식으로 활용 가능
  - Owner가 티켓을 사용하여 권한 관리가 가능
- DataStore
  - RGM 정보 및 ticket정보가 있다면, 해당 데이터를 사용할 수 있게 됨

### 로그 활용 이야기
- ![image](https://media.oss.navercorp.com/user/13278/files/444f7680-fa8a-11e9-914a-083389a3f5af)
- 로그의 오픈화
  - 직접 Hue를 사용할 수 있도록 함
  - 로그에 대한 접근 권한 제어가 필요
  - 이를 사용하기 위해 DataStore(Cuve)를 도입
- 로그에 대한 접근 제어는 가능하나, 비개발직군에 대한 진입 장벽이 높음
  - SQL 도입(Hive)
  - zeppelin, hue를 제공하여 분석 결과를 확인할 수 있도록 함
  - python과 spark도 지원
- raw 로그로부터 비즈니스적인 분석 결과를 얻기 위한 쿼리 등의 복잡도가 높다
  - 로그 분석 의뢰건을 전수 조사, 분석 패턴을 파악
    - 기존에도 로그 분석 의뢰를 **태그**를 붙여 관리했기 때문에, 전수 조사 기간은 오래 걸리지 않음
- 로그 분석 의뢰 패턴(5)
  - 노출수/클릭수/CTR
  - QC(Query Count)/UQC
  - 노출문서
  - 유입경로
  - 연령/성별
- Hue의 Template 사용
  - ![image](https://media.oss.navercorp.com/user/13278/files/0141d300-fa8b-11e9-8f2b-a744d51fd700)
  - 로그 분석 의뢰 패턴에 맞게 **Hue**에 존재하는 Template을 구성한다.
- SQL-Archive
  - 패턴으로는 해결되지 않는 상세한 로그 분석 의뢰
  - oss에 이슈를 등록하여 처리
  - `oss의 이슈 번호 = hue의 이슈번호`
    - ![image](https://media.oss.navercorp.com/user/13278/files/5aaa0200-fa8b-11e9-8f36-9416bfa6fabc)
    - ![image](https://media.oss.navercorp.com/user/13278/files/6695c400-fa8b-11e9-8390-87986b1bd84f)
- DataStore(Cuve) 인증
  - RGM별 티켓 access 권한 부여
  - RGM
    - `DB = R + G`
      - ex) `korea_naver_log`, `korea_map`, `korea_kin`, ...
    - `M = Table`
      - ex) `stream_log`, `s_log`, `sas`, ...
    - ex) `korea_naver_log.stream_log, ...`
  - DataStore에 로그가 쌓이면, 1:1로 매핑되는 테이블이 생성 됨
    - RGM별 사용자 접근 제어가 가능하며, `Hbase : Hive = 1:1`로 되는 테이블 사용이 가능해짐
- 지표 조회의 복잡도 증가 => 정제 테이블 활용
  - 정제 테이블에서는 `ORC Format`을 사용
    - 하이브의 처리속도를 높이기 위해 개발됨
    - `Columnar Format`
- 로그 파티셔닝
  - 기간별 로그 파티션(`PARTITIONED BY ...`)
- Buckets
  - `CLUSTERED BY {column} INTO {bucket_num} BUCKETS;`
    - **컬럼**을 해시 기준으로 지정된 **버킷 개수의 파일로 분리**하여 저장
    - `버킷 개수 = 파일 수`
  - `join` 키로 `bucket`을 생성해두면, 파일 전체를 **Full Scan**하지 않음
  - `Impala`에는 따로 지원하지 않는 옵션
    - https://docs.cloudera.com/documentation/enterprise/5-15-x/topics/impala_create_table.html
  - 단, 특정 bucket에 집중될 경우
    - 해당 bucket(file)에 hot spotting이 일어난다.
      - 조회 속도 및 메모리 부하 발생
    - shuffle 단계에서 **OOM**발생 가능성 존재
    - 메모리를 늘려주거나, 메모리 의존도를 낮춰준다.(DISK I/O tradeoff)
- External Table의 장점
  - 안전함 : `DROP TABLE`을 하여도 파일 존재
  - HDFS가 아니어도 Table화 가능
    - `Hbase, Cassandra, Azure Table, ...`
  - 데이터 소스 변경 용이
    - `SET LOCATION ...`을 통해 데이터 소스 변경 가능
  - 단, `Hive`에서 제공하는 `transaction`이나, `LLAP(Live Long And Process)`사용시 제약 존재
- Hive UDF
  - 검색 로그에 특화된 UDF를 만들어 운영

## Pinpoint는 어떻게 observability를 강화했는가

## ms 단위의 Serverless World에서 Docker의 성능 한계 극복하기

## 대용량 멀티테넌트 시큐어 하둡 클러스터를 시행착오 없이 만들기

## 쿠팡 추천 시스템 2년간의 변천사 (상품추천에서 실시간 개인화로)

## 스케일아웃없이 순간 급증하는 주문 처리하기 (Microservice with Kafka)
