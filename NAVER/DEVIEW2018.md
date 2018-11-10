# DEVIEW 2018
- 18.10.11 ~ 10.12
---


1일차, 18.10.11 15:00
## 쿠팡 서비스 클라우드 마이그레이션, 16:00
---
* 서비스 이전 관련 사업
* scale-out을 위해 클라우드로 변환됨
* 무중단으로 이동
* 데이터 센터와 클라우드 동시 운영 - Roman Ride
    * 만료된 서비스부터 먼저 이동한다
* Dynamic Routing
    * Rapid Rollback
    * App 내부에서 연결을 옮기게 설정

---
2일차, 18.10.11 16:00
## 10:00, Track1, 인공지능이 인공지능 챗봇을 만들다
---
* 챗봇이란?
    * 챗봇은 성능도 안좋은데..
    * 챗봇 시장은 커지고 있음
        * 대부분 회사가 사용하고 있음
    * 일반화 하기가 어려움
* 형태소 분석
    * Vector Representation
    * Embedding Vector
* 챗봇에서 말뭉치를 만들고 모델을 학습 시킨다
* 진화된 인공지능 프로그램(autoML)
    * 몰라도 설계 가능
        * 인공지능 기술 - 머신러닝 프로그램 = parent : child
* 2.6 Auto ML
    * 모델 선택, 파라미터 최적화, 모델 평가를 계속 반복하게 됨
* 3.1
    * 말뭉치는 매우 중요
* 3.2
    * 알고리즘 선택부터 모델 평가까지 모두 자동화
    * 직관에 의존하는 모델링
        * NLP관련한 모델은 상당히 많음
        * 아무리 해도 논문처럼 결과가 안나온다.
            * 학습은 되는데 성능은 안나오고..
                * 말뭉치 바꾸고 파라미터 바꾸고..
            * 위와 같은 문제를 AutoML로 해결 가능
* 3.2
    * Ensemble Model
        * 같은 모델이지만 parameter를 다르게 합성한것
* 3.3
    * 심심이 데이터를 가지고 학습
    * 모델에 들어갈 hyper-parameter의 양도 많음
    * 총 걸리는 시간만 5,000시간..
* 3.3
    * 말뭉치를 효율적으로 관리하게
    * Clusteing
    * 그리고 Hyper Parameter Tuning
* 3.4
    * 들어온 말뭉치 Clustering
    * Model Tuning
* 3.4. Chatbot Module
    * seq2seq 사용
    * 말뭉치 도메인으로만 N-bot
    * 글로벌 임베딩 벡터, global embedding model
    * qq모델 ( text - to -text)
    * 위 세가지 모델 emsemble
    * N-hot input model
        * 밥 / 을 / 먹다 - Token을 나누는 Token index
        * TOS 정보(동사, 명사 정보)
        * 어미 정보
            * 어미 복원을 위해, 다른 feature로사용
    * 여러 모델의 답변을 emsemble
        * akka를 사용하여 멀티 플랫폼 제공이 가능함
* 4.
    * 스스로 말하기 실력을 평가한다.
* 4.1
    * 모델 평가를 사람이 직접 한다고 함..?
        * 1,2,3,4점으로 나누어 처리
* 4.2.
    * 그래서 자동평가를 사용
    * 정답 : 정말 포함해야하는것
    * 오답 : 포함하지 않아야하는데 정말 포함하지 않는지
    * 클러스터링 평가를 MSE를 사용
        * MSE를 최대화 한다
            * 군집간 분산 최대화, 군집내 분산 최소화
            * 물론 기준에 따라 해당 기준은 다를 수 있음
                * 다른 곳에서는 MSE값이 작아야 좋을 수도
* 4.2 Hyper Parameter
    * 모델을 하나 둘 씩 바꾸어, 테스트
    * Validation Set으로 빼논것을 가지고 테스트 한다.
    * 코사인 유사도와 MSE 사용
        * 코사인 유사도 : Vector Size
            * 인공지능이 벡터 공간 사이즈를 직접 결정
            * 벡터의 크기보다 벡터간 거리측정
* 5.2
    * 가우시안 분포 사용
    * cluster k를 잘 찾아주지만, embedding vector가 정확해야함
    * Locality Sensitive Hasing
        * 쿼리간의 유사도는 잘 판단하나, 삼단논법이 성립하지 않음
    * K-means
        * 최적화 함수 거리기반
        * 결국 k-means 사용
