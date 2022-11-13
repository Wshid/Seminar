# COVID19시대 빠른 사용자 행동 패턴 파악
- link : https://tv.naver.com/v/16970008

---

## 1. COVID-19가 가져온 변화[CHANGE]
- 마스크 대란
- 테이블 주문, QR 결제
- 효과적인 마케팅

## 2. 필요한 실시간 지표 시스템의 방향[NEED]
- 단순 window의 sum값이 아닌, 여러 demension/metric 조합으로 사용
- 실시간 유입 현황 등의 다양한 정보 제공
- BI도구를 사용하여 FE를 보충하여 개발

## 3. 실시간 데이터 처리 파트[SPEED]
- ClickHouse를 사용하고, OLAP으로 해당 데이터를 조회
- Flink를 사용한 시스템
  - abusing 탐지, 머신러닝, 디버깅등에 활용
  - back pressure monitoring이 초기버전보다 많이 개선되었다고 함
### Flink Window and Trigger
  - Tumbling, Sliding, Session Window
    - Sliding은 Tumbling과 유사하나, Overlap되는 부분이 존재
    - Session은 말 그대로 Session 단위
  - **Custom Trigger**
    - 특정한 시간, 이벤트 등을 정의하여 Window 구성이 가능함
    - 사용자 분석을 다양화 시킬 수 있음
    - 어뷰징등을 탐지할 수 있음
    - 시퀀스, 개별 이벤트 관리를 할때는 Flink외의 다른 시스템이 필요함
### late data
  - 해외 IDC, flink상 딜레이 등을 극복하려면
  - 일정 threshold를 두어, batch시에 처리하거나 알람을 받도록 처리
### 선택적 집계를 위한 API 구성 방법
  - URL 단위뿐만 아니라, 서비스 단위도 조회가 가능해야 함
  - Flink가 Yarn에서 실행, 계속 IP가 변경됨
    - Flink가 Client가 되어야 함
  - Client -> Node -> Flink의 처리 구조(Websocket, Node.js, IPC)
    - Zookeeper emsemble로 구현해도 유사하게 가능

## 4. 실시간 데이터 조회 파트[Optimize]
### ClickHouse
  - 매우 빠른 OLAP DBMS
  - 적은 수의 노드로 성능이 좋음
  - HW Efficiency가 높음
  - Hadoop 과의 연동은 3rd party app을 이용해야 함
- 최적화 관련
  - Array 기반의 데이터
    - Native data type으로 array를 지원
  - Array Join : Col -> Row
    - Row를 축소한 뒤, 추후 다시 Row 형태로 복구하여 사용이 가능함
    - Pv 계산등에 활용
  - Click Array의 활용
    - Row수를 줄이는 방법, 데이터 압축이 가능함
### Table Engine, AggregatingMergeTree
  - merge 수행 함수 2가지(SimpleAggregateFunction, AggregateFunction)
  - 조건에 해당할경우 그에 맞춰 사용
  - 머지하는 순서에 따라 영향을 받는 경우 `AggregateFunction`을 사용해야 함
### Materialized View
  - Insert trigger로 동작하여
  - 데이터가 들어갈 때마다, merge가 가능하도록 함 
  - 많은 양의 데이터를 줄일 수 있음
  - AMT와 같이 사용하게 되면, 크게 데이터를 줄일 수 있음(52 -> 2)
### source 테이블에서 다양한 단위 재집계 가능
  - 다양한 형태의 테이블 구성이 가능함 
  - 주간, 일간 데이터를 알아서 생성하기 때문에(별도 추출없이) 활용 가능
### ClickHouse Indexing
  - sorting key : SELECT에 유용한 설정
  - Granularity : 필요 이상으로 늘리거나 할 내용은 없음
  - 너무 긴 Primary Key -> 시스템 부하
  - Left-prefix rule, primary key 순서가 성능에 영향을 미침
### Table Merge를 고려한 Write
  - bg merge 작업 : flink-clickhouse-sink 라이브러리 사용
    - timeout seconds, buffer size를 조절하여 사용
### Flink H/A
  - checkpoint, autoRestart
  - yarn cluster
  - 재구동 될 때 at least once 등의 설정은 가능함
### ClickHouse H/A
  - zookeeper를 통한 replication
    - one copy, two copy 등의 구성 가능
  - **기본적으로 multi-master 구조(master-slave가 아님)**
    - 조회시 같이 조회해야 함
### Data Loss
  - data가 fire될 때, overlap data와 같이 fired
  - flink는 update는 불가함
  - 단, AMT처럼 refresh merge tree
    - 데이터의 버전 명시 가능
    - fire되는 시간의 timestamp 기록
    - 버전이 낮은 데이터가 제거되도록 운영
### 실시간 Backend
  - 데이터의 수 줄이기(ArrayJoin)
  - Materialized View + AMT를 사용하여 Records수 줄이기
  - **Source Table + Materialized View의 조합**
    - 마치 labmda로 따지면 speed -> batch 데이터 생성 과정

## 5. 효율적인 Visualization
- BI 도구
  - FE 차트 라이버리까지 대체 가능한 시각화 제공
  - Kibana등도 여기에 속함
  - chart의 customize가 많이 발전함
  - SuperSet
  - **Tabelo**
- 여기서는 `Tabelo`를 사용했으며, js api 형태로 호출하여 사용함
- Front app에서 tabelo api를 호출하여 데이터를 그림
- 다차원 구현을 위해서 다차원 필터 연동을 진행해야 함
  - 여러 차트를 하나의 공통 필터 사용이 가능함
  - `getAppliedValues()`
    - 대시보드상 설정되어있는 필터의 값을 계속 받을 수 없음
    - 필터의 data src상 받아올 수있는 api도 없음
    - 필터의 사본을 만들어서 worksheet에 추가하여 사용해야 함
- 차트 라이브러리
  - Data-Driven Documents
  - Google Developers Charts
  - Chart.js
- 다양한 BI 툴
  - ![image](https://user-images.githubusercontent.com/10006290/104799499-edf76080-5812-11eb-966e-6e08a3ed7545.png)
  - Tabelo외에도 관련 api가 제공된다면, 해당 BI에서 값을 받아 사용할 수 있을 것으로 보임

## 6. Wrap up
- 전체 구조도
  - ![image](https://user-images.githubusercontent.com/10006290/104800252-72e27a00-5813-11eb-85ee-88e1bf3eb775.png)
