# #1 Flink Seoul First Meetup with data-Artisans
- 18.12.18, 스페이스나인 삼성점
- Written By. wshid(kso4013@gmail.com)
---


## Keynote: Opening (Kostas Tzoumas, data Artisans)
---

### 실시간 빅데이터와 분산 처리 엔진 오픈소스 플랫폼 Apache Flink 소개
---
- Realtime streaming data process
- Batch Processing
    - 기간이 정해짐
- streaming data
    - unbounded data process
- Flink
    - 스트리밍 및 배치 프로세싱이 가능한 오픈소스 플랫폼 
    - Data process
        - Event-driven
        - Streaming pipeline
        - Stream & Batch analyze
    - 계층 
        - local, kubernetes, yarn cluster
        - Flink Runtime
        - User API
            - Dataset, DataStream
    - 마무리 작업은 `sink`로 진행 
    - Parallel Dataflow
        - 분산 처리가 장점
    - Time : 두가지 시간 개념이 존재
        - **event time**
            - 더 중요함 
            - 순서가 다르게 들어올때, sorted를 하려면 cost에 대한 처리가 가능
        - process time
        - 스타워즈 예시
    - Stateful
        - 복구에 능동적


### Streaming Processing takes on Everything
- Stateful function
- Realtime ETL
    - 변환, 추출, 적재 
- 새로운 어플리케이션의 등장 
    - Abstractions API
    - Consistency
        - Exactly-once, savepoint
    - Scalability
    - Interoperability
        - Deployment, Connector, Operations
        - RESTFUL Flink이 위치
- Stream processing take on ACID
    - Exactly-once Changed Application
        - 데이터의 중복/손실이 발생하지 않음
    - ACID Transactions for Multi-key stream processing
        - 트랜잭션의 조건을 만족 시킨다.
- A Library on top of Apache Flink
    - da-streaming ledger