* 5.3
    * 유사도 판단
    * spark을 사용하여 대용량 처리
    * Lexical Variance in Queries가 가능해짐
* 5.5
    * 여러개의 모델 사용했으나, 결국 seq2seq
* 5.6
    * Hidden size vector
        * 작은 것에 매핑이 될 수록, 작게 매핑이 가능
        * 많은 시나리오 매핑시, 크기를 늘려줘야 함


## 11:00 track1, Hadoop Cluster
---
C3 v2
* docker를 지원
* 1.5
    * 하나의 단일 클러스터에서 여러 서비스 구동시의 장점
    * 네이버의 많은 서비스, C3 Hadoop Cluster
* 2.1 : Hadoop Yarn
    * RM, AM
    * AM에서 필요한 리소스를 RM에게 할당 받음
    * 그 이후 Node Manager에서 실행
        * AM을 어떻게 구현하는야에 따라 성능이 결정됨
    * Yarn Application에 대해 알아야 함
* 2.2 : Apache Slider
    * Sliderize
* 2.4 Docker
    * sliderize보다 dockerize에 익숙한 개발자들
        * docker image로 만들기
* 2.5 Docker on Slider
    * Slider Agent에서 Docker를 실행하게 끔함
* 3. 문제점에 관한 이야기
    * 3.1 : resource isolation
        * 할당된 만큼만 사용하도록 isolation 해야함
        * cgroup을 사용하여 처리
        * docker 하위의 cgroup으로 동작하기 때문에, YARN의 제약을 받지 ㅇ낳음
            * croup-parent를 두어 해결
        * 메모리 관리 포인트가 2가지가 되어버림
            * docker단 설정에서 처리하도록
    * 3.2.: Reliability : 안정적으로 실행되도록
        * RM이 다운되더라도 work-preserving하게
            * 다른 노드로 사용하게 됨
        * RM all down시
            * 실행중인 컨테이너는 유지
            * 새로운 리소스 컨테이너는 할당 불가능
        * RM이 지속 복구되지 못하면
            * yarn.resourcemanager.connect 라는 옵션을 통해서, n분간 m간격으로 재시도 후 종료된다
            * 컨테이너는 유지
        * RM와 NM 사이의 네트워크 장애
            * RM에서 기다리다가 KILL
            * 복구 되더라도 NM으로 컨테이너 중단
            * 네트워크 장애 대비
        * work preserving AM restart
            * AM 종료시, 복구 과정
        * AM retry window
            * AM retry-window 일정 시간 동안의 실패 횟수만 고려
            * default를 하게 되면 한번만 실패해도 죽는 버그, fixed됨
        * AM 실패 대비
            * 현재 5정도를 retry 수로 설정하였다고 함
    * 3.3. Scheduling
        * Node partition
            * 권한별 분리
            * 리소스로 구분
        * Slider Placement Policy
        * 데이터 재사용 문제
            * anti-affinity 옵션에 의해, 이전 내용의 복구를 안할 수 있음
            * 해당 옵션을 개선하여 default와 같이 사용했다고 함
        * Fragmentation
            * 골고루 할당되도록 yarn에서 처리는 하지만, 하나으 컨테이너가 남은 노드의 사이즈보다 클때
                * Reservation을 하게 됨
                    * 노드의 사용량이 줄을 때까지 60G가가 대기하게 됨
            * 컨테이너 최대 리소스를 가능한 작게 설정
                * 불필요한 큰 컨테이너를 요청하지 못하도록 제한
    * 3.4 Upgrade / Reconfigure
        * client가 특정 컨테이너의 업데이트 요청
        * 기존 종료 이후, 새로운 컨테이너를 실행
        * 컨테이너 재할당시 오래 걸림
    * 3.5 Usability
        * Slider
        * ... UI 앱 구성


## 12:00, Track3, 대형 컨테이너 클러스터에서의 고가용성
---
* 네트워크 로드 밸런싱을 어떻게 할 것인가
    * 패킷의 부하 분산에 대한 이야기
* 1. 대규모 컨테이너 Load Balancing
    * 여러 클러스터에 수천개 이상 클러스터 운영
    * Container Host = Linux Machine
    * 1.1
        * L4 Load Balancer
            * 여러 컨테이너로 부하 분산
    * 1.2
        * Dynamic Load Balancing
            * 수동으로 IP를 입력해줘야 했었음. 또한 추가/삭제/재시작 빈번
            * 정적으로 설정이 절대 불가능 했던 상황
            * IP가 사라진것을 LB에 할당을 해야함 // 컨테이너 삭제시
        * Large Scale Load Balancing
            * 분산 과정이 여러번 거처야함
            * 보통은 active/backup 구조
                * 그렇게 되면 active만 사용하게 됨
                * 따라서 active/active 구조로 사용
        * Load Balancer Down
            * 모든 Connection Down
    * 1.3
        * 확장성이 있으려면
        * docker swarm 이란 개념 등장
