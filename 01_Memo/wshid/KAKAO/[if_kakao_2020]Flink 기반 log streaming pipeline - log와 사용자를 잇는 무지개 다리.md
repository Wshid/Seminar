## Flink 기반 log streaming pipeline - log와 사용자를 잇는 무지개 다리
- link : https://if.kakao.com/session/116

---

### Streaming? Pipeline?
- log pipeline
  - 로깅 이벤트 시퀀스를 처리하는 플로우
  - src/dst가 존재하는 데이터 흐름
  - 로그 데이터 전송과 변환을 자동화
- Streaming

### Kakao log pipeline
- KEMI(Kakao Event Metering & Monitering)
  - 데이터 수집 저장 가공 및 전달
  - 카카오의 모든 리소스에서 발생하는 데이터를 수집/저장/가공하여 필요한 이벤트 전달
- KEMI Architecture
  - ![image](https://user-images.githubusercontent.com/10006290/102005455-4ba01500-3d5c-11eb-9732-ce2157b525ef.png)
  - lambda architecture

### Needs
- 로그를 실시간으로 보고 있다
- log trailer라는 툴이 있으나, 검색이나 쿼리가 가능함
- 결국 실시간 파이프라인 니즈 때문
- 필터가 가능해야 하며, 요청에 따라 ES 적재를 옵션처럼 선택할 수 있도록
  - ES자체가 매우 비싼 리소스이기 때문 
- 확장 가능한 시스템
- 신뢰성이 높은 시스템이면 좋음
- ![image](https://user-images.githubusercontent.com/10006290/102005546-e567c200-3d5c-11eb-89b3-dccc2f5b636e.png)
  - TODO에 대한 내용 구현이 필요

### Apache Flink
- Stream processing framework
- checkpoint를 통한 `exactly-once` 보장
  - replay를 통해 exactly-once를 지원
- yarn, mesos, k8s 배포 지원
- spark, storm 등과 비슷
- flink의 구조
  - ![image](https://user-images.githubusercontent.com/10006290/102005578-34155c00-3d5d-11eb-9217-d57b75cdfeac.png)
  - Job Manager : master
    - Task Scheduling, checkpointing, Recovery
    - 전체 overflow를 가지며, job task manager에게 적절하게 분배
    - actor system을 통해 상호 커뮤니케이션(scala)
    - job manager
      - 잡 배포, 체크포인팅, 태스크 상태 관리, 통계정보등을 수집하여 저장
  - TaskManager : worker
    - task execution
    - 실제 잡이 수행
    - 태스트 슬롯에 태스크가 할당
    - Network Manager를 통해 노드간 통신
      - 데이터를 송수신 할때, network manager의 데이터 스트림을 통함
- flink 예시
  - ![image](https://user-images.githubusercontent.com/10006290/102005643-c0c01a00-3d5d-11eb-91f4-b33f6d2ae42d.png)
  - source - transfomation operator - sink : 입력 - 출력
  - map 변환을 통해 데이터가 변함
  - keyby : 각 모양별 스트림이 분리되어 sink와 연결
  - Paralleized View : 실제 task manager로 분리되었을 때
    - ![image](https://user-images.githubusercontent.com/10006290/102005668-fb29b700-3d5d-11eb-833f-cec7b88b5071.png)
    - source, map은 direct
    - keyBy등은 리밸런싱

### What is Bifrost
- Flink 기반의 kafka to es pipeline
- on k8s
- prometheus 기반의 모니터링 KOKOON
- minIO 기반 checkpoint for reliability
- 파이프라인상 위치
  - ![image](https://user-images.githubusercontent.com/10006290/102005891-d8000700-3d5f-11eb-8615-af30b5b9993e.png)
- 오브젝트 스토리지 miniO를 이용하여 데이터 backup
- Bifrost Data flow
  - ![image](https://user-images.githubusercontent.com/10006290/102005922-07af0f00-3d60-11eb-8411-3f21a02021c6.png)

### 경험기
#### rebalance와 resale
- rebalance, inter-node
  - 노드간 통신, 네트워크 비용
  - 단순한 구조였기 때문에 굳이 셔플을 발생시키지 않음
- rescale : inner-node
  - 각 노드 내부에서 진행
#### 한 바구니에 잘 담기
- 연관된 task를 묶어 성능을 높히는 방법
- 한 task slot(task manager)에 할당하면
  - 데이터 교환없이 더 빠른 처리가 가능함
- 괜한 분산으로 네트워크 비용 발생 가능
  - 노드나 slot 안에서 처리하기
  - Task chaining / SlotSharingGroup / CoLocation
- 모니터링
  - backpressure
    - operator와 operator 간의 원한한 스트림 처리에 대한 가장 중요한 지표중 하나
    - 기본 제공되는 `source`와 `sink`의 `backpressure` 측정 방식에 대해서도 조사 필요
      - 원하는대로 수집이 되지 않을 수 있음
  - custom metric
    - application 내의 특정 값도 metric으로 만들 수 있음
    - 사용자 정의 metric을 만들고 이를 노출시키면
      - prometheus나 기타 모니터링 플러그인으로 수집할 수 있음
  - 무엇을 더 봐야할까
    - kafka
      - kafka lag
      - kafka fetch rate / fetch latency / io wait time 연관
        - 세 지표는 비례, 반비례 관계 존재
      - network 관련 지표
    - jvm
      - job manager cpu, heap
      - task manager cpu, heap
      - task amanger young generation GC
    - etc
      - checkpoint duration
- 클라우드 텔레메트리 파트
  - monitoring, logging, tracing, k8s, data engineering, stream processing, ...