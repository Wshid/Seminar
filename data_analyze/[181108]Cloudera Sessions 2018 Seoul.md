# Cloudera Sessions 2018 Seoul
- 18.11.08, 코엑스 하모니볼룸
- Written By. wshid(kso4013@gmail.com)
---

## DW 오프로딩과 스트리밍 데이터 수집 문제 및 고려사항
---
* Kafka - Fluentd 같은 position
* apache sqoop
    * sql에서 데이터 전환시 사용하는 기법


## Kakfa Stream vs Spark structured streaming
---
* 스트리밍 처리 방법에 대한 비교
    * 어느 상황에서는 어느 기술을 선택할 것인지
* 기존 처리 방법
    * RxJava, Spring Reactor, Flink, Storm 등
* SparkSQL
    * spark 2.0이상부터
    * streaming 처리 특화
    * 표형태로 자료 모델링
        * stream = 무한히 업데이트 되는 table을 의미
* Spark structured Streaming
    * 약간의 추가적인 개념만 알면 바로 쓸 수 있음
        * Source, Sink, Trigger, Watermark
        * 많은 Function과 Datasource들이 지원됨
        * Join, ML Pipeline
    * 어떠한 작업을 할 것인지
        * Catalyst Optimizer
* Kafka Streams
    * v0.10.0 ~
    * stream
        * 서로 스트림 데이터를 주고 받는 처리 과정들의 집합
    * 데이터 처리를 일종의 그래프처럼
        * 데이터를 주고 받는 것을 edge
        * 데이터 연산을 노드
    * DSL을 사용하여 작업을 정의
        * KStream, ...
    * 코드
        * Topology를 정의하는 부분과
        * 이를 실행시키는 부분이 따로 존재
    * FrameWork가 아닌 Library
        * spark의 경우 yarn 등을 설치해야하는 의존성(특정 runtime)
    * Masterless
        * Coordination이 필요 없음
            * 전체 작업을 stream task로 분리
            * source kafka topic의 partition 수를 기준으로 streamTask 개수 결정
    * Fault-Tolerance
        * statestore
* 결론
    * spark structured streaming
        * 여러 Data source에서 정보를 읽어와야 할때
        * 복잡한 처리를 해야할때
            * Join, Pivot, ML, Pipeline
        * ETL처리
    * Kafka Streams
        * kafka topic을 주로 처리하는 (경량) application을 개발할 때
            * kafka topic을 사용하는 micro service
                * topic들을 읽어와서 cache해놓고 질의 기능을 제공(Interactive Query)
            * kafka topic 전처리
                * topic(들)을 읽어와서 가공된 형태로 다른 topic에 저장
            * Event에 대한 Instant Prediction - 요즘 이슈라고 함
                * topic에 저장된 event에 대해 ML 모델을 사용해서 예측한 값을 다른 topic에 저장

## Impala at scale
---
* impala가 cloudera에서 어느 위치에 있는가
    * Impala는 Data Warehouse에 속함
    * Kudu라는 새로운 storage layer
* Batch Processing
    * Hive, Spark
* Impala
    * BI and SQL analysis
* Procedual Development
    * SparkSQL
* SQL을 사용하기 때문에 빠르게 도입시킬 수 있다는 듯
* Hadoop용 SQL 엔진, multi-user 지원에 대한 문제를 해결하기 위해 탄생
    * 동시접속은 DB를 넘어갈 수 없음
* 디스크와 메모리를 같이 사용하기 때문에
    * fail 확률이 낮아짐
* impala 동작 방식
    * coordinator
        * 특정한 노드에 작업이 할당 되도록
    * coordinator가 나중에 master가 되며
        * 실제 일을 할 노드를 지정해서 돌린다
    * 최종 결과가 coordinator로 돌아옴
* impala component
    * Hive metastore는 공유
    * Impala daemon
        * Catalog
            * 메타데이터를 캐싱하는 데몬
        * statestore
            * 요구, 변경사항이 있으면 전체 노드에게 broadcast
    * impald는 Hadoop datanode와 같이 돈다.