* 2. Large Scale Load Balancing
    * 2.1
        * 기존 L3 Routing의 경우, IP를 기준으로
        * L3와 L4까지 hop=2
            * 경로가 같고, ip가 같기 때문에, 한군데로만 몰리게 됨
            * L4단까지 와도 의미가 없게 됨
    * 2.2 : BGP/ECMP
        * BGP
            * L3의 라우팅 information이 적힘
            * 이를 동적으로 설정하게 함
            * IP Advertise를 통해 동적연결이 가능하게 됨
        * ECMP
            * router - L3 - L4..
            * path를 balancing하는 기술
                * src, dst, ...
                * 이 값들의 해싱을 함
                * 해싱을 한 후 L4LB에 대해 Modular 연산을 하여 동작
            * active/active하게 연결이 가능하려나?
    * 2.3 ECMP Hashing Disruption
        * Hashing Disruption
            * 최종적으로 N Modular 연산을 하는데
            * LB가 죽으면 다시 계산을 해야함(해당 노드 선택이 불가)
            * ECMP 특성상 해당 노드와 peering이 된 노드 모두 영향을 받음
        * TCP Connection Loss
            * 한 노드가 죽은 후, 다른 LB로 포워딩 할때
            * 기존 Connection을 끊고 새로 연결하게 됨
                * 기존 연결이 다 끊김
                * 장애가 난 것 외에 ECMP Hash Disruption에 의해 모두 영향을 받음
    * 2.4 고가용성 Load Balancer
        * L4 LB가 죽어도 정상 운영이 되어야 함
        * LB1로 가다가 LB2로 가더라도 영향이 없어야 함
        * BGP/ECMP - L3 Load balancing 기술
            * 아직 Hash 문제
    * 2.5
        * Docker + BGP/ECMP + High Availability
* 3. 고가용성을 위한 Consistent Hashing
    * 일관된 해싱
    * 3.1.
        * src ip hashing
            * modular 연산을 하는 것이 LB
        * 컨테이너가 4로 늘어나는 순간..
            * LB가 바뀌어도 원래 물리던 Connection을 유지할 수 있도록 해야함
            * 안됨
    * 3.2.
        * n의 변동 -> K/n만큼의 diruption
            * n은 dst, container
        * 각 IP는 Connection은 하나
        * Hash Ring을 구현한 것
        * 효율적인 LB가..
    * 3.3 Maglev LB
        * Google의 Network Solution
        * 중간에 있는 0.0.0.1이 죽더라도 다른 곳이로 이동할 수 있도록 함
        * Consistent Hasing이 가능
    * 3.4.
        * Permutation
            * backend(dst)
            * 선호하는 index table
        * Population
            * B0가 처음 3번으로 들어가고 싶어함
            * lookup table을 보고 3번에 할당
            * B1을 보고 0번 할당
            * 들어감
            * B2는 b0가 선점 당하여 해당 못함
                * 다음 index에 따라 4번에 위치 시킴
            * lookup table을 다 채울때까지 반복
        * Load Balancing
            * 거의 동등한 backend selection이 가능하게 함
            * 물론 완벽한 balancing이 아님
        * minimal disruption
            * B1의 컨테이너가 사라진다면,
            * B1을 제거한 테이블 생성
            * B1이 있었던 자리만 B0로만 대체
                * 대체 된 것 이외에 나머지는 그대로 유지
        * Disruption 비율
            * Lookup Table의 크기가 클 수록 Disruption 감소
            * 메모리와 trade off
        * 단, 모든것을 구성해야 함
