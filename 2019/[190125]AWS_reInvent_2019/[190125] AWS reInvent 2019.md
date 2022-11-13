# AWS re:Invent 2019

- 개발자들이 원하는 4가지 클라우드 동향

        1. 컴퓨팅 - 다양한 선택의 폭과 낮은 개발 비용
        2. 요구사항에 맞는 db선택의 자유
        3. 좀더 자동 및 실용적 ai 플랫폼 제공
        4. 미래산업에 대한 진입 장벽 철폐


## Amazon DynamoDB 신기능 - 온디멘드와 트렌젝션 - 윤평호 (AWSKRUG)

### 목차

    - 온디맨드 용량 방식

            1.  소개
            2.  기존 방식과 비교
            3.  언제 사용하지?

    - 트랜젝션 지원

            1.  왜 필요하고
            2.  어떻게 사용하고 사용예를 보고

### 다이나모디비 

    -   규모에 상관없이 완전히 관리되는 비관계형 데이터베이스
    -   비즈니스 로직에 좀 더 집중할 수 있다.

### 다이나모디비 온디맨드

    -   데이터베이스 용량 산정

        -   조금더 능동적으로 용량 산정이 가능해짐

    -   관계형 데이터베이스에서는 일반적으로 Scale-Up을 이용

        -   용량을 Scale-Up하는 과정이 RDB에서는 굉장히 어려운편
        -   NoSQL에서는 Scale-Out 방식을 이용한다. <br>RDB보다는 비교적으로 쉬운편

    -   다이나모디비(DynamoDB, Managed NoSQL database) 특징

            -   서버리스 : 관리할 소프트웨어나 서버가 없음
            -   대규모확장성 : 원하는만큼 많은 처리량을 원할때
            -   고성능 : 일관된 1자리수 밀리세컨트 응답

    -   온디맨드(용량)

        -   기능
        
                -   용량 계산, 계획하거나 예약할 필요 없음
                -   사용한 읽기, 쓰기 개수만큼 과금

        -   장점

                -   용량을 낭비하거나 부족함이 없음.
                -   트래픽에 따라 즉각적으로 작업 부하를 자동 조정

        -   기존 용량 산정 방식과 비교

                -   Provisioned / Auto-scale : 하한값을 설정하고 최대 소비량을 관리
                -   On-Demand : 0에서 시작하며 제한없이 조절됨

        -   요금
            <br>서울 리전은?
                <br>-   $1.3556 쓰기 요청 100만개당
                -   ...

        -   언제 사용하나?

                -   예약용량(Provisioned) 
                    <br> 싸다. 라이브 운영은 이것으로 주로사용하고 Burst capacity를 유용히
                    <br> 예상가능한 정도라면 이것을 사용하는것이 좋음
                    
                -   오토스케일(Auto-Scale)

                    -   스케일업 하는 지연시간이 존재
                    -   지정한 사용율을 유지하지 못하면 스케일링이 안됨
                    -   상한 하한을 정할 수 있으나.. 

                -   온디맨드
                    -   서비스는해야겠는데 , 사용량이 측정불가능할때 이용


###   다이나모디비 트랜젝션

    -   중요한 이유? : 트랜젝선 API로 실생활의 트랜젝션을 (쉽게) 구현할 수 있다.

        -   동시다발적 쓰기, 여러 아이템을 갱신
        -   처리하기전에 여러가지 조건 검사
        -   다양한 테이블 간에 데이터의 일관성 유지

    -   1번의 API호출로 여러 작업이 가능하다

        -   데드락 X
        -   긴 트랜젝션 시간 X
        -   열린 트랜젝션 X
        -   관리되지 않은 동시성 x
        -   DBA의 괴로움 X
    
    -   추가된 트랜젝션 API

        -   TransactWriteItems

            -   작업을 10개까지 묶을 수 있음
            -   조건 추가 가능
            -   모든 조건이 맞을 때, 쓰기(Write)작업 수행

        -   TransactGetItems

            -   작업을 10개까지 묶을 수 있음
            -   일관되게, 모든 항목의 격리된 스냅샷을 가지고 옴

    -   다이나모디비에서 ACID 지원은?

        -   이전에는 단일 아이템 연산만 지원했으나 이제는 다중 아이템 연산에도 지원함

    -   NoSQL 트랜젝션의 성공 경로?

    -   트랜젝션 예

    -   트랜젝션이 실패하는 경우?

        -   아이템 단위로 실패하는 경우

            -   사전 조건 실패
            -   읽기/쓰기 용량 부족
            -   트랜젝션 충돌

        -   다른이유

            -   트랜젝션 진행중
            -   서비스 에러
            -   잘못된 구성한 요청 
            -   퍼미션

