## [210707] Kafka Virtual Meetup
- 21.07.07 20:00 ~

---

## kafka sink connector에 대해
- s3 sink connector의 경험기

### Converter 설정
- key,value의 직렬화
  - StringConverter, JsonConverter 등을 사용
- producer가 직렬화한 방식에 따라 개발이 필요할 수 있음

#### Partitioner
- S3에 어떤 경로로 적재할 것인가
- FieldPartioner
  - 현재 struct type만 가능
- TimeBasedPartitioner
  - 메세지 시간 값에 따라

#### Flush 설정
- 일종의 버퍼를 가지고 flush를 진행함
- flush.size
  - 토픽 파티션별로 몇 개의 메세지 flush
  - 메세지 개수
- rotate.interval.ms
  - 어떤 시간 값을 기준으로 ms 단위로 flush 할 것인가
  - TimeBasedPartitioner를 사용해야만 적용 가능
- rotate.schedule.interval.ms
  - 00:00를 기준으로 몇 ms마다 flush 할 것인가

#### S3 설정
- 버켓, 데이터 포맷등의 설정
- AssumeRole
  - kafka-connet와 aws s3의 계정(권한)이 다를 경우 사용
  - Confluent Platform 5.5.1 버전 이후로 사용 가능
- s3.part.size
  - Multipart upload시의 part의 최소 크기
  - s3 파티션별 버퍼 사이즈
  - 최소 5MB

### example
- flush.size
- rotate.schedule.interval.ms 두개의 조건이 or로 flush
- partitioner.class로 파티셔너를 지정하고(class)
  - 이후 `path.format`으로 파티션 단위(날짜 단위)로 처리
  - year=../month=../day=../hour=../

### trouble shoot
#### 개발 관련
- S3 Sink connector가 정상적으로 구동되려면,
  - 2개의 프로젝트 빌드 필요
    - kafka-connect-storage-cloud
    - kafka-connect-storage-common
  - S3 Sink Connector 배포를 다운로드 받고
    - kafka-connect-storage-common을 빌드한 결과물 교체
- 커뮤니티가 중요함

#### 운영 관련
- Custom Partitioner 개발 및 적용
  - s3 세분화
  - 버퍼 수가 많아짐
  - OOM
- flush를 자주
  - 작은 크기 object 자주
  - Api call 증가
  - 비용 증가
- 이슈의 큰 원인은, 모든 메세지가 메모리에서 처리된다는 점
- 해결 방법?
  - 토픽별 worst case를 고려한 메모리 산정
    - 추후 메모리 값의 확장에 취약
  - kafka-streams로 정제하여 토픽 메세지별 다양도 축소
    - 관리 포인트의 증가
      - 부하 분산을 위한 토픽 파티셔너 개발 필요
      - streams app 개발/운영
- 해결 방법
  - 디스크 처리를 기반으로한 S3 Sink Connector 고려
    - 현재 개발 진행중

### 마무리
- S3 Sink Connctor
  - 카프카 메세지를 백업하기에 가장 손 쉬운 방법
- Kafka connect 운영은 쉬움
- 단, 다양한 이슈는 발생할 수 있음 

---

## Kafka Streams 소개 및 활용
- 카프카 스트림즈란?
  - 2가지 구현 방법 제공
    - 스트림즈 dsl
    - processor api
  - 내부적으로 RocksDB를 사용하여 상태 기반 처리가 가능함
    - 페이스북에서 개발
    - 실질적으로 사용할 필요는 없음
- 스트림즈 DSL
  - KStream, KTable, GlobalKTable을 도입
  - 상태/비상태 기반 처리 가능
  - `쓰일만한 기능들`이 없을 경우 Procesor API를 사용하여 개발
- Processor API
  - Streams DSL에 없는 기능을 개발할때 구현하여 사용
  - 직접적인 rocks db 접근 등이 가능
  - e.g.
    - 메세지 값의 종류에 따라 토픽을 가변적으로 전송
    - 일정한 시간 간격으로 데이터 처리

### Streams Task
- 토픽에 있는 파티션 수만큼 task가 생성
- task
  - 데이터를 처리하는 최소 단위
  - task는 1개 스레드에 여러개 들어 있을 수 있음
  - task 수만큼 thread를 늘려 scale-up 가능

### Scale out
- 좋은 전략
  - 파티션 개수만큼 인스턴스(프로세스)를 늘려 병렬 처리
  - 목적에 따라 작은 단위로 나눠서 배포
    - 이벤트 기반 micro service
- thread별로 task가 한개씩 할당될 수 있도록

### 코파티셔닝
- 두개의 토픽을 aggregation 처리
- 조건
  - 1: 동일한 파티셔닝 전략으로 레코드가 들어가야 함
  - 2: 2개의 서로 다른 토픽의 파티션 개수가 동일

### 상태저장소
- RocksDB를 활용
- 상태 기반 처리를 할 때 streams를 구현하면
  - rocksdb와 changelog 토픽이 신규 생성됨

### Processor
- 소스 프로세서 : 토픽으로부터 데이터를 가져오는 역할
- 스트림 프로세서: 데이터 처리/변환
- 싱크 프로세서: 데이터 토픽 저장

### Kstream, KTable, GlobalKTable
- KStream
  - 레코드 흐름, 메세지 키와 값을 처리
  - `poll`이후 레코드 단위 처리와 유사
- KTable
  - 메세지 키를 기준으로 데이터 묶어 조회
  - 토픽의 데이터를 Materialize하여 사용
  - task에는 할당된 파티션의 데이터만 포함
- GlobalKTable
  - KTable과 유사
  - 소스 프로세서로 가져온 토픽의 파티션에 대한 모든 내용을
    - KTable로 각 task마다 가져와서 처리
  
### Stateless
- 1개 레코드에 대해 1번만 처리 후 다음 topology로 처리
- 상태를 저장하지 않음
- 메서드
  - map, flatmap, split, filter, foreach, merge, print, selectKey 등..


### Stateful
- 이전 레코드에 대한 정보 저장
- mem이나 disk에는 무조건 저장해야하며, 장애상황에 대한 고민 필요
- kafka streams는 app이 동작하는 disk와 topic에 이중으로 저장
  - rocks db 기반
  - changelog 토픽 (Write-Ahead-Log; WAL)로 간주
- 메서드
  - aggregating
    - count, reduce
  - joining
    - leftjoin
    - innerjoin
    - outerJoin
  - windowing

### 카프카 스트림즈의 조인
- 메세지 키를 기준으로 처리
  - inner, left, outer