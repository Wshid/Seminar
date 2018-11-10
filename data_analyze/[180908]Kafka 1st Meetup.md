# 카프카 1차 Meetup
- 케이트윈타워 11층, 한국 마이크로소프트, 14:00 ~ 18:00
- Written By. wshid(kso4013@gmail.com)
---

## 카카오스토리의 카프카 사용 사례(Data PipeLine @Kakao Story)
---
* Data Pipeline @ Why
    * 4차 산업혁명, 로깅등에 대한 데이터가 많아짐
    * 그에따른 인프라 구축 및 파이프라인 구성이 필요해짐
    * 정제 처리, Anomaly Detection
    * Collect -> move/store -> explore/transform -> aggregate/label -> learn/optimize
    * 사실은 AI가 상위단이지만, 그 전의 데이터 처리도 중요하다
* Data Pipeline @How
    * Lambda Architecture(http://lambda-architecture.net)
        * new data
        * batch layer(새로운 데이터는 누적)
            * Master dataset
        * serving layer
            * 날짜별 취합
        * Query
            * Serving layer에서 데이터를 가져감
            * Speed layer와 serving layer에서 데이터를 가져감
                * 배치 형태와 실시간 형태의 데이터 모두 가져감
        * Speed layer
            * new data를 받아 증분계산을 미리 진행
    * ETL(Extract Transform Load)
        * E : 데이터 소스로부터 데이터 추출
            * 실시간 처리 - kafka
            * 배치 처리 - hdfs
            * 개인 컴퓨터 - csv
        * T : 데이터 변환
            * Cleaning, validation, sorting 등
        * L : 변환된 데이터를 최종 시스템에 전달
            * RDB, NoSQL, OLAP(실시간 분석 플랫폼), ...
* 주요 구성 요소
    * Logback, logstash, kafka, spark, scala
        * logback - 일정 시간 단위 rolling이 가능함
    * Spark 를 사용한 이유
        * 배치, 실시간 처리, ML 지원
        * Spark가 클러스터링 지원, 필요에 따라 scale-out이 가능
        * 기존 java library를 사용
        * Scala의 learning curve를 무시할 수 없음
    * Kafka는 여러 시스템에서 쌓이는 데이터를 하나로 모을 수 있음
    * Spark steaming ETL 과정
    * 정제를 하여 다시 server로 밀어넣을 수 있음
    * 데이터 전달 방식
        * sync : API 서버 운용
            * 비추천, 서버에 따라 응답을 못받게 되면 동시에 느려짐
        * async : 카프카의 큐 이용
    * Spark Batch
        * 카프카를 통한 정제 이후 데이터를 
        * Spark로 로드하여 Storage나 외부 시스템, 큐로 집어 넣는다.
    * BI(시각화..?)
        * Stat Admin, Google Datastudio등으로 표현할 수 있음
    * RDB GSuite
        * RDB를 사용하지 않고, 구글 스프레드시트등을 이용함
* Kafka topic & Log
    * 구성
        * Log를 떨구고
        * 물리적인 카프카 토픽
        * 스트리밍 과정에서 app을 붙임
            * 단순한 과정이긴 하지만, recovery 과정의 관리포인트가 늘어나기 시작
            * 스트리밍 app이 적다면 사용할만한 함
    * 구성2
        * Unified Log processing
            * Log를 labeling
            * kafka topic, 물리적인 내용으로만 사용(Unified Log)
            * 하나의 스트리밍 app이 ETL단위로 분할하여 처리
        * 로그 포맷이 일원화 되어 관리하기 편함
        * 로그 종류가 늘어나도 토픽 생성이 필요 없음
            * Topic은 오직 unifiedtopic
        * 특정 ETL이 지연되면 단체가 지연됨
    * 구성3
        * 변환된 데이터를 다시 kafka topic로 밀어넣는다
        * Loader채널을 따로 두어 해당 부분에서만 로딩할 수 있도록 한다
        * 로그 포맷의 일원화, 로그에 특정 키워드 명시가 중요
* Kafka log & HDFS
    * Batch Layer를 구축하기 위해..
    * 10분단위 적재(특정 topic으로 오는 내용을 Hive로)
        * 실시간 Streaming App은 짧은기간, 일정량의 데이터만 처리해야 하기때문에
        * 기간을 적게 잡음
    * 추후 Hive에서 일단위 처리
    * Kafka 이용 축, Batch Data 이용 축
* ETL Case Study
    * 실시간 뎉이터는 실시간 처리
    * 배치 데이터는 Hive나 HDFS에서 데이터를 저장하고, 그 데이터를 가지고 처리
    * A/B Test PipeLine
        * 데이터 기반으로 의사결정
        * 어떻게 버튼을 위치 시켰을때 사용자가 더 선호하는가
        * 사용자 이벤트 정의
    * 이렇게 테스트를 하려고 한다면, 클라이언트 릴리즈에는 구애 받지 않아야 하며
        * 계속 늘어나는 데이터에 대해 추가 수정없이 구성이 가능할지
            * 기실험 데이터는 json, 그리고 Base64를 사용한다.
    * 또한 사용자 집단을 나누어야 함
        * 한 사용자에 대해 rand로 나누게 되면 사용자에게 혼란이 일어날 수 있기 때문
    * Kafka?
        * client-log 토픽
            * Click, view에 대한 로그들까지 포함됨
            * Drop invalid log
            * decode impression id
        * Transform
            * load를 하여 추후 druid를 사용
            * Roll up을 사용하여 실시간 뷰 구성
                * 말아 올린다
                * 압축을 한다는 것이며, sum이나 이런 과정을 통해 진행이 가능함
    * BI Visualization
        * Real Time View
            * Druid - Pivot
        * Batch View
            * Daily Spark batch
            * google spreadsheets- data studio(무료)라고 함
* Summary
    * Front- back- system 간의 협업
    * 통합된 로그 형식 및 Data Flow에 대한 고민
    * 지속 관리 포인트는 제거
        * 스트리밍의 앱의 수는 최소
        * 불필요한 데이터 소스 및 ETL은 주기적 제거
    * 로그는 누락이 있을 수 있음
        * 네트워크 단절이나..
        * 완벽하게 쌓일. 수 없음
        * 로그 스키마는 계속 변할 수 있음
            * 하위 호환성 문제도 고민해야함
    * 패러다임 & 용어
        * Idempotent
            * ...
        * eventual consistency
            * 중간과정은 이상하나 결과적으로 정합성은 맞음
        * 확률 자료 구조
            * Hyperloging, bloomfilter
        * AB/Test -> Thompson Sampling
            * 나쁜 것에 대해 빠르게 알아차리는 방법
        * BI
            * Cardinality, emtric, segment, dimension 등
        * ML Pipeline
* Spark Streaming의 예외처리
    * Marathon을 붙여서, 죽으면 재시작을 하도록 하게 함
        * Marathon에서 spark앱을 다시 올림
        * spark 옵션중에 다시 실행시켜주는 옵션이 있다고 함

## 스파크, 그리고 카프카 타임 스탬프 오프셋 - 강대명
---
* 일반적인 방법
    * Kafka and Spark Streaming
    * Log aggregation server -> logstash -> kafka -> Spark Streaming
* Kafka log
    * partition이 나뉘며, 보통 그 partition마다 consumer가 붙는다
        * Partition은 그 내부에서 segment로 나뉜다
            * Default 1GB
* 문제는, 카프카의 중간에만 분석을 해보고 싶다면
    * 전체 데이터를 로딩 후, 필터링을 해야함
* Timestamp를 사용하여 특정 내용만 로딩이 가능하다(Query)
    * kafka 얼마전에 2.x버전이 로드 됨
* Related KIP ?
* 기본적으로 로그 파일은 3가지가 생김(세그먼트 하나당 3개씩)
    * .log
    * .index : offset이 저장됨
    * .timeIndex : timestamp가 저장되는 곳
* Kafka index files
    * offsetIndex.scala
    * timeIndex.scala 
* When Kafka write Logs
    * MMAP으로 사용됨
    * Offset은 상대경로로 저장됨
    * Current offset - base offset으로 저장됨
* Timestamp offset
    * Timestamp / Offset 
    * 8byte / 4byte
* offset이 있어야 timestamp에 해당하는 내용 파악이 가능함
    * 특정 위치부터 찾으려고 사용됨
* Offset이든 timestamp offset이든, 내부 쿼리는 동일함
* Spark 에서 사용하는 함수
    * Convert timestamp to OfffsetRange
        * KafkaCnsumer.offsetsForTimes
    * Just Create KafkkaRDD with OffsetRange
* When Segment Rolled
    * size > maxSeegmentByte - messsageSize
    * 특정 시간이 지났을때 등
    * 5가지의 경우가 존재
* Kafka timestamp가 세팅될 때
    * 사실은 publish 시간을 기록하기 때문에 다름
    * 실제로 로그는 앞 뒤의 오차가 발생할 수 있음
    * Timestamp 변경이 이루어져야함
        * Logstash나.. 이런곳에서
        * Flumtd는 아예 기능이 없음
* Timestamp
    * 이전에 대한 시간에 대해 kafka에게 준다면
    * 과거에 못보낸 데이터를 이제서야 보낼 수 있다면?
    * Kafka에서는 저장이 될까?
        * Log에서는 저장이 되는데
        * Timestamp는 last만 저장이 되고, 나머지는 먹힌다
        * 내부적으로 append, maybeappend함수 때문임
        * Timeindex는 업데이트가 되지 않음
            * 무조건 이전 timestamp보다 높아야 가능함
        * 데이터를 로드할때 timestamp가 이전인 값이 존재할 수 있음
            * 따라서 정제 과정이 필요
        * 애초에 내부 코드는 무조건 이후에만 존재한다는 내용이 가정되어있기 때문에 발생
    * 따라서 과거 정보를 조회할때, 정제를 거쳐야 하는 이슈가 있음
    * 너무 과거의 index를 집어넣게 되면 retention이 발생하여, 로그 데이터가 전부 삭제될 수 있음
    * 필터링을 해서 임의대로 kafka를 집어넣을때는 한번 더 검증할것
    * 일반적으로 current를 넣기 때문에 큰 이슈는 없음
* kaffa
    * 배치 레이어를 없애고, 처리를 하는방식?
    * Lambda와 architecture의 주축
    * 추상적인 모델
* 카프카 스트림즈 테스트 & 디버깅하기
* Kafka streams
    * kafka의 내부 모듈/라이브러리
    * 스트리밍 처리가 가능하게끔 됨
* 카프카 스트림즈 어플리케이션을 어떻게 테스트를 하는가
    * 대부분 실제 instance에 물려 테스트..
* Kafka 2.0.0에서 에러가 발생함
    * 현재 내부 버그가 존재함
    * 1.0.0에서 동작하는 것도 돌아가지 않는다고 함
* 테스트 방식이 3가지가 존재한다고함
    * EmbeddedKafkaCluster
        * In-memory로 동작, log는 남지 않음
        * 실제 클러스터에 물려 동작하는 방식
        * Zookeeper에도 로그가 남음, 테스트 관리 형태로 확인이 가능함
    * TopologyTestDriver
        * KafkaStreams, Topology 객체를 테스트 해주는 툴
        * 가볍고 빠름
    * MockProcessorContext
        * Processor 구조까지 확인할 수 있음
        * Learning curve가 상당히 높음
    * 이 세가지 모두 장/단점이 있음
* EmbeddedKafkaCluster
    * kafka-streams에서 test 라이브러리 로드가 필요함
## 카프카 활용 - 비동기 시스템
---
* 비동기 시스템 구성
    * 배치
        * FILE, DB
    * 준실시간(Near Real Time)
        * Redis, RabbitMQ, Kafka
* Kafka
    * 대용량 트래픽 처리
    * Consumer Thread 수가 Partition 수에 종속적
    * Consumer에서 재처리가 어려움
        * Offset 처리에 의해 무유실 운영등에 대해
    * Consumer를 늘리고 싶을 때
        * Partition을 늘리면 됨
        * 혹은, 메세지 처리와 consume을 분리하면 됨
            * 문제는 이러면 메세지 순서가 깨질 가능성이 있음
            * 카프카의 사상과는 맞지 않음
                * 실제 이런식으로 하지 않음
* RabbitMQ
    * Consumer의 제약이 없음
    * 메세지 분산 저장 구조가 아님, 대용량 처리 미흡
* 비동기로 처리하기에 적절한 로직
    * Sync일 경우, one transaction 처리 
        * 응답이 느리다는 단점이 존재 
    * 지연처리(응답)이 중요하지 않을 때 
* Filebeat -> kafka -> application worker(web app) -> mysql / elasticsearch
* logstash -> kafka -> spark -> casandra
* 배치 구조에 비동기?
    * 보통은 spring batch를 사용함
        * 사용하기에 번거로움
        * 확장이 어려움
    * Jenkins를 사용하여 시작
        * JOB Manager
            * Partitioning 전처리를 한뒤 처리
* Queue를 활용한 비동기
    * Queue로 메세지 전송
        * 메세지를 로직에서 직접 전송(Async / sync)
        * 메세지는 로그로 남기고 외부 솔루션을 통해 전송
            * Logstash
                * Inode값을 사용하여 메세지 읽은 지점을 파악
                * Inode값 운용이 제대로 되지 않기 때문에(해당 시점 이전 데이터 누락이 가능함)
            * FileBeat / Fluentd
        * 두 방법 혼합
            * 정상적인 경우 로직 내
            * Queue에 문제 생길경우 log
    * Queue의 메세지 재처리
        * 실패한 메세지를 메인 큐로 재전송
            * 재시도 interval 등
            * 최대 재시도 후의 내용 추가
        * 실패한 메세지 처리를 위한 별도 큐 추가
        * 대용량 트래픽이 아니라면, RabbitMQ를 통해 재처리 되도록 구성
            * Queue-original
            * Queue-retry
            * Queue-failed
                * 더 이상 실패 시 수동 처리
        * Kafka를 사용하는 구성이라면
            * RabiitMQ를 나중에 처리하는 방식으로 구성할 것
    * Queue 혹은 Application 메세지 에러 발생시 처리
        * 특정 임계치 오류 발생시 Circuit Break(전송 중단)
        * 어딘가에 저장한후, Queue 장애가 해결되면 전송되도록 구성
            * File, Redis, DB등의 대안
    * 배포 시점에서의 메세지 송/수신(처리) 보장
        * Spring 사용시
            * Kill을 통해 서버 종료시,
            * 등록된 빈의 Destroy 호출 순서에 의해 
            * 메세지 송신 혹은 수신 구조에서 에러 발생
        * 메세지 송신
            * 서버가 종료되는 시점에 메세지를 끝까지 보내고 종료할 수 있도록
        * 메세지 수신
            * ...
        * 여튼 종료 로직에 해당 내용들을 처리해주어야 함
* Retry/interval
    * retry : 3회
    * Interval : 60s 추천

## SKT의 카프카 사용 사례 - 류지형
---
* 모니터링 시스템
    * 장애예방
* 서비스 구성도
    * Logstash * n -> InfluxDB -> Grafana -> slack
        * Telegraf : 수집 Agent
        * InflexDb : Timeserize DB
        * Grafana : UI 툴
        * Slack : alert 용도
        * InfluxDB가 죽으면 모두 유실
    * SPOF를 줄이기 위해서는
        * 중간을 두어 kafka를 활용
    * Telegraf -> kafka -> streaamSets 
        * -> InfluxDB -> Grafana -> slack
        * -> Slack
* Telegraf
    * brokers
    * topic
    * compression_codec
    * required_acks
    * max_retry
    * data_format
* logstash
    * event['now'] = Time.now.to i*1000000000;