- Stream Processing in SQL
    - [Flink in SQL](https://github.com/dataArtisans/sql-training)
    - SQL과 같은 명령으로 이벤트에 대해 조회할 수 있음 
        - group by, join 연산과 같은 다양한 연산 가능
    - 복잡한 연산에 대해 
        - `SELECT * FROM ?` : *와 같은 연산은 이벤트 타임으로 동작하면 매우 복잡한 연산 
            - `PATTERN (S M(2, ) E)`와 같은 키워드 사용
- Technical Enablers matter
    - Parallel Computation on Local State
        - 로컬에서 parallel연산을 한다고 함


## Real-time driving score service using Flink (김동원, SKT Data 기술원)
---
- T-map
    - Driving Score
        - 자신의 운전 습관에 대한 점수를 확인할 수 있음 
        - 주행 기록을 분석하여, 3개의 factor에 대해 점수 계산이 진행된다.
        - 어디에서 이슈가 발생했는지도 확인 가능 함
- 사용자가 실제 사용 시
    - Timestamp가 적힌 Gps trajectory를 계쏙 수집한다.
    - 실시간으로 하기 전에는 배치작업으로 돌렸다고 함
- 실시간으로 구현하기 위해 flink 사용 
- `kafka -> hive`과정을 `kafka -> flink`로 개선
- **processing time**은 단순히 시스템에 들어온 시간으로 처리 
    - **event time**은 로그에서 박힌 시간을 기준으로 계산하게 됨 
- event time
    - watermark
        - 이벤트 타임의 시간을 결정 
        - progress of event time
        - 보통은 실제 watermark를 생성 
            - 가장 큰 타임 스탬프를 골라서 찍는 것이 아닌   
                - 특정 바운드 만큼을 뺀것으로 결정한다.
        - 워터마크를 찍는다는 것은, 시스템의 현재 시간을 기록하게 되는데,
            - 워터마크 수보다 작은 데이터는, 추후에 들어오더라도, 무시가 되어버린다.
            - 따라서 bounded하여 워터마크 수를 결정하게 된다.
        - missing이 없도록 하기 위함
- flexible windowing
    - 다양한 기준의 window를 지원함 
        - tumbling windows
            - 갈이를 정해서 결정(length)
        - sliding windows
            - 특정 간격별 밀어서 설정, length, slide
        - session windows
            - idle time이 정의되어 있음 
            - 5분이라 결정시, 5분정도 이벤트가 없다면 session gap으로 처리하여 session을 구분한다.
            - 사용자 입력 받을 시, 종료코드를 못받는 상황등이 있음. 그 때 사용하면 유용 
- exactly-once semantics
    - fault시 재시작, 중복과 손실이 없어야 함
    - at most once
        - 손실은 존재 한 경우
- light-weight fault-tolerance
    - checkpoint 기능 
    - 주기적으로 persistance-storage에 내려서 사용한다고 함
        - 데이터가 메모리에 올려서 작업되는데, 손실을 방지하기 위함 
- high throughtut low latency
    - spark은 throughput이 높음
    - flink는 둘다 좋다고 함
- flow
    - flink -> prometheus -> grafana
- 12분 운전시, 얼마마다 gps 좌표를 생성하고 받아낼 것인가 
    - 매 초마다 보내면 너무 많음 
    - 특정 배치성으로 보냄 
        - 5분간격으로 생긴 메세지들(300message)를 보냄 
        - 마지막 end message를 볼때 마무리 
    - end message를 받으면 결과를 처리하도록 
- 코드 상 구현 
    ```
    .addSource(..) // flink에서 kafka를 읽음 
    json 내용 파싱 
    keyby 연산을 통해 유저마다 다르게 관리 
    .trigger  // custom trigger를 정의 // session gap을 사용, 
    . aggregate // factor에 의한 계산
    // kafka에 새로운 토픽으로 설정
    ```
    ```
    kafa -> source- > JSON Parser -> Bounded out of orderness -> session window with a custom trigger -> sink -> kafka -> service db
    ```
- session window와 기본 trigger에 대해 
    - record가 들어오면 시작 윈도우를 결정 
    - 윈도우 끝에 timer를 건다고 함 
    - 새로운 이벤트가 들어오면, 두 이벤트를 합친다. 
    - 이전 윈도우를 삭제한다.
    - 하지만, 마지막 타이머가 계속 남아 1시간이 기록된다.
- Custom trigger를 사용 한다 
    - 이벤트 추가는 동일하게 함 
    - 마지막 End가 기록된 곳에 Timer를 건다.
        - 이후 윈도우 사이즈 만큼 이상의 timer(기본 timer가 설정) 한곳에서 fire하지 않음 
- 만약 end가 먼저 들어오고, 중간 것이 너 늦게 들어온다면?
    - `a-b-d-c`순
        - early-fire를 동작 시킴 
            - `a-b-d`

    - EaryResultEventTrigger를 깃헙에 있다고 함
- [flink-examples](https://github.com/eastcirclek/flink-examples)
- easticclek/flink-examples/.../EaryResultEventTimeTrigger.scala
    - onElement : element가 들어올 때 마다 작업 
- prometheus
    - 
- metric은 flink의 meter를 설정하면 됨
- histogram : 메세지의 크기 분포도 확인할 수 있음 
- jitter
    - ingestion time - event time
    - 혹시나 사용자가 시간 조작으로 인해 뒤집히는 결과 요청이 오는 것을 방지 
- prometheus는 pull로 작업을 하기 때문에, 원하는 metric을 가져오기 위해 
    - prometheus가 각 노드별로 생성된 prometheus 로그 웹서버로 띄우고 기다리는 것을 
        - 찾아 긁어 와야 함
    - 하지만, port별로 복잡하게 올라가므로, 어느것에 떠있을지 확인이 불가능 하다.


## Flink 적용 전 고민해볼 것 (김용휘, LINE)
---
- 대부분 lambda architecuture를 사용함 
    - 여기서 streaming layer를 사용할 때..
        - `spark, flink, kafka, heron, ...`
- 개발과 운영이 좋으면 당연히 좋은 framework
- Pardo
    - MapReduce와 같은 operation
    - parallel한 processing에 대해
    - apache vim이 이 용어를 사용한다고 함 
    - 여러 서버에서 일어나는 operation
- GroupbyKey
    - 키별로 모든 노드가 참여 
- streaming data는 기본적으로 unbounded-data 
    - 시작과 끝을 정해야지만 사용이 가능함 
    - time based, event based 등의 방법이 있음 
- process time 기준으로 처리하면 단순히 서버에 도착한 순으로 처리하면 되지만,
    - event time에 대해서는 
        - trigger, lateness data를 얼마나 기다릴 지, 등의 여러가지 조건이 필요 
- Stream API
    - `map, flatmap, KeyBy, Reduce, Fold, Aggregation, ...`
        - Map, FlatMap, Filter : Same Task Slot
            - data의 셔플이 일어나지 않음
        - KeyBy, Reduce, Fold, Aggregation 
            - 셔플이 일어남, 보통 셔플링을 줄여야 함
    - catalistic optimization
        - spark가 지원 
        - **filter push down**
            - 데이터의 셔플 전에, 보내야 할 데이터를 최대한 거르는 것
            - 안쓸 데이터를 버린다는 의미
- event/process time 설정 
    - `env.setStreamTime(..)`
- 이벤트 타임을 사용하려면, 워터마크를 설정해야 함
- 언제 발생 시킬지, 늦은 데이터를 얼마나 기다릴지, 처리할 것인가 등
- ProcessFunction
    - low-level operation
    - 로우단에서 코딩이 가능하게 됨
    - API보다는 빠르나, 가독성이 떨어짐 
- Rich Function
    - runtimecontext에 접근이 가능 
    - task open/close 를 할때 어떻게 할지 
    - Document에 내용이 조금 모자름 
    - `flink-tensorflow 2016.06에 발표된 내용에도 있다고 함`
- Broadcast State in Flink
    - 스트림이 들어올 때, 특정 패턴을 감지할 때
    - Broadcast pattern을 뿌림 
    - 각 노드별로 로그를 받다가 패턴이 확인되면 처리함 
    - 패턴을 임의대로 바꾸고 싶다면 
        - 패턴을 코드안에 넣고, 코드를 deploy를 계속해줘야 함
            - 하지만 broadcast를 사용하면 해결됨
    - 변경이 자주 일어나는 패턴을 찾을때 사용할 수 있음 
        - stream pattern에 등록후 사용 가능함 
- Connector
    - 호환성이 높음
- Flink DashBoard
    - task slot
        - DAG에서 네모 밖으로 나가면 shuffle이 발생함 
    - metric
    - checkpointing
        - stateful하게 개발을 했을 때, windowing을 올릴 때 checkpoint를 확인하게되면 
        - 용량을 확인할 수 있음, 용량이 클 경우 당연히 delay 발생, 문제점 확인 가능 
    - back pressure testing
        - 장애가 났을 때, 처리하는 과정 
        - kafka의 경우 장애가 발생하면, 복구시 바로 모두 다 들어오게 됨
            - 에러 테스트 방법은 되게 많음 
        - flink의 경우 대시보드에서 처리가 가능 
    - 단, dashboard에 history가 없음
        - Monitoring을 구현 해야 함
        - 로그를 직접 확인하여 REST API로 metric을 뽑아내야 함
            - Job Manager에서 이미 메트릭들은 확보되어 있음
        - 어떤 시스템을 선택할 것인지는 회사에서 제공하는 것 사용할 것
- 이상 징후 탐지 
    - Z-core method, modified z-score method, IQR method  - Statistics Based
        - 보통은 0~1사이 일 듯
    - pattern matching - Rule Based
    - 해당 지표에 대해 어떤걸로 알람 받을지
    - 여러가지 지표가 있지만 **latency tracking**을 사용한다고 함 
        - 최소한의 alerting으로 설정 한 것
- Resource Management
    - hadoop, mesos, kubernetes
    - 클러스터 구성 시
        - yarn의 경우 다양한 것이 돌 수 있음 
        - heavy batch가 돌면, 클러스터 내 전 서비스가 느려질 수 있음 
        - 가급적 
            - batch cluster와 streaming cluster를 분리하는 것이 좋음 
        - 단 분리가 안될 경우, 
            - heavy batch가 일어나는 것을 모니터링 하면 됨 
- 가급적 운영 시 
    - Avoid to consuming from kafka-earlist-offsets
        - retension기간이 길 수록 엄청난 부하
    - Be careful of GroupByKey operator
        - skew 조심할 것 
            - 키에 의해 한쪽으로 쏠릴 수 있음
    - Do it first to Predicative / Filter-out operator
        - 셔플전에 먼저 쳐내기 
    - Repartition / Rescaling for bottle neck 
        - 여러개의 머신을 사용하여 
    - Use Async Logic
        - 바로 체크가 필요 없다면 
    