###   사용 예

    이미지 삽입 예정

###   트랜젝션 비용?
    트랜잭션은 비트랜젝션에 비해 2배의 일을한다.
    트랜젝션은 작업당 2배단위로 읽기/쓰기를 사용한다.

    
## Amazon Timestream - 시계열 데이터 전용 데이터베이스 소개 - 변규현 (모빌리스트)

-   Time Series Data? : 시간을 중점으로 나열되어있는 데이터!

-   일반적인 데이터베이스 종류는? 
    -   RDS(RDB)
    -   DynamoDB(Key-Value)
    -   ElasticCache(In-Memory)
    -   Neptune(Graph DB)

-   Time Series Data는 시간에 따라 변하는 데이터, 그리고 우리 주변에 있는 것들을 분석하는데 아주 중요한 역할을 수행한다.

-   이전에는 Time Series Data를 어떻게 저장했을까?
    RDB로 저장한다고 가정할 경우
    ERD를 그리고 DDL DML을 사전에 습득하고 있어야 한다.
    Row와 Column으로 저장 - 엄격한 데이터 스키마
    RDB는 결국 TSD에 적합하지 않다.
-   시간은 TSD에서 최우선으로 두는 단 하나의 축
    -   시간과 하나 이상의 속성으로 이루어짐
-   insert와 read에 성능이 중점적으로 집중되어있음
-   update등과는 적합하지 않다.

-   Amazon Timestream TSD를 지원할 amazon에서 새로 출시한 강력한 db
    RDB와 비교하여 1000배의 속도 1/10의 가격이다.
    매일 수조개의 이벤트 처리

    TSD에 최적화된 분석 : 평활화, 어림셈 보간과 같은 내장 분석기능

    다양한 데이터 스토리지 지원  : Memory / Magnetic / SSD

    무엇보다 ATS(Amazon Time Stream)는 Serverless이다.

    Time Stream의 이용처
        -   Monitoring
        -   IOT sensors
            실시간으로 수집하여 서버에 수집/저장하는데 이용이 가능
        -   Statics
        -   Signal Processing
        -   Pattern Recognition
        -   Finance
        -   Weather forecasting
        -   Astronomy

ATS 가격 정책
    Write
    Momory Store
    SSD
    Magnetic Store
    Query
    DataTrancfer IN - Free
    Data Transfer OUT - Over 1GB Data

## 블록체인 원장 DB를 클라우드로, Amazon Quantum - 김예본 (고려대)

QLDB != 블록체인
블록체인의 개념을 가져다가 사용하는 DB

-   QLDB를 사용해야 하는 이유

    -   블록체인?
        기존에는 중앙 DB에서 데이터가 관리됨
        블록체인에서 각 사용자는 Node로 지정

    -   SHA256
        현재 가장 많이 사용되고있는 해시 알고리즘

-   QLDB의 특징 및 장점
    -   Document 모델 구조
    -   SQL과 비슷한 API

-   QLDB 쿼리
    -   모델 구조(관계형 데이터 모델)
    -   문서형 데이터 모델 - QLDB

-   QLDB 세부 작동 방식

-   QLDB Closed Beta Testing중

## Amplify로 Neptune 그래프 DB 기반 모바일 앱 만들기 - 김현민 (메가존 클라우드)

WorkShop에서 만든 어플리케이션
    -   AWS AppSync를 활용한 API 연동
    -   AMAZON Neptune을 활용한 Graph DB 이용
    -   AWS Amplify

-   AWS AppSync
    실시간 기능 및 오프라인 프로그래밍 모델과 함께 GraphQL을 사용해서 어필리케이션 데이터를 관리하는 서비스
    -   실시간협업
    -   동기화 기능을 갖춘 오프라인 프로그래밍 모델
    -   유연한 데이터베이스 옵션
    -   세분화덴 보안? 제공

