# Druid Meetup 8th
- 18.10.16, 19:30
---

## 1st - Druid at naver.com
---
* Part 1 - 드루이드 입문을 위한 내용
    * Druid란?
        * Query Engine과 Storage Format
        * Elastic과 ClickHouse와 같은 위치
        * Column-oriented
        * 초당 약 300만건
        * 확장성이 좋음
    * Elasticsearch 비교
        * Elasticsearch보다 러닝 커브가 높음
        * Secondary Index가 존재
        * Flow of Query Processing
        * coordinate - broker와 유사
        * 데이터의 처리는 빠름
        * GC 오버헤드
    * What is Druid
        * 노란색 - 외부 컴포넌트
    * Queries
        * json 형태로 표현한다
        * druid자체에서 조인을 지원하지 않음
        * 조건들의 조합이 많아서 미리 말아놓기도 어려움
            * 빠르게 쿼리를 실행할 수 있어야 함
            * 조인을 지원하지 않지만, Spark를 연동해야 함
    * TopN Query는 Group By와 유사하다
    * Elastic에서는 Terms Aggregation을 사용하여 aggr
        * spark랑 물렸을때 ser/de에서 시간이 매우 많이 소요된다고 함
    * Most Viewed = TopN Query
    * Kafka로 log를 쌓는다 -> spark streaming 정제 후 다시 kafka -> real index
        * parquet으로 변환하여 추후 batch작업으로 사용
            * batch를 사용하여 여러 세그먼트를 한 단위로 compact
    * API server는 Node를 사용했다고 함
    * TopN Query
        * Historical = Data Node
        * 각 노드의 Top N을 가져온 후, broker에서 정제
            * Shuffling이 발생하지 않도록 함
        * 단, approximate이기 때문에 정확하지 않을 수 있다.
            * 각 노드에서 TopN만큼 잘려오기때문에, 랭킹의 역전에서 정확한 값을 가져올 수 없음
        * GroupBy와 비교 표에서 내용이 조금 다른것을 확인할 수 있음
            * 값의 차이가 나는 것을 감수해야할 듯
    * TopN Limitations
        * ES에서는 여러 dimension이지만, 여기선 오직 하나
            * Terms Aggregation
        * 같은 쿼리에도 답이 다르게 나올 수 있음
        * 해결해야할 점
            * part 2에서 설명해준다고 함
        * 실제 해당 내용을 해결하기 위해
            * Top 3n 정도를 각 노드에서 가져온다고 함


## 2nd - Continuously Optimizing Storage of Stream Histories
---
* Stream 데이터 저장시, 어떻게 저장소를 최적화 할 것인가
* Kafka를 데이터 스트림으로 받아 실시간으로 분석하는 것
    * 가장 많은 use case
    * Kafka Indexing Service
* Segment Publish
    * broker로
* segment handoff
    * historical에 가는 것
* task가 kafka의 작업 단위로 보인다.
* Time chunk는 특정 segment 단위로 보여짐
* imply 라는 앱을 사용하면 편하게 쿼리를 웹으로 볼 수 있음
    * kibana와 같아 보임
* Segment Compaction
    * 실시간 데이터의 불확실성
    * segment compaction은 최적화를 의미함
    * segment마다 버전을 갖게 함
        * 보통 가장 높은 버전이 priority가 높음
        * 높은 버전의 세그먼트가 낮은 버전을 overshadow 한다라고 함
    * General Guideline 슬라이드 확인하기
* 3가지 방법 Compaction
    * Hash partition
    * sorting order 변경, 각 seg마다 천만개
        * partition key로 사용되었던 컬럼을 order 앞으로
    * 같은 sorting order, 각 세그먼트당 2000만개
* Segment수가 Compaction이후 줄어든다
    * limited-size의 Thread pool
    * segment가 줄게 되면 당연히 query time은 빨라짐
* segment가 time chunk당 얼마라는 건가..?
* Autometic Compaction을 직접 구현했다고 함
    * 기존 durid에서 merge등의 기능이 있었음
    * merge랑 append는 compaction과 유사
    * indexing task, 기본적으로 batch job
    * type과 datasource interval를 지정만 해주면 그에 맞추어 compaction 하도록 함
* coordinator
    * incubator-druid/pull/5102
    * segment가 compaction해야하는지에 대한 정보를 판별
* keepSegmentGranularity : 기존 Timestamp 유지
* targetCompactionSize : 기준 사이즈 지정
    * 각 timestamp마다 segment를 하나로 compact를 하게끔 함
    * coordinate 설정
* compaction이 진행중일 때는, 기존 원본에 대해서 query를 날리게 됨.
    * 따라서 운영상에 문제는 존재하지 않음
    * 중간에 있는 숫자는 segment의 수를 의미함
