## GDG DevFest WebTech 2018
- 20:00 ~ 22:30, 마루 180
- Written By. wshid(kso4013@gmail.com)
---
- GDG Devfest란?
    - GDG가 매년 전세계적으로 개최하는 개발자 행사
- Front-End LevelUp

## Angular로 프론트엔드 개발 레벨 업 - 고재도님
---
- LevelUP = 성장
- Javascript 역사
    - ECMA 5 -> 6까지
- 순서
    - **Canvas**
        - 캔버스로 차트 그리기
        - 좌표로만 이동함
        - JavaScript OOP 문법
    - **dojo toolkit**
        - UI 컴포넌트
    - **jQuery**
        - 웹 접근성이 중요해진 시기
            - 크로스브라우징을 해야 함
        - DOM Manipulation
        - Ajax
    - **ExtJS**
        - 프레임워크 기반으로 동작하게끔
        - 클래스 시스템
        - Modularity
            - ExtJS의 경우 Module을 하는 방법이 정해짐
        - **require** 문법
        - template
            - JSP와 유사하다고 함
        - Data Model
            - 어떻게 데이터 처리할 모델을 View로 가져올 것인가 등
        - Layout
            - flex 개념
    - **Angular.js**
        - MVC 구조
            - Model : JS Object
            - View : DOM
            - Controller : JS Function
        - 양방향 데이터 바인딩
        - 의존관계 주입
        - 지시자
            - React Component와 유사
    - **Polymer**
        - Web Component를 지탱하는 4가지
            - Custom Element
                - UI Component단을 하위 Layer(Brower Level)에서 지원할 수 있게 해줌
                - 새로운 엘리먼트의 확장
            - HTML Imports
                - HTML, CSS, JS 로드
            - Shadow DOM
                - 캡슐화된 DOM, CSS의 스코프 분리
            - Template
        - 더 편리한 웹 컴포넌트를 만들 수 있도록 함
        - 요즘에 사용하지 않음
        - 컴포넌트
            - 어플리케이션의 구성 요소
    - **Angluar**
        - TypeScript
            - A Superset of JavaScript = JavaScript + Static Types
            - ES6 Syntax, Type Annotation
            - ES6 기반으로 돌아가기 때문에, 이 문법을 알고 있어야 함
        - Component를 Decorator 기반으로 사용
            ```
            <My-HelloWorldComponent>
            </My-HelloWorldComponent>
            ```
            - 컴포넌트에도 다양한 존재가 있음
                - stateless vs stateful
        - 컴포넌트 계층구조간 커뮤니케이션
            - 부모 -> 자식 : ```@input()``` decorator 활용
            - 자식 -> 부모 : ```@Output()``` decorator 활용
        - 서비스를 활용한 커뮤니케이션
            - 서비스 : 자료 교환을 위한 용도
            - 데이터의 전달은 다 서비스로 해야함
                - cf. Redux in React
        - 라우터를 통한 SPA 개발
            - 특정 URL에 따라 그리는 내용이 달라짐
        - Rxjs : reactive programming
            - 비동기 데이터 스트림을 만들고 변형
                - 이전에는 promise 했다고 함(비동기 전송을 위해)
            - FE에서 Observable과 함께 프로그래밍을 하는것을 의미
                - Observable
                    - lazy 하게 처리
                    - 요구사항이 생기면 consume하여 처리
                    - 마우스 이벤트를 원하는 내용으로 조합이 가능해짐
    - **React**
        - with Redux
- FE 개발자 이긴 하지만, BE 개발자 이기도 함
    - BE개발을 해가면서 FE에서 이용할 수 있는 지식들이 많다고 함
    
## 팀장이 되었다 - 박 민
---
- 커리어를 어떻게 쌓는가
- 타입
    - Gen
        - 연결하고 실행하는 사람
            - Managing
        - 인사이트
        - 어디서 봤었다는 것에 대한 시야
        - 겸손
        - 어디까지
            - 어디까지 바운더리를 두어 배울 수 있는가에 대해
    - Spe
        - 문제를 해결하는 사람
            - Engineer
        - 전문성
        - 겸손
        - 자기만의 세계
        - 자기 세상에 갇혀, 커뮤니케이션의 대한 부재