* 4. IPVS Maglev Hashing Scheduler
    * 4.1
        * IPVS : IP Virtual Server
            * L4에서 동작하는 SW LB
        * Linux Kernel, NetFilter를 사용
            * Network Filtering
                * iptables
                * ipvs
        * 리눅스 커널에 들어가기 때문에
            * 재부팅
                * 메모리 재로드
                    * 오류 발생 시
                        * 다시 수정
                            * 재부팅
                                * ...
            * IPVS scheduling을 모듈로 제공
    * 4.2
        * Pluggable하게 선택이 가능
        * 스케줄러를 선택하여 설정이 가능
        * 모듈 컴파일을 진행하면 됨
        * interface 예시
        * IPVS의 변경 없이 Maglev만 컴파일
    * 4.3
        * 테이블 사이즈를 키우면 메모리와 trade off
        * 리눅스의 경우 임베디드 장비에도 들어가기 때문에
            * 사이즈를 적게 설정할 수도 있어야함
            * 호환성 때문
        * Kconfig에서 table size 조절이 가능
        * weight 기반
            * canary release strategy
                * 일정 이상만 신규 버전 적용 및 운영
        * destination fallback
            * blue-green일때, 해당 가중치를 0으로 주면 됨
            * 그러면 그쪽으로 들어가지 않게된다
        * IPVS MH lookup Table
            * VIP가 10.0.0.1:80
                * 동일하게 설정
            * 그리고 dst 목록이 있음
            * 왼쪽과 오른쪽의 내용이 달라짐
                * (dst의 순서에 따라 변경된 것)
        * 5-Tuples Hash
            * 5개의 해싱을 하기 때문에, 기존 연결은 그대로 가게 됨
* 5. 확장성 있고 신뢰할 수 있는 Load Balancer
    * 5.2
        * No disruption
            * 등록 순서를 동기화 시켜버림
                * dst를 등록한 데몬이 하거나
                * IPVS에 sort를 하게 함
            * IPVS간의 dst 순서가 동일하게 됨
                * lookup table도 역시 동일하게 됨
        * 모든 packet은 기존의 목적지로 scheduling
        * No TCP connection Loss
            * 원래 2번으로 가던 패킷을 다시 forwarding하게 됨
        * HAshing 알고리즘 하나로 모두 처리하게 됨
    * 5.4
        * SLOPPY_TCP
            * TCP 3 handshaking에 따라 connection이 안되면 해제 시켜버림
            * 하지만 기존 연결을 유지시켜야 하기 때문에, 해당 옵션을 켜주면
            * 데이터 전송상황 그대로 유지시켜서 진행하게 됨

## 14:00, 네이버 검색과 개인화
---
* 개인화 검색
    * 조금 늦게 도입되게 되었다고 함
    * 페이스북에서는 개인화 서비스를 통해 긍정적이미지가 부각되었기 때문에, 사용자가 거리낌 없이 사용
* Filter Bubble
    * 개인화를 잘못했을 때,
    * 다른 정보로 부터 배제되는 현상이 있는가
* 개인화는 리스크가 크다
    * 하지만 해야한다
        * 검색 의도에 맞는 검색 결과를 제공한다.
        * 주식에 관련된 단어 검색시 차트를 동시에 보여주거나
        * 특정 아이돌 그룹을 선택하면 아이돌 그룹에 대한 영상을 연동해서 보여준다던가
* 같은 단어에 대해 다른 의도가 존재할 수 있음
    * 펜타곤 이란 단어에
        * 미국방성 혹은 아이돌 그룹 이름
* 맞춤형 검색 결과로 나타날 수 있도록 변경해 주어야 함
* 언제 개인화가 필요한 것인가?
    * 클릭엔트로피, 개인화 잠재점수가 높다
        * 클릭 엔트로피
        * 특정 섹션과 토픽에 따라 선택하는 정도
* 3 level query ambiguity
    * Query를 Entity에 여러개의 존재
    * 해당 entity의 property로 나뉨
        * slot혹은 Attribute와 동일한 의미
    * property에서 document 단위로 나누어짐
    * Query - Entity - Property - Document
        * Entity Level이 제일 Critical함
* Document level이 어려운 이유
    * 추측으로 판단해야 하기 때문
    * implicit하다
    * filter bubble이 적용되는 단계
* 개인 검색 의도 파악
    * sensory memory
    * working memory
        * 이 두가지는 shot-term memory라고 함
    * HuMM
        * 사람의 인지 과정과 유사한 모델
* 데이터가 sparse 하다
* 검색의도 파악 확장
    * User 기준 확장
    * Query Group 확장

## 15:00, Track1, Druid
---
* 데이터 분석의 필요성
    * 로그 분석
