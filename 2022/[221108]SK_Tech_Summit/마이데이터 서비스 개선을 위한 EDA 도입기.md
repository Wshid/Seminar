## 마이데이터 서비스 개선을 위한 EDA 도입기
- ksql기반 사용자 Slef Stream Processing 플랫폼 서비스
- 기술track2_비스타홀2
- 15:10 ~

### 머니한잔
- 11번가 마이데이터 서비스
- 내소비/내자산, 소비태그, 예산관리
- 금융, 쇼핑, 공공, 의료 등의 개인 데이터를 통한 초개인화 데이터 플랫폼

### Architecture
- 서비스계
  - 도메인별 역할 분리 및 의존성 최소화
  - e.g. 인증에서 에러가 발생해도, 서비스, 관리서비스에 영향 x
  - GRPC 도입
- 분석계
  - 데이터 기반 개발 환경 구축

### 데이터 기반 개발 프로세스를 위한 Mock 시스템 구축
- 데이터 생성, 검증 비용을 줄이기 위해
  - MyPDS로부터 데이터를 가져옴

### 작업 방향성
- AWS 기반 인프라, 오픈소스 적극 도입

### 서비스 개선을 위한 EDA 도입기
- 사용성 vs 규제 준수(마이데이터로부터) 가치 충돌
- **EDA(Event Driven Architecture)**
  - Producer, Consumer가 Event Broker를 중심으로 동작
- EDA 설계 목표
  - 서비스, 내부 시스템 EDA 분리, 상호 영향도를 낮게
  - 한쪽에 이슈가 발생하더라도, 다른 시스템에 영향을 미치지 않음

### EDA 개발 과정

#### 이벤트 정의
- User Event, System Event
- event key를 가지고 일관성 유지
- event를 sub key를 받을 수 있도록 하여 성능 충족
- User Event Definition
  - 복잡성이 높음
- Event Map
  - 이벤트 역할, 종류, 전파의 역할을 나타냄
  - 이벤트 flow 이벤트 map에서 고민이 되었던 부분
  - **정책 복잡도**
    - 각 영역에 최소한의 역할, 정책 복잡도를 한곳으로 집중
  - FE, Server, EDA가 각각의 책임을 가져 관리하는 방향
- Event Management

#### Event Flow
- kafka `collection, classification, analysis` topic
- Event broker는 kafka기반의 MSA resource 활용

#### Event Consumer Tuning
- Heavy event
  - 많은 데이터량 의존
  - n개의 기관에 영향을 받음
  - Consumer의 성능 저하
  - kafka rebalance
  - kafka topic의 무게 이벤트
  - 데이터량이 많고, source가 많아, 상대적으로 불안정적
    - 실패할 가능성이 높음
- Event Fail & Retry 전략
  - 빠르게 실패하여 재시도하는 방향으로 해결
  - 데이터 수집시 항상 성공 보장이되지 않음

### 이슈
- Event Fail & Retry시, 오히려 외부 부하가되어 장애 전파가 나지 않을까?
  - Retry capacity를 정하고 그에 맞게 요청을 한다고 함
  - 이벤트 정책을 두어 과한 부하가 일어나지 않도록