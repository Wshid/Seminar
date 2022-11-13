# Elastic Stack 6.5 Webinar
- 18.12.13 13:00, [Ealstic Stack 6.5 Webinar](https://www.elastic.co/kr/webinars/6-5-release-webinar?ultron=dec-2018&blade=newsletter&hulk=email&mkt_tok=eyJpIjoiTm1aaVpXTTJNVGxqTjJNMyIsInQiOiJJckhkdmcyTTJ0cTZHWHR0VEg5Z0FvWjYwSVVJcko3ejRkSm5GNGowU2oxeG9MT0NNSnhydzNPOG82RUZnRXE4SHZ2eDIwbURqZjgwV1Q1dW9tYWhjNk9EVFRmZDdhczY1bzBCUGMzZ2lYR0IzeEVENEF6VElPTis1bmhISE5POCJ9)
- Written By. wshid(kso4013@gmail.com)
---

### ElasticSearch
- Cross-Cluster Replication
    - Beta / Platinum
    - 한 클러스터의 인덱스를, 다른 클러스터로 이중화
    - 오버헤드가 적고, 추가적인 자원이 필요하지 않다고 함
        - 기존에는 logstash, re-index등의 작업을 거쳤음 
    - 실시간 lookup
        - 인덱스 변화 삭제등을 로깅할 수 있음 
    - snapshot 추가 공간등의 문제가 발생하지 않음 
    - **Disaster Recovery**
        - 대체 시스템 복구 
    - **Data Locality**
        - 각 region별로 DC(Data Center)를 두어 분배할 수 있음
    - **Central Reporting Cluster**
        - 클러스터들에서 central에서 데이터 수집
        - 한군데로 모아 데이터 처리가 가능함 
- _source Only Snapshot
    - Beta / free
    - 전체 full snapshot대비 50% 정도의 저장공간 차지 
    - snapshot을 restore하는데 재색인을 필요로 함
    - **긴 복원 시간과 적은 저장공간의 treadeoff**
    - segment를 복사하는 것이 아닌, source만을 복사하여 사용
    - restore할 때, 재색인과 동일한 작업을 진행 -> 시간이 느릴 수 있음 
- ODBC Client
    - Alpha / Platinum
    - 6.3부터 Elasticsearch SQL 지원 
    - ODBC 드라이버를 사용하는 서드파티 프로그램에서 ES 데이터 사용 가능
- Java 11
    - Java 10+이상에서 지원되는 **G1 GC**를 지원
    - G1을 쓰더라도, heap Mem을 크게 지정하는 것을 **권장하지 않음**
        - 일반적으로 지정되는 heap size로 되도록 설정할 것 
- Authorization Realms
    - Platinum
    - 한 영역(realm)에서 인증을 하고
        - 다른 영역에서 권한 실행
    - 한 realm에서 가져온 정보를 다른 realm에서 이용하여 인증 가능
        - kerberous에서 가져온 정보를 LDAP에서 처리하는등
- Structured Audit Logging
    - Gold
    - 감사로그 형식 변경
    - 일반 메세지 형태 -> JSON
- Independent Scoring
    - ML 이상징후 점수 계산 방식 변경 
        - 전체 영역 -> 파티션별 
    - 범용 공식이 작은 파티션에 적용된 임시 공식을 **덮어 씌우지 않도록 변경**
    - 이상징후 점수는 더 이상 bucket spans에 고정되어 종속 x
    - 여러 bucket에 걸쳐 나타나면 -> 더 큰점수를 부여 
    - 여러 bucket에 걸쳐 나타나면, 더 높은 레벨로 나타남 
- Data Bisualizer for Files
    - Experimental / Basic
    - 데이터를 파악하기 위한 머신러닝 기능 
    - 파일을 업로드 하면,
        - 파일 구조 파악
        - 인덱스, 인덱스 패턴 저장 
    - 추후 계속 개발 중

### Kibana
- Canvas : Create live pixel-perfect presentations
    - Beta / Basic
    - 기존에 있던 Data Visualization이 강화 됨
    - 대시보드를 만들 때 사용하는 도구 
    - 마치 Powerpoint를 만들듯이 디자인이 가능함
    - 데이터의 라이브 쇼케이스 기능 
    - 데이터에 대한 스토리 텔링 가능 
    - 실시간으로 수치 변화 가능 
    - ElasticSearch SQL 지원 
    - 플러그인을 통한 확장 가능 
- Space
    - Basic / Gold
    - visualization, dashboard 등을 space로 나누어 관리 
    - gold는 space별 접근 관리가 가능 
        - rold-based access control 
- Rollups in Kibana
    - 원시데이터를 집계하기 위해, 집계성 데이터로 변환하여 저장하는 기능 
        - 데이터 저장을 더 효율적으로 할 수 있음 
        - 데이터가 적어지기 때문에 aggregation query가 빨라짐
    - 자동으로 데이터 집계가 필요한 부분만 **roll up**하여 저장


### Beats
- Beats Central Management
    - Beta / Gold
    - Security와 연관됨 -> Gold 부터 
    - Beats의 config파일을 **kibana**에서 직접 관리가 가능 
    - 특정 태그를 활용하여 관리가 가능함
- Functionbeat
    - Beta / Basic
    - function 레벨로 동작하는 beat
        - serverless 환경에서 동작 
    - 클라우드의 스트림 데이터를 ES로 색인이 가능함 
    - 아직은 AWS Lambda만 지원, 추후 플랫폼을 늘려갈 예정 
        - **AWS CLI** 커맨드로 설치 가능
- Heartbeat GA
    - HeartBeat을 이용한 모니터링의 생성, 업데이트 삭제가 쉬움 
    - Docker/Kubernetes 지원 

### Logstash
- SNMP Input Plugin
    - SNMP 장비로부터 데이터를 중앙에서 polling
    - 네트워크 모니터링에 적합 
- App Search Output Plugin 
    - 중앙 ETL 도구로 이용,
        - Elastic App Search에 배포 가능 
    - UseCase
        - 사용자 정의 ES 데이터를, ES app search로 Migration
        - Queue, Database & 원시 저장소 데이터를 
            - Elastic App Search로 전송 

### Infrastructure UI
- Infrastructure Solution
    - MetricBeat와 연동되는 UI
    - 인프라 기반의 동작 데이터를 한 눈에 볼 수 있게 큐레이션 하는 도구 
    - Kubernetes, Docker 시스템을 지원 

### Logs UI
- Logs Solution
    - Filebeat와 연계 
    - TS를 위해, 로그 파일을 원시적으로 볼 수 있는 뷰어 
    - 라이브 로그 스트리밍이 가능 
        - `tail -f`와 유사 
    - 과거 로그에 대해 무제한으로 스크롤링 가능 
    - 검색 가능 

### APM
- Java & Go Agents GA 
    - Java와 Go언어 Agent 추가 
    - Servlet API, Spring Web MVC, Spring boot.. 등의 다양한 프레임 워크 지원 가능 
- Distributed Tracing
    - Beta / Basic
    - 분산된 데이터를 한 곳으로 모아 확인할 수 있음 
        - Waterfall 형식으로 가능 
    - Microsoft Architecutre에서 활용 가능 
    - 어떤 메뉴가 실행되면서 끝날 때까지 
        - 연동된 기능들, 및 시간, 성능 파악이 가능하다.
    - OpenTracing과 호환
- Elastic APM Monitoring
    - Elastic APM에 대한 모니터링 가능 
    - rates, events, requests 등을 포함 
- Intake v2
    - APM Agent에서 전송되는 데이터를 개선 

### 7.0 Preview
- [Elasitc 7.0](https://www.elastic.co/v7)


### Demo
- 현재 최신, 6.5.3버전이라고 함
- Kibana 실행 
    - 시작시 sample로 테스트 확인 가능 
- Basic 좌측 메뉴 추가
    - Machine Learning
         - Data Visualizer 사용 가능
    - Canvas
    - Infrastructure
- Data Visualizer
    - 파일을 업로드 하면 
    - 헤더를 자동으로 인식함 
    - 필드 변환까지 가능 
    - 아파치 로그 업로드 시,
         - 어떤 grok pattern을 사용하면 좋을지 추천 가능 
         - date 필드에 따라 포맷 파악 가능 
         - sampling한 데이터 import 가능 
    - import
        - advanced menu 
            - ingest pipeline 확인 가능 
- InfraStructure UI 
    - 특정 metric을 선택하여 사용 가능
    - metricbeat 환경 설정
            ```
            # 환경설정, kibana에 데이터 추가(ES)
            ./metricbeat modules enable system`
            ./metricbeat setup
            ./metricbeat -e
            ```
        - kibana에서 확인시, 여러 Dashboard 확인 가능 
    - Hosts
        - Docker/Kubernetes로 탭을 전환하여 확인할 수 있음 
        - 메모리 사용량, CPU를 선택하여 확인할 수 있음
- Logs
    - Filebeat을 사용하여 연동 가능 
    - Filebeat 환경 설정 
        - Nginx에 대해 로깅을 하고 싶을 때,
        - Logs를 클릭 후, Nginx를 선택
        - 메뉴얼에 따라 플러그인 설치 
            - ES를 종료 하고 실행해야 함
    - filebeat, 모듈 실행 
        ```
        ./filebeat modules enable nginx
        ./filebeat setup
        ./filebeat -e
        ```
        - **.kibana**에 정보가 업데이트 되는 것을 확인할 수 있음
    - DashBoard를 확인할 수 있다.
    - geo-ip 플러그인 설치를 통해,
        - Dashboard에서 전세계 접속 상황을 확인할 수 있다.
    - 이후 logs에서 filebeat로 들어오는 로그를 raw형태로 확인 가능 
        - 검색도 가능하다.
- Space
    - Management/Space에 위치 
    - space 생성이 가능 
    - space에 따라 workspace가 분리된다. 
- Canvas
    - 단순 숫자처럼 보이지만, 실제 데이터로 처리됨 
    - 실시간성으로 데이터가 변동될 수 있음 
    - 우측에서 해당 데이터가 어디로 부터 왔는지 파악할 수 있음