* Prioritize Locking
    * 높은 priority가 낮은것의 lock을 깬다
    * compaction이 진행될때 Lock을 사용
    * 기존에는 lock이 걸리면 기다려야 하는 문제가 있었음
    * Compaction의 priority가 높은 순서대로 lock을 뺏김
        * 그리고 버려지게 된다.
        * 그렇게 되면 다음 타임에 만들어야 함
    * realtime의 경우 문제 없이 동작은 할 수 있음
* 하지만 문제
    * 대부분의 데이터는 제시간에 도착하나, 일부 데이터는 늦게 도착한다.
        * 최대한 한달 늦게....
        * kafka indexing에서는 lating data를 처리하기 위한 task를 새로 처리
        * 이렇게 되면 lock을 뺏겨 compaction이 제대로 진행되지 않음
    * historical 데이터를 kafka로 넣으려는 문제
        * 들어오는 timestamp가 random해짐.
        * 데이터 로딩시까지 compaction 진행되지 않음
* Segment Locking
    * locking의 단위를 줄임
* Compaction
    * druid에서 개발하는 hot한 기술
    * single index task로 도는 특징을 가짐
    * time chunk마다 데이터가 많아지면 문제가 발생할 수 있다.
* 토픽 내부 스키마가 변경될 때?
    * druid 자체에서 스키마에 대한 자유도가 높음
    * druid 자체에서 최적화를 해준다고 함
        * 없으면 단순 null로 리턴
    * 설정으로 null이나 0등 기본값 지정이 됨
* Secondary Partition
    * Pruning이 가능
    * 해당 컬럼에 대한 filter를 걸게 되면, 그에 대한 segment wrapping이 된다고 함
    * 지원되는 partition이 한정적이라고 함
* 금일 OS 공부를 하고 kafka 공부를 해야할 듯
* nested json의 지원 여부
    * 1단계만 지원
        * flatten spec을 사용해야함
* segment file을 column based를 사용하지 않는 이유
    * 고유의 파일 포맷을 사용했었음
    * druid file format 역시 column based
    * 자체로 최적화를 해준다고 함
* imply
    * superset보다 뛰어나다고 함?
    * metatron은 opensource
    * 쿼리 타입에 따라 성능이 변함
        * 단순히 key값이 무엇인지 이정도가 아니라고 함

## 3rd - Building GIS on Druid
---
* ApacheCon에서 발표하셨던 내용
* Druid
    * OLAP 
    * Lambda Architecture
        * realtime과 batch를 따로 운용
        * realtime을 빠르게 처리할 수 있음
* why druid
    * 빠르기때문
    * java based
    * Hbase 운용시 phoenix or kylin을 사용해야함..
        * 기린기린기린
        * Hbase는 리소스가 많이 든다
        * Secondary Index에 따라 n배 storage 비용이 늘어남
    * 역시 비교대상은 ES
        * 역시 리소스 많이 먹음
    * 비용은 싸나 운영하기엔 조금 어려운
* Druid in SKT
    * 거의 3년가까이 운용
    * Tango, Tmap, MDM, Jive, ...
        * Tango : Consolidated netowrk management DW
* Druid
    * Batch Ingestions
        * 보통 여기선 배치로 사용한다고 함
    * 원하는 기능에 따라 조금 수정해서 사용
    * Query
        * 확장하게끔 되어 있음
        * K-means
        * Iteration
        * Join 등..
    *  요즘 Kubernetes로 말아서 사용도 한다고 함
* Index
    * JSON도 사용할 수 있음
    * Column parts
    * look possible
        * row를추가 또는 hide
* Ranged Histogram
    * 범위를 쪼개어 row만 꺼낼 수 있도록
* Bit-sliced bitmap
    * 빠르고 정확
    * 속도가 느림
        * full scan * 10배의 시간... 안씀
    * 아이디어는 좋지만
* Lucene Index
    * 텍스트 인덱싱
    * 실제 구현은 해봄
    * Document를 dimension이 아닌, String형태로 운용
        * 해당 내용을 가지고 lucene index 사용
    * score는 없음
    * 할 수 있는거 다 할 수 있다고 함
* GPS coordinate
    * 루씬을 해보니까 다 되던데.. 해서
    * 로직을 만든다
    * r-tree index
        * ...를 활용
    * 엄청 빠르다 mongodb나 이런것보다
        * 다만 직교좌표만 지원하며
        * dimension으로 지원하고 r-index로 해야하기 때문에
            * 불필요한 dictionary, index가 생성됨
    * r-index를 어떻게 해보기
        * ES에서는 다 하고 있던데?
    * Lecene
        * Geo3D point
            * 언젠간 필요하겠지만
        * LatLonPoint 사용(BKD-Tree)사용
            * K-dimension
        * Shape
            * Recursive.. 지원