-   GraphQL
    페이스북에서 공개한 클아이언트에서 데이터를 쿼리를 통해 가져옴
     <br>   !=  Grph database<br>
    NoSQL, Relationla, HTTP등등 다양한 데이터 소스 사용 가능

    -   GraphQL Spec

    -   GraphQL Spec - schema
        <p>
        type Query {
            getTodos [Todo]
        }
        </p>
        <p>
        type Todo {
            id: ID!
            name: String
            description
        }
        </p>

    -   GraphQL Spec - Types

    -   Subscription


    -   Real time, batching, Pagination, Relations, aggregations, search, Offline 모두 사용 가능
    

    -   AppSync 개발 방식
        -   create and upload schema
        -   connect data sources
        -   appsync updates data in real time and manages

    -   AWS Appsync - 데이터소스, 인증등


-   Amazon Neptune

    완전관리형 그래프 데이터베이스
    
        -   빠른속도
            밀리초레이턴시가 필요한 수십억개의 관계형 쿼리
        -   신뢰성
            전체 백업 및 복원 기능을 갖춘 3개의 AZ에서 6개의 데이터 복제본
        -   사용하기쉬운
        -   오픈소스

    -   Neptune 사용 사례
        SNS, 추천시스템, 지식그래프, 사기탐지, 추천시스템, IT 네트워크 서비스

        관계에 기반한 추천시스템

        GREMLIN(그래프 순회 언어) 지원

    -   Neptune에 데이터 로드하기 


-   AWS AMplify
    모바일 어플리케이션 개발시 요구사항
        데이터보안
        빠른 성능 및 반응형 UX
        개인화
        빠른 데이터 접근
        오프라인 데이터 접근

    비즈니스 요구사항
        데이터보안
        데이터에 대한 가용성
        사용자가 이용하기 편하게
        사용자를 지속적으로 증가
    
-   모바일 앱 개발 시작
    프론트엔드에 집중하게된다
    비즈니스 로직? 백엔드? 전부 신경쓰다보면 너무 복잡해진다

    백엔드 관리하기
        관리하지 맙시다
        보안 및 확장이 용이한 서버리스 아키텍쳐 권장
        amplify toolchain
            amplify auth add
            analytics add
            storage add
            push
        
    
    AWS Amplify CLI
        백엔드 리소스를 프로비저닝 해주는 도구
        코드를 이용해서 설정

        amplify init

        amplify add auth

        amplify add analytics

    amazon cognito를 이용한 로그인
        로그인 개발이 까다로운 이유?
        사용자에 대한 식별
        권한에 대한 관리
        사용자 경험을 개인별로 다르게

    
    사용자 분석
        사용자에 대해서 더 많이 알 수 있고 사용자 참여를 유도할 수 있음

    -   로컬 아키텍쳐
        
            데이터를 저장
            데이터를 최신 정보로 유지
            메모리 누수 방지
            UI

    
## Lake Formation, 데이터레이크 관리와 운영을 하나로 - 이재성 (플레이오토)

    Data Lake란 무엇인가

    왜 클라우드에서 Data Lake 해야하는가

    AWS Lake Formation이란?

데이터 - 파일, 데이터베이스, 스트리밍 데이터

데이터 웨어하우스
    분산된 RDBMS에 저장된 데이터를 DW에 모아서 처리

커져가는 비정형 데이터

-   데이터 인프라의 요구사항
    
        Secure
        Real time
        Flexible
        Scalable

Data Lake

기존의 빅데이터 분석 시스템
    Hadoop Cluster 채용해서 사용

클라우드에서는 어떻게 하지?

클라우드에서 Data Lake
    Amazon S3
        내구성 99.99999999999%
        다양한 보관옵션(비용 효율적)
            Standard IA, OneZone IA, Reduce Redundancy, Glacier
        S3 select - s3에 직접적으로 질의 가능
        다른 AWS 서비스 연계성


AWS Lake Formation

데모에서 보지못했던 기능 - FindMatches
    유사한 그룹끼리 연결해줌
        Star Wars
        Star WArs: A New Hope
        Star Wars: Episode 4--A New Hope (Special Edition)
    같이 연관성이 있을만한 데이터를 연결해줌

    같은부류
    Amazon Forecast 예측 
    Amazon Presonalize 추천

결론
    - Lake Formation은 기존의 AWS 서비스를 묶은 통합 서비스다
    -   접근제어 암호화를 한꺼번에 해준다.
    -   FindMatches가 나와서 얼마나 효과적인지 테스트 해봐야한다
    -   이미 구축된 S3와 Glue를 Lake Formation으로 마이그레이션 해주는 기능은 추후에 제공할 예정