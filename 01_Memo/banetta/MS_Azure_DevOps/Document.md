# Microsoft Azure DevOps Community Launch
`#AzureDevOpsLaunch`

## 환영사
MS 의 github 인수로 인해 Azure DevOps와의 시너지도 증가하는중
작년 9월 Azure DevOps 출시와 함께 지금까지 계속 Community Launch를 진행함

한국은 늦게 시작한 만큼 좋은 내용을 많이 제공할 예정

[MS Learn](http://microsoft.com/Learn) 에서 무료 강의를 받을 수 있음

추후 발표자료는 이메일로 공유 예정

##  DevOps & Azure DevOps 소개, 동향, 그리고 기대효과
>  DevOps, 혹시 나만 빼고 다하고 있는건가?
> 
> 어떤 산업군을 막론하고 이제 비즈니스의 경쟁력은 소프트웨어의 개발 역량으로 귀결되고 있습니다. DevOps는 신속하고 지속가능한 소프트웨어를 안정적으로 출시하기 위한 대안으로 최근 급부상하고 있는 주제이기도 합니다. 이 세션에서는 DevOps의 개발 문화적인 측면, 조직적인 측면, 기술적인 측면을 고루 검토하고, 사내에 DevOps를 도입하기 위한 단계별 절차와 방법 그리고 그 기대효과를 함께 살펴봅니다. 더불어 Azure DevOps를 활용한 DevOps 실천 방법과, 지난 수년간 마이크로소프트가 DevOps를 조직내에 어떻게 안착하였고, 그 결과는 무엇이었는지에 대해서도 실사례를 통해 살펴보고자 합니다

- DevOps를 실제로 활용하기 전에 회사나 각지에서 왜 필요한지 질문이 온다면 이 내용이 중요하게 여겨질수도..?

-   Nearly 50% of the current S&P 500 will be replaced by 2026
    -   얼마나 회사가 살아남을지에 대한 지표로 20년만에 전 세계에서 가장 수익을 많이 얻는다는 회사들이 절반이 사라짐
    -   회사의 기업가치를 쭉 리스트업 하는 곳에서 절반이 사라질것으로 예측
    -   새롭게 떠오른 기업들은 과연 어떤 장점이 있길래..?
    -   특히 S/W Develop.. 모든 회사들이 이에 대한 역량을 가져야 할 것

-   무엇을 공부해야 살아남을수 있나요???
    -   세상은 모호함의 세상인듯
    -   계속 지속적으로 방향을 바꿔나가야할것 일단 발사하고 계속 과녁을 바꿈
    -   빨리빨리 Acelerate~
    -   가장 중요한 Fast Curve Ball 우리가 필요한 요소
        -   굉장히 빠르면서 회전이 빠른 것
        -   이에 필요한것이 바로 DevOps
-   S/W 출시의 패러독스
    -   Speed vs. control impedes innovation
-   S/W의 출시가 느리다?
    1. 배포 주기가 길다
       - 최종 사용자들에게 도달할 때까지 보통 6주가 걸림
    2. 개선하려면 오래 걸린다
       -   다시 개발하고 다시 테스트하고 다시 배포하는데 너무 오래걸린다.
    3. 변경시 실패율이 높다.
       -   버그를 수정하려면 굉장히 오랜시간이 걸림
    4. 복구 시간이 길다.

-   왜 느리죠?
    -   한번에 너무 많은 일을 해요
    -   불필요한 새로운 아이디어에 대해서 너무 많이 이야기 해요
    -   개발팀이 일을 끝내도록 놔두질 않아요
    -   감당 할 수 없는 프로젝트였어요
    -   팀원들에게 개발외의 다른 일을 시켜요
-   느린 이유들
    -   요구사항의 변화
    -   개발, 운영의 전문성, 역량 결여
        -   계속 계속 공부하게 됨
    -   협업, 소통의 결여
        -   굉장히 많은 부서와 팀들이 있는데 그들 간의 협업과 소통이 잘 안되기에
    -   자동화된 과정, 절차 체계 시스템의 결여
        -   회사내에 이런 시스템이 없을 때
-   무엇을 개선할 것인가?
    -   문화(혹은 가치관)
        -   회사가 처리하는 프로세스나 방법들을 모를 때?
        -   모르겠고 출시만 하면 됨! 마인드는 코드의 퀄러티가 떨어지는 요인중 하나
        -   개발자들이 가지는 문화의 잣대
    -   과정/절차
        -   개발을 한 후 적어도 로드 테스트를 하고난 뒤 출시할거야 혹은 운영 서버에 붙어서 제거하면되! 라던지 에 대한 중요한 절차
    -   도구
        -   개발 하는중 도움을 줄 수있는 도구가 있음
-   Dev Ops
    -   Dev
        -   Create
        -   Plan
        -   Verify
        -   Package
    -   Ops
        -   Release
        -   configure
        -   monitor
    -   이런 절차들의 연속
    -   이 절차들을 더 빨리 빠르게 진행하자!
    -   단순한 커뮤니케이션에 그치지 않고 문화, 과정/절차등을 통틀어서 
    -   DevOps / DevSecOps / DevSecOpsBiz -> Dev*Ops... ArchOps, DataOps, WinOps.... 파생된 말들이 있지만 제일 중요한 것은 SW개발 전체의 프로세스를 가속화하자가 중요한 포인트
-   DevOps를 적용하여 출시 가속화
-   DevOps를 도입하면 무엇이 좋아질까? 적용한 사례를 보니
    -   배포 주기가 46배 빨라짐
        -   6주(42일)이 하루에 한번 배포할 수 있을정도로 개선됨
    -   개선 속도가 440배 빨라짐
    -   변경 실패율이 1/5로 줄어듦
    -   복구 시간이 96배 빨라짐
        -   대충 반나절이면 복구 가능한 수준으로 빨라짐
        -   혹시 잘못되면 어떻해... 라는 두려움 떄문에 
        -   배포했다가 롤백하기 편하기 때문에 부담이 줄어듦
    -   시장 진입속도가 20%개선됨
    -   매출은 20%신장
-   What is DevOps?
    -   협업이야!, 자동화 방식이야!, 인프라 스트럭쳐 코드야!, 스윗치야! 등등
    -   다 틀린 이야기
    -   DevOps를 하면 콜라보레이션, 오토메이션을 할 수 있다!
    -   AWS : 철학, 사회 도구, 굉장히 빠른 속도로 개발하고 배포하는 것, 사용자에게 좋은 서비스를 제공하기 위해
    -   Wiki : practices, time, change to a system...
    -   redhat : 보안 협업, 데이타 분석,등등
    -   MS : DevOps is the union of people, process, and products to enable continuas ...
-   Technical Approach
    -   Plan & Track
    -   Build & Test
    -   Deployment
    -   Monitoring & Operations
-   DevOps화
    -   DevOps적 문화
        -   Trust, Share, then Collaborate
            -   Developers Operatiors 간 교류
        -   토대 구축
        -   ahslxjfld, fhrld fkffladmf rntjdgkf tn dlTEhfhr rncnr
        -   재사용 가능한 배포 패턴 구축
        -   테스트 패턴구축
        -   다른팀이 사용할 도구 개선
    -   실용적 단계
        1.  Normalize the technology stack
        2.  Standardize and reduce variability
        3.  Expand DevOps
        4.  ....
    -   DevOps를 시작할 때 고려할 사항들
        -   Source Control을 반드시 도입하자
        -   운영체제에 대한 표준화를 어떻게 할것인가?
        -   표준화된 기술 셋을 정의
        -   디플로이할 표준 ...
    -   DevOps적 과정/절차
    -   DevOps적 도구

-   DevOps Microsoft
    -   Azure DevOps Tool
        -   Azure Board
            -   plan을 만들고 트래킹하고 쉐어하기 위한도구
        -   pipelines
            -   언어 플렛폼 클라이언트등을 관리?
        -   repos   
            -   소스코드를 저장하고 PR을 관리하고 효율적으로 머지하고 커뮤니케이션을 할 수 있고
        -   test plans
            -   테스트도구를 자동화 하기 위한 도구
        -   artifacts
            -   패키지를 만들고 공유하고 추가한다
-   랙ㄱㄷㄴㅅㄷㄱ ㅊㅇㄲㅁ ㅉㅁㅍㄷ
    -   Leaders groups
        -   Elastic Cloud
        -   XebiaLabs
        -   IBM
        -   CA Technologies
        -   Microsoft
-   DevOps across Microsoft
    -   86000
        -   MS가 하루에 배포하는 갯수 즉 1초에 한번씩 SW를 배포함

-   Demo
    -   상당히 괜찮게 구성되있는듯
    -   elastic stack + github + zenhub 같은 느낌
    -   파이프라인 등
-   Azure DevOps Services Pricing
    -   오픈소스 무료
    -   소규모팀 (5명 이하) 무료
    -   10명정도 월 33000원 정도 대충 1인당 6천원 정도
-   Azure DevOps Server
    -   보안을 위해 클라우드가 아닌 로컬로 쓰고싶을 때에도 배포 가능

[Azure DevOps](https://dev.azure.com)




##  Azure DevOps, CI/CD를 위한 5개의 핵심기능 살펴보기
>  Azure DevOps의 CI, CD를 감당하실 수 있겠습니까? 어서 회사로 들이십시오<br>
> 
>이번 세션에서는 Azure DevOp의 5가지 주요 기능들을 살펴보고, 이러한 기능들을 활용하여 실제 운영중인 웹 애플리케이션의 지속적인 통합(CI)과 지속적인 전달(CD)의 기본적인 구현 예시를 다룹니다. 지속적인 빌드와 테스트를 결합한 지속적인 통합(CI)을 함께 구성해보고, 지속적인 딜리버리를 구축하기 위해서 필요한 단계들 즉, Infrastructure as code (IaC)를 활용한 배포 환경 구성, 웹 애플리케이션의 배포, End to End 테스트 수행 등 다양한 데모를 오케이 계획대로 하고있을 건데, 이 놀라운 발표를 감당할 수 있겠습니까?


-   DevOps를 시작하려면

-   CI/CD (MSDN에 있음)
    -   Continuous Integration
        -   코드의 개발 및 테스트를 단순화
        -   개발초기에 버그나 문제를 파악하는데 도움
        -   자동화된 테스트와 빌드
        -   산출물로는 빌드 아티팩트가 생성
    -   Continuous Delivery
        -   하나 이상의 테스트 및 운영 환경에 배포하는 프로세스
        -   품질향상
        -   자동화된 릴리즈 파이프라인
        -   기존 시스템에 새로운 버전과 수정 사항을 릴리즈
-   CI 다시 이해하기

-   DevOps와 Azure

-   여러 다른 서비스에도 문제없이 배포가 가능하다
    -   이를 위해선 중요한 키포인트는 Azure pipelines
    -   매킨토시가 없어도 클라우드 상에서 macOS도 빌드가 가능하다!! (충격)
    -   커스텀 머신을 물려서 쓸수도 있음!

-   데모 타임
    -   데모를 이해기위해 알아야 할 사항
    -   소스에 대한 간략한 설명
        -   T모 사이트 강좌 WebAPI
            -   강좌 메뉴 (대,중,소)
            -   강좌 목록 및 내용
        -   그냥 Web App이라고 생각하면 됨
        -   단위 테스트 프로젝트 하나 만들어둠
        -   실제 Stage에 배포가 된 뒤에 DB와 연결되고 난 뒤 End to End 테스트를 수행
        -   ARM 템플릿
            -   json 기반을 통한 리소스 그룹 생성 및 WebApp 생성

## Azure Pipelines, Python을 CI, AWS로 CD 프랙티스
 > 이것은 Azure인가 AWS인가. Azure와 Python과 AWS가 함께하는 DevOps 콜라보 <br>
이번 세션에서는 Python/Django 웹애플리케이션을 GitHub의 Repo에 올리고, Azure DevOps의 Pipelines 기능을 사용하여 지속적인 통합(CI)과 지속적인 전달(CD)을 수행하는 데모를 진행합니다. 특히 Azure DevOps를 활용해서 Amazon Web Service로 배포하는 데모는 상당히 인상적입니다. Azure와 Python과 AWS가 함께하는 화끈한 시나리오. 듣기만해도 기대가 되쥬? 

## 디자인미 팀의 Azure DevOps 실제 적용사례
 > 뇌에 그냥 넣어주는 소확행 실제사례. 실시간 배포를 실시간으로 경험하는 은혜로운 시간 <br>
디자인미의 feature 팀에서 Azure, iOS, Android, Windows 등을 대상으로 하는 서비스를 만들기 위해 Azure DevOps를 어떻게 활용하고 있는지 살펴봅니다. Azure Boards를 활용하여 팀원에게 작업항목을 할당하고 팀원이 코드를 PR하면 코드 리뷰와 빌드, 테스트를 거쳐 자동으로 기능이 완료되는 마법같은 리얼타임 액션을 실시간으로 여러분의 눈속에 비춰드립니다. 3월말에 경험하는 확실한 소확행. 놓치지 않을거예요.