- 회사
    - SI
        - 유지보수, 작은 수정
    - 스타트업
        - 서둘러 개발, 직접 개발
    - 대기업
        - 자기 개발, 하청
- 루트
    - 스타트업
        - 추천하는 분야
        - 제품
            기능 추가
    - SI => 스타트업
    - 대기업 => 스타트업

## 프론트엔드 개발자 가이드
---
- font
    - 일반적인 글씨
    - 다국어지원 시 문제가 발생할 수 있음
    - 디자이너가 해당 폰트에 따라 자간이나 이런 설정을 해주긴 하지만, 그에 따라 비용이 엄청 큰 작업
        - 하지만 그 폰트에 대한 용량도 무시할 수 없음
    - 폰트의 용량 문제를 꼭 확인할 것
        - woff2를 사용하면 그만큼 문제가 발생할 수 있다
- typography
    - 폰트를 가지고 만들어 내는 것들
    - 타이틀로 어떤 폰트에 어떤 형식을 가져갈 것이다
        - 스타일과 유사한듯
    - 중간에 내용이 자주 변경될 수 있음
- important에 대한 이슈
    - 중요정도가 변경되었을때, 그에 따라 변경사항이 발생된다면
- color에 의미주기
    ```
    --gray-0: #999999;
    --gray-1: #AAAAAA;  #이와같이 Range 설정

    --brand-black: #1111; # 브랜드 색 지정
    
    --status-danger : 상황에 따른 색설정
    --status-success : 상황에 따른 색설정

    #type 지정하기
    --common-background : 커먼에 대한 설정
    --common-padding-size : 패딩 사이즈에 대한 설정

    $red: #ff0000;
    $dark-red : darken($red, 10%); // global에 피하기 유해 이런식으로 설정

    ```
    - unit-margin 같은 수치를 두어, 유닛 설정을 프로그래밍적으로 할 수 있도록 조정할 수 있음
    - Component가 페이지마다 값이 달라진다면
        - 컴포넌트 형식으로 디자인을 한다
        - 컴포넌트를 미리 표로 만들어가지고 설정한다.
    ```
    human.log(buttonA === buttonB);
    ```
    - ```npm run storybook```
        - 툴을 사용하여 미리 확인할 수 있음
    - 해부학적으로 접근하기
        - 특정 컨테이너에 어떤 input을 두고 어떤 사이즈에 어떤 폰트를 사용한다
        - 이를 미리 정해두고 가는것이 좋음
        - DOM은 결국 Component의 조합과 유사하기 때문
- 컴포넌트 레벨로만 잘 구성해도, 디자이너의 삽질을 줄일 수 있음
- Button이나 p태그 이런것을 어떻게 나눌 것인가
- 외부 라이브러리 활용시
    - 어느정도 추상화를 해야함
    - 줄 수 있는 옵션을 지정해주기
    - 내부적으로 interface를 구성해서 만들어주기
        - Wrapping
    - interface를 사용하지 안흥면 라이브러리 변경이나 추후 코드 수정시 전체 코드 수정이 필요할 수 있음
- 데이터 핸들링에서 이슈가 발생할 수 있으므로
    - 그에 따른 케이스를 만들어 유닛 테스트를 해두는 것이 좋음
- 상수 관리
    - 관용적인건 상수로 만들어주는 것이 좋음
- API 호출
    - 추상화 작업을 거쳐야함
    - 사용하는 의미를 확실히 하기
- css
    - css도 component처럼 사용하기
    - ``` .home .home-button``` 이런식으로 사용
- closure를 사용하기
- 라이브러리 선택시
    - Github Star
    - 팀의 요구사항
    - 필요한 정도
