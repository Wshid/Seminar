## 셀프서비스 Stream Designer로 나의 시스템을 더욱 강력하게
- ksql기반 사용자 Self Stream Processing 플랫폼 서비스
- 기술track2_비스타홀2
- 13:30 ~

---

### 반도체 실시간 데이터
- 반도체 데이터를 가지고 활용
  - 24/7: 끝없이 데이터는 들어옴
- 수많은 raw데이터가 실시간 유입
  - 54MB/s

### What's Realtime Platform
- Low Latency
  - **실시간 유입 및 가공**이 일어남
  - 데이터 적재 하는 부분은 **batch의 일부를 활용**
- Legacy System
  - CDC -> EventHub -> Stream pipeline

### DataHub
- 유입: CDC pipeline, Event pipeline
- delivery: h-stream 구축

### h-Stream
- dev(sandbox)환경에서 데이터 조작, 검증
  - 이후 검증이 되었다면 운영 배포 진행
- **ksqlDB**
  - 스트림 프로세싱
  - 외부 저장소 연결(connector)
  - materized view(사용자 쿼리 확인)
- **stream**
  - topic, structure
- **table**: 실시간 데이터를 기준 정보와 조인, 기준 정보성 테이블을 테이블로 형성
  - stream, rocksDB

### h-Stream Feature
- **ops(real) -> dev로 토픽 복제 가능**
  - 운영 토픽의 내용을 개발 환경 토픽에 실시간 복제
  - 관리자 검증 없이 가능
- **복제된 토픽 데이터를 dev에서 검증 및 작업(사용자)**
- **복제는 토픽을 가지고 Stream Designer를 사용한 쿼리 사용**
  - kSQL 쿼리
- **Join및 집계와 같은 function은 다음 방향으로 제공**
  - ksql 내부 function 제공
  - UDF를 만들어 제공
- **dev -> ops, 쿼리 운영 환경 적용**
  - 개발 환경에서 검증된 쿼리를, 운영 환경에 적용하기 위해선 관리자 승인이 필요함
- **사용자가 수행한 쿼리 로그는 관리자가 확인 가능**

### 사용처
- 이상 탐지 등

### 이슈
- h-stream portal, **PaaS** 형태로 이루어짐
- replica 확장시 emitter를 찾지 못하는 이슈
  - 처음에는 k8s pod 하나로 운영
  - 처음에 실시간 처리를 spring webflux, sl3 Emitter 활용
  - pod의 replica를 늘려 배포한 순간 Emitter 없다는 에러 발생
  - 한 세션의 요청들이 다른 Pod으로 요청이 날아가면 이슈가 있음
    - back-browser 요청을 위한 emitter
    - ksql api를 요청
  - **redis pub-sub을 가지고 해결**
    - pub으로 사용자 정보를 요청
    - 동시에 모든 pod이 사용자 정보를 받아, emitter를 get한 동일한 Pod가 일을 처리하도록 함
- 라이선스 이슈?
  - **ksql opensource를 활용했다고 함, 라이선스 이슈는 없었음**