## 이상거래 탐지를 위한 실시간 데이터 처리와 금융 사기 행동 분석
- link : https://if.kakao.com/session/82

---

## 1. 이상 거래 사례와 FDS
- 피싱으로 사용자에게 사기를 침
- 사용자는 피싱범에게 **송금/물건 구매**
- 이상거래 fraud
- FDS(Fraud Detection System; 평소와 다른 금융 패턴 감지)
- ![image](https://user-images.githubusercontent.com/10006290/102004811-7e93da00-3d57-11eb-8672-cb05f30359eb.png)
  - REST API로 정상 여부인지 결과를 리턴
- 다양한 행동정보도 수집 진행
- RMS(Risk Management System)
  - 중요한 곳의 데이터를 실시간 수집하여, 잠재적 위험 관리

## 2. 위험 관리를 위한 RMS 프로젝트 개발기
- 초기 요구사항
  - 서비스와의 최소한의 디펜던시
  - 룰 + 모델 실시간 모니터링
  - 대시보드
- 구현 방식
  - 동시성 : Kafka/Akka
  - 확장성 : Akka Cluster/k8s
  - 실시간성 : Redis/Druid/Kudu
- 아키텍처
  - ![image](https://user-images.githubusercontent.com/10006290/102004854-e34f3480-3d57-11eb-8dda-9b658fce83bd.png)
- Rule Engine
  - ![image](https://user-images.githubusercontent.com/10006290/102004866-08dc3e00-3d58-11eb-87fb-b89f76be649b.png)
  - Rule Engine : 마스터 노드
  - Rule Farm : 클러스터 노드
- kafka로 들어오는 데이터 = 이벤트
  - Akka Stream을 사용하여 실시간 수신
  - Akka Typed(akka 최신버전?)
- 들어온 이벤트는 Rule Farm Shade로 들어옴
  - Akka Cluster Shading 기술 사용
  - 클러스터 상의 유일한 액터인 엔터티에서 카프카 파티션별로 이벤트를 처리할 수 있도록 함
  - Akka Cluster Shading + Kubernetes
    - `discovery` 설정으로 쉽게 구성이 가능함
- Entity는 전달받은 이벤트에 대해 어떻게 체크할지 확인
- Cluster에 흩어져 있는 룰 액터에게 이벤트와 플랜을 보내 체크 요청
- 룰 액터는 클러스터 전반에 퍼져있는 컨디션 액터를 조합하여 이벤트 체크
  - ask pattern / feature sequence를 이용하여 aggregate
  - Filter Condition : 단컨 필터
  - Redis / Druid Condition
  - Model Condition : 지도학습/비지도학습 모델 사용
    - 모델은 Tensorflow 2.x로 구현되어 있음
    - 룰 엔진은 스칼라 환경이기 때문에 `Tensorflow for Java`를 활용
- 모델을 inference 하기 위해
  - Feature data 가공 과정 : Redis / AirFlow(Airflow를 통해 redis로 전달)
  - Model & Training : Tensorflow 2.x / MLFlow
  - Model inference : 이상 탐지 확인 / Tensorflow for Java

#### redis를 통한 feature data 저장 예시
- 데이터 구분
  - 통계성 데이터 : Hashes 를 사용
  - 히스토리컬 데이터 : Sorted Sets(ZSets, redis 자료 구조)
    - 시간 범위로 조회가 가능함

#### 다운타임 없이 모델을 교체하려면?
- ![image](https://user-images.githubusercontent.com/10006290/102005011-0f1eea00-3d59-11eb-8c38-35a50e475468.png)
- Akka typed라는 선행 지식이 있어야 함

#### RMS에서 주로 사용하는 DB
- Redis
- Kudu
- Mysql
- Druid : 실시간 통계 조회
  - 행태 데이터를 kafka, druid를 통해 realtime ingestion
    - druid table에 데이터 유입, 실시간 통계성 추론이 가능해짐
  - Roll-up 과정
  - RMS 대시보드 및 Rull Engine등에서 사용

## 3. 데이터 분석 여정
- FDS의 장단점
  - Unsupervised Model
    - 군집에서 벗어난 outlier 찾기
    - 새로운 행동 관찰이 가능하나, 명확한 평가 기준이 부재
  - supervised Model
    - 라벨링된 데이터를 가지고 모델을 생성
    - 목표값에 개입을 하기 때문에 비교적 높은 정확도(사람 개입)
    - 라벨링 데이터를 모으기 어려움
    - 변화하는 사기 방법에 대한 대응이 어려움
  - Rule Based Model
    - 특정 조건을 기반으로 만듬
    - 빠른 대응 및 이해가 쉬움
    - 구체화된 조건으로 유연성이 떨어지며, human resource가 큼
  - 어떠한 하나의 모델을 활용해서 작업이 어려움
  - 위 세 모델뿐 아니라, 히스토리적으로도 파악하기

### Relation 관점에서의 데이터 분식
  - Graph Network, User-account(계좌) 관계
  - 페이 머니라는 단위가 있으나, 실제 돈 입금/출 관계는 계좌에서 발생
  - 계좌 네트워크를 기반으로, 블랙리스트 계좌 파악이 가능
  - Account Network
    - 어떤 계좌를 기준으로 threshold를 넘었을 경우 그래프 edge로 표현
    - 성질히 비슷한 계좌끼리 클러스터가 이루어짐
    - 사설 도박 계좌 등의 생성/소멸 주기가 존재함
  - 생성 소멸주기를 파악
    - 계좌가 사라지면, 그 사람들도 사라질지 => 새로운 이상목적 계좌를 신설함
    - Account Immigration
      - 급격히 소멸되는 계좌 = 급격히 생성되는 계좌
    - 유저의 이동 및 유저 하락/상승률을 비교하여, 비정상 목적 계좌 추적이 가능함
    - 일단위 적재로 파악
  - ![image](https://user-images.githubusercontent.com/10006290/102005231-9587fb80-3d5a-11eb-9ce4-fa9833fc6123.png)

### History 관점에서의 데이터 분석
- 기존 행동과 다른 행동을 보일 경우
- Helix Structure
  - 3차 이상의 구조를 나타내기 위함
  - DNA가 대표적
- Spiral Steps
  - 나선 계단의 1바퀴는 24시간
  - 나선 계단을 오르면서 하루 히스토링
- ![image](https://user-images.githubusercontent.com/10006290/102005282-fa435600-3d5a-11eb-9ba5-464dc2756a83.png)
  - 지면과 멀어질수록 과거
  - 거래 타입별 색상을 달리 표현
- 유저의 주요 거래 시간(Main Trasaction Time)
  - 같은 시간대의 경우 같은 위치에 존재(시간단위)
- Rapid Transaction, Transaction Speed
  - 최대 연속거래, 연속거래 횟수 빈도 파악
- ![image](https://user-images.githubusercontent.com/10006290/102005304-23fc7d00-3d5b-11eb-9753-e110c637d3b9.png)
  - pyspark를 통해 helix structure를 구현
- 거래의 최근성, 빈도, 거래 규모를 단순히 처리도 가능했었으나
  - 정보를 구체적으로 추출하고, 시각적으로 보여주기 위해 구현하게 됨