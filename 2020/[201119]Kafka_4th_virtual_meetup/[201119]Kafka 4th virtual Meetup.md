# 카프카 4차 Meetup
- 온라인 밋업
- Kafka 한국사용자 모임(KafkaKRU)
- Written By. wshid(kso4013@gmail.com)
---

### 로그 관리의 의미
- 이벤트 발생시 기록하는 부분
- 자료구조로써의 로그
  - trasaction log, mysql log, ...
- kafka 로깅시에 어떤 점을 로깅하면 좋은지
- Kafka-connect에 대한 내용
  - 어떤 로그를 어떻게 열어볼지
  - 작동하는 로거들을 관리하는 방법
- kafka 2.8부터 log4j2로 바뀐다고 함

### 로그의 핵심 : Logging in Kafka Broker
- 카프카 로그를 볼때 제일 먼저 확인해야 하는 로그
- kafka.request.logger
  - kafka의 다양한 client들에서 요청을 보낼때, 그에 대한 응답을 제공
- kafka.server.KafkaApis
  - default logging 설정에는 따로 로깅하지 않음
  - 기본적으로 정의된 내용은 없음
  - 특정 method call을 했을 때 문제가 되었을 경우 해당 메세지 확인
- kafka.log.LogCleaner
  - 자료구조로써의 로그가 compaction, retention등이 발생했을 때
  - 기본적으로 1주일 정도

### How to manage logging in kafka broker
- kafka를 k8s로 돌릴 때 문제가 큼
- 로깅 레벨을 동적으로 바꾸는 기능이 필요
  - 이 기능을 카프카에서 제공함
    - JMX Console를 이용하거나 `kafka-configs.sh`를 사용하여 변경 가능
- 문제는 이 기능이 documentation이 되지 않았다고 함

### How to manage logging in kafka broker(2),(3), ...
- JMX Console 사용(java application)
- 이를 이용해서 꼼수로 사용 가능
  - kafka 뿐만 아니라 storm, zookeeper 등도 활용 가능
- VisualVM
  - kafka, kafkaLog4jController
  - Operatioins
- getLogLevel의 값을 수정
- setLogLevel의 메서드로
  - 로거와 (kafka.request.logger) 및 로거 레벨을 지정하면 바뀜
- 굳이 kafka broker의 설정을 변경하여 Restart 필요 없이 자동 변경 됨

### How to manage logging in kafka broker(5), (6)
- 한번에 여러 항목을 변경하고 싶은 경우
- `kafka-configs.sh`를 사용하여 변경
- `--entity-type broker-loggers` 라는 옵션으로 지정이 가능
  - 문서화가 잘 되어있지 않음
  - `--alter --add-config`를 사용하여 변경

### How to manage logging in kafka broker(7)
- 기본적으로 카프카 초기부터 있는 기능이긴 하나,
  - 2.3.0 때 업데이트가 됨
- 하나는 예전부터, 하나는 2.3.0부터 지원하는 버전
- 위 슬라이드에서 말하는 내용은, 2.3.0 이후부터 적용 가능


### Kafka Connect (1), (2) ...
- kafka broker만큼 복잡하지는 않으나..
- 2.3.0부터 loggingContext를 같이 출력해줌
- `connector|worker`는 메인 프로세스가 찍는 기능
- `task-`가 붙으면 kafka-connector의 처리를 직접 수행하는 부분의 메세지

### How to manage logging in Kafka Connect
- `curl -s ...` 를 사용하여 내용 정보를 확인할 수 있음


### Log4j2 (3)
- `KAFKA_LOG4J_OPTS` 옵션을 사용해서 `configurationFile`로 변경됨
- log4j2에서는 root 로거의 이름이 `""`로 변경됨
  - 하지만 하위 호환성 때문에 `root`로 그대로 유지
- public beta

### summary
- jmx console & command line tool 을 활용하면 동적으로 변경이 가능함
- kafka connect 역시 비슷하게 로그 레벨 조정이 가능하며 Rest API로 조정이 가능함
- kafka는 추후 Log4j2로 넘어감
  - 하위 호환성을 위해 작업 되어있음
  - 한국 문서도 제공할 예정

### Questions
- k8s에서 yaml파일에 log4j2 관련 환경 변수 설정 조정이 가능함
- JMX Console이 false로 설정된 이후, true로 변경하려면 운영중인 카프카 리붓이 필요함
- 기본적으로 재부팅하게 되면 데이터가 유지가됨
  - 다만, 기본값이 tmp로 되어있기 때문에
  - (quick start의 설정을 안변경 하고 구동했을 경우에는) 기존 데이터가 사라질 수 있음