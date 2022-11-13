## 데이터 중개 플랫폼 Kafka 기반의 Data-Hub
- 기술track2_비스타홀2
- 15:10 ~

---

### Summary
- Casita, 11번가의 data hub
- **이벤트 기반의 데이터 중개 플랫폼**
- **CDC기반**의 실시간 데이터 동기화
- EDA 개발 가능
  - Exploratory Data Analysis: 탐색적 데이터 분석

### Data Hub
- 데이터 관리 솔루션
- SAP, Data Orchestration Hub
- 데이터 생산자, 소비자 연결, 관리, 대시보드화
- 데이터가 어디서 흘러오고 파악하는지, 이 문제는 어려움
- 서비스에서 생성되는 데이터 정의, 흐름 관리
- Casita
  - 이를 관리하고, 실제 서비스
  - MultiDB Multi Model로 데이터 동기화
  - 데이터 이동, 수집 관리

### Why?
- 왜 만들었는가?
- 11번가: MSA를 장기간에 걸쳐 전환중
- 이상적으로는 API로 데이터를 제공해야 함
  - 하지만 현실적으로, 유지보수, 비용이 많이 듦
- API, Http등의 통신, ETL
- 여러 방식으로 데이터를 주고 받다 보니, FLOW가 복잡해짐
- 스케줄 데이터가 아닌, 준실시간의 데이터 동기화 이슈가 존재
- 가능하다면 CDC로 실시간 동기화 필요
- CDC로 동기화 하면, 중복데이터가 많음
  - 그에 따른 데이터 통합을 통해, 중복, 비효율을 줄이기
  - 표준 플랫폼, 프로세스를 세우기
- 실시간 CDC를 위한 데이터 아키텍처 구성

### What?
- 표준화된 입수환경 제공
- Consumer Application, 손쉬운 파이프라인 설계
- Sync
- 복제를 위한 부하 최소화

### How?
- 10개의 모듈
- 서비스 db의 데이터 수집
  - kasita payload 형태로 가공

#### 데이터 추출 모듈
- `mongodb, mysql, oracle`에서 추출
- vender에서 제공하는 기능을 최대한 활용
- DBMS별로 출력은 제각각
  - 이를 표준 포맷으로 통일
- casita payload형태로 통합
- **CDC**가 주요 목표

#### Full Record Event
- **레코드 단위의 멱등성** 보장
#### Casity Repository
- 최적화
- 최대한 가볍게 구성하기
- 원본 dbms를 가볍게 하기 위해 `mongodb`형태로 **Cache Layer**를 사용(보조 Repository)
- 보조 repository에 데이터가 있을 경우 원본 db를 조회하지 않음
- 그에 따라 **full record**를 만들기 편해짐

#### Data Revision
- 보조 record가 rich하지 않을까?
  - **retention** 기능 추가
  - 적정 수준으로 맞추기 위해, **토픽별로 hit ratio를 조절 및 모니터링**
- 사용자 데이터의 특성
  - 일정 시간만 동작, 이후에 잘 사용되지 않기 때문
  - 캐시에 대한 retention 설정이 적합했던 이유

#### Data Injector
- 가공된 데이터를 target에 insert
- 모든 데이터는 **delete/insert**로 처리

#### Management & Governance
- UI For Apache Kafka 활용
- Grafana 대시보드 구성
- 처리량 모니터링, Governance와 관련된 부분은 직접 개발
- Kafka, Mongodb에 대한 항목 모니터링
- Casita 특화 내용들 같은 경우 직접 개발
  - grafana와 같은 외부 툴, 오픈소스로 custom한 상태 메트릭, 관리를 할 수 없기 때문
  - 수집 data list, consumer list, data map, ...

#### Casita Data Goverance
- 어느 db에서 오고 db로 가는지에 대해
- 어느 데이터가 입수되고 있는지
- **사용자가 직접 조회 및 활용 가능**
- Casita Payload, 데이터 파이프라인 map을 시각화 제공

### Where
- 11번가 주문 db 분리 구축
  - 이중화된 db에서 동기화
- **Event Driven Service**
  - 메인 db oracle
  - oracle의 변경 데이터가 많이 들어옴
  - db 조회 로직 제거도 가능했었음
- 서비스 db 분리
  - 배치 서비스를 위해 작업
  - 배치를 돌때 리소스가 너무 많이 듦
  - db도 여러가지
  - CDC에서 배치 처리는 어려움
  - casita는 이정보를 bulk 처리
    - full record로 가능하기 때문
- kafka event cluster에 의존하여, 데이터 처리 및 정합성 확보
- 운영 이슈
  - producing 되는 부분이 full record를 만들다보니
  - 순서가 뒤바뀌어 데이터가 누락되는 경우 등
    - 이런 부분은 fix