* 2. Druid
    * 컬럼 기반
    * Time 기반 partitioning
    * 2.2
        * 검색 필터링할 항목이 많음
            * 미리 계산하기가 어렵다고 함
        * ES
            * 주기적으로 index와 메모리를 관리해주어야 함
        * Kudu
            * 적은 공간 차지, 빠른 조회가 가능하나, 질의 기능 미제공
            * Kudu의 경우 Impala와 같이 사용했다고 함
            * kudu는 pk외에는 indexing을 하지 않음
        * Druid
            * 비밀 인덱스로 빠르게 처리할 수 있음
            * spark와 연동하여 사용했다고 함
        * D2, helloworld, kudu 포스팅에 자세한 정보 확인 가능
        * 응답시간 비교 실험
            * 1개의 필드 랭킹 분석시, 350ms로 빠르게 응답
            * sub ranking까지 구했을경우, Es, kudu+impala는 느렸다고 함
            * Secondary Index를 지원
                * PK외의 key에 대해서도 빠름
            * 복잡한 질의는 spark를 이용하였다고 함
* 3. Druid로 분석하기
    * keyStart만 보더라도 쉽게 구성 가능
    * 3.1 Imply-UI
        * druid 버전에 따라 업그레이드
        * 유료임
    * 3.2 grafana
        * 다양한 그래프 가능
    * 3.3 superset
        * 기본 제공 그래프가 많다
        * 권한, 통계 기능 내장
        * 표현의 자유도가 높은만큼 learning curve가 높음
    * 3.4 Metabase
        * 연동된지 얼마 되지 않음
        * 기능이 많지 않다
* 4. Druid 분석 고급
    * 4,1
        * 초록색 글씨가 tuning 부분
    * 4.2
        * Druid의 TopN 근사 알고리즘
        * 교환집합이 적용되지 않음
        * 연산의 수는 줄어든다
        * Historical을 다르게 조회하게 되면서, 교환 법칙의 결과가 나타남
        * 머지해서 반환하는 것이 아닌, 해당 결과를 broker가 취합하여 merge하는 방법으로 변경
            * replica를 늘리더라도 계속 일관된 결과가 리턴
        * 4.2.3 spark-druid-connector
            * queryType때문에 한정적으로 검색이 가능함
            * SQL로 분석을 하기 위해 SparkSQL 사용
            * locality
                * 압축을 해제 한뒤에 처리할 수 있게 됨
                * historical server를 spark cluster와 동일하게 구성
            * segment partition pruning
                * SQL을 분석하여 해당 날짜별 파티션만 읽도록 함
            * using druid-filter
                * 드루이드는 bitmap을 사용하여 빠르게 처리
                * 일치여부를 bitmap index로 처리하게 됨
                    * 비트 연산만으로 해결이 가능
                * spark에서 filter조건을 druid하게 넘겨, druid에서 처리하게끔 함
                    * fitler를 하게 되면 모든 연산이 다 올라와야 하지만, druid에서 올려서 처리하므로
                        * 그만큼 성능 향상이 이루어짐
            * Full scan의 경우
                * durid가 parquet보다 3배이상 오래 걸림
            * random access는
                * druid가 훨씬 빠름
    * 4.3
        * Hash Partition
            * 정렬된 순서로 저장하게 되면
            * shard pool의 확장이 가능
            * Single Dimension과 같이 구성하면
                * hash partition이 가능하다
                * hashPrunable을 ture로 두어 사용한다.
        * Druid Spark Batch
            * Druid는 기본적으로 MR을 사용한다고 함
                * 읽고, 쓰는데에 계속 IO 발생
                * 이렇게 되면 Hbase Rebalancing 문제등이 발생
            * spark indexing이 되도록 하는 링크
            * spark executor를 실행하여 처리하는 방식


## 16:00, Track1, Search Reliability Engineering
---
* 많은 사용자들이 접근해도 장애가 없어야 한다
* SRE
    * 일종의 문화,
    * 시스템의 신뢰성을 어떻게 유지시킬 것인가
* 지표 분석에 따른 service id를 발급
* 가용량 지표 개발
    * 가용량
        * MTTR : 평균 복구 시간
        * MTBF : 평균 무고장 시간
    * 한 노드가 에러가 나면 다른 노드에 해당 피해가 전파됨
        * 형제노드의 부하 증가
* 실제로 새로운 지표를 도입하여 계산, alert을 보내기로 함
* Macro Analyze(거시적 분석)
    * 각 서비스가 문제 가 있는지 모두 보이게 함
* Blackbox Monitoring
* 실제 사례 소개
    * 실제 지진과 같은 상황 발생시, 중복된 검색어들이 상당히 많이들어옴
    * 이 때 Cache Server의 역할이 중요