* catalogd
    * 메타데이터 관리
        * HDFS 파일 관리(namenode)에 대한 정보
        * 데이터베이스 테이블 정보 - HMS(Hive)
        * Hadoop에 대한 centry 정보
* Impalad도
    * front end - java
        * SQL 파싱 작업
    * back end - c++
        * 더 복잡한 작업
* 측정 -> 관리 -> 개선
    * 어떻게 좋고, 나쁘다를 판단?
    * 최신버전으로 가면 갈수록 Query Optimizer에서 통계값 활용도가 높아짐
    * Cloudera Manager -> Imapala -> Best PRactive
        * 통계값 문제가 있는 쿼리를 모니터링
        * 암묵적인 문제 발생자이기 때문
    * TS Query를 사용하여 문제가 있는 Impala 쿼리를 검색할 수 있음
* 커넥션 스톰 해결 방안(KRPC)
    * 클러스터에서 과도한 동시 작업은 connection storm을 발생시킴
    * 쿼리 복잡도가 높아지면 높은 부하 발생
    * 하나의 호스트에 여러 쿼리에 해당하는 fragment 존재
        * 해당 fragment마다 통신(Thrift)
    * KRPC
        * host끼리 통신
        * 따라서 훨씬 부하가 적어짐
    * Impala debug WEB UI를 통해 KRPC 지원 확인
* 전용 역할 부여하기
    * 전용 Executor, Coordinator 설정
        * 100개의 Coordinator, 5개의 Coordinator
        * Master(Coordinator)가 메타 데이터를 가져오게 되는데, 이 비용이 생각보다 크다
        * 주로 현업에서는 30~50대에 coordinator를 한대정도 주는 것을 추천한다고 함
        * coordinate only, executor only와 같은 옵션 설정이 가능
* Admission Control
    * Dynamic Resource Pool
    * 메모리를 기준으로 resource pool을 나눈다
    * docker와 같이 isolation이 완벽하게 되지는 않음
    * resource pool에 맞는 쿼리 옵션 설정이 가능함
        * 이를 부서별로 관리해도 좋음
* Spill to Disk 제어
    * spark에서는 디스크까지 쓰다가, disk에서 뻑나는 경우도 생김
    * temp table space를 local disk를 사용
        * 이를 spill to disk라고 함
    * 노드에서 허용된 메모리를 넘는다 싶으면 disk도 같이 사용한느 것을 의미
        * default : unlimited
            * 디스크를 엄청나게 사용할 수 있음
* Count Distinct 비용 절약하기
    * 정확/비정확의 쿼리들이 많음
    * 단순 count더라도 요즘은 비정확한 결과를 내는 경우가 많음
        * 속도 향상 등, 비용 저하 효과를 낼 수 있음
    * APPX_COUNT_DISTINCT 함수 사용
    * set APPX_COUNT와 같이 옵션 설정이 가능
* 리소스를 제어하는 다양한 옵션 검토 필요
    * NUM_SCANNER_THREADS
    * BUFFER_POOL_LIMIT 등
* Catalog concurrency semantics
    * coordinator가 실행계획을 세움
        * 이에 테이블에 대한 메타데이터가 필요
    * catalog는 lock기반의 동시성 제어 메커니즘 사용
    * catalog read/write lock, table lock
        * 대부분은 빨리 끝나지만..
            * 실제 실행은 1,2초 Query Plan이 n분
    * 주기적으로 catalog는 주기적으로 broadcast
        * metadata의 변경 발생시
            * getCatalogObjects()라는 함수 사용
                * 전체 객체를 다 찾아보게 함
    * 쿼리가 lock에 대해서 걸려있는 경우가 있음
        * 락을 두가지 획득해야하는데, 이에 따라 block이 되는 경우가 있음
        * 한가지 락만 획득하고 다른 락을 기다리게 되면서
            * 이는 resolve가 되었다고 함
    * catalog lock은 해결이 되었으나 broadcast에 대해서는 아직까지 존재