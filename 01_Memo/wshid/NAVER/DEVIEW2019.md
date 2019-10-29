# DEVIEW 2019
- 19.10.30
- 코엑스 그랜드볼룸
---

### 2일차

## 대용량 멀티테넌트 시큐어 하둡 클러스터를 시행착오 없이 만들기
- 14:00 ~ 14:45

### Hadoop Security 시작하기전 고려사항
- 작은 조직이라면 무조건 필요하지 않음
- 단, 민감한 데이터라면 무조건 필수적으로 필요
### Hadoop Security OverView
- Authentication(인증) : 사용자의 신분을 확인하는 과정
  - kerberos, delegation token
- Authorization(권한부여, 허가) : 사용자가 요청한 리소스의 접근을 허가/거부를 확인하는 과정
  - auth이후에 작업
  - ACL로 관리
  - 허술한 auth로는 권한부여도 불가능 함
  - HDFS : Posix per, POSIX acl
  - Yarn : HDFS ACL, ...
- Auditing(감사) : auth, author 과정의 결과를 audit log에 기록
  - 로깅 관련
  - HDFS : hdfs-audit.log
  - ...
- Kerberos
  - 중앙화된 서버에서 사용자 정보 관리
  - 평문의 비밀번호, 네트웍 전송을 하지 않음
  - 상대적으로 짧은 유효기간의 티켓 사용
  - SSO
- Kerberos 용어
  - Realm : 인증을 제공하는 범위, 관습적으로 대문자의 도메인 사용
    - `X.NAVER.COM`
  - Principle : 커버로스의 사용자 id, 사용자는 사람이 아닐 수 있음
  - KeyTab : Key Table의 약자, principle의 비밀번호 저장
  - Ticket : KDC에서 발급, C가 서비스 접속시 사용
- KDC 구성 요소
  - KDC(Key Distribution Center) : 보통 한 프로세스 안에 세가지 구성요소가 들어감
    - 중앙화된 서버에서 사용자 정보 관리
  - Authentication Server(AS)
    - TGT를 발급 하는 서버
    - 평문의 비밀번호를 네트워크로 절대 전송하지 않음
  - TGT(Ticket Granting Ticket)
    - TGS에 접속할 수 있는 티켓
  - TGS
    - TGT를 가지고 있으면 TGS에 접속할때 비밀번호가 필요 없음
    - SSO
- Kerberos Ticket
  - KDC(TGS)에서 발급한 암호화된 데이터 구조
  - 일반적으로 24h 사용(ticket_lifetime)
  - 만료되기 전에 갱신(renew)이 가능하나, 7d정도의 제한 존재
- Process
  - 1. User -> Get TGT -> KDC
  - 2. User -> Request Service Ticket -> KDC
  - 3. User -> Name Server(서비스 서버)
- Delegation Token
  - 하둡의 인증은 kerberos와 Delegation Token을 사용
    - 분산 환경에서 KDC가 병목이 될 수 있기 때문
  - NameNode에서 발급함, C는 dt를 이용하여 nn에서 인증
    - 리소스 서버등 대부분의 서버에서 관리 가능
  - `renew-interval : 24h, max-lifetime : 7d`
- MR 제출 흐름
  - keberos인증, nn에서 DT 요청
  - DT를 포함하여 MR을 RM에 제출
  - RM에서 dt유효성 검사후 mr job 런칭
- Kerberos Ticket vs Delegation Token
  - 서버에서 데이터 저장 여부
    - kdc에서 ticket을 저장하지 않음
    - dt발급서버에서 dt저장
  - renew시의 데이터 변경
    - kdc에서는 새로운 티켓을 발급받아 티켓 변경
    - dt는 매번 새로 발급받지 않음, 서버에서 유효기간만 연장
- SPENEGO
- LDAP
  - 디렉터리 서비스
  - 파일시스템의 폴더는 아님
  - 사용자 계정과 그룹관리를 위해 사용
- LDAP이 필요한 이유
  - 시큐어 환경에서 MR과 같은 작업은, 계정이 모든 노드에 포함되어야 함
  - 각 노드 LDAP 연동 이후 설정하면 자동으로 됨
### Secure Hadoop 설치 과정
- Ambari, HDP, MIT Kerberos, OpenLDAP
- KDC와 LDAP을 Ambari를 지원하지 않음
- 설치 흐름
- 설치는 쉽지만 다양한 이슈가 존재함
- Ambari로 주요 이슈는 설치가 해결 됨
- 설치 이후 Summary탭에 커버로스 관련 문구 존재
- ambari가 최적은 아니라고 함
- LDAP을 연동하면 zeppelin 계정등의 다양한 이점을 누릴 수 있음
- 
### 하둡 사용 패턴 변화



---
## 쿠팡 추천 시스템 2년간의 변천사 (상품추천에서 실시간 개인화로)
- 15:00 ~ 15:45

## 스케일아웃없이 순간 급증하는 주문 처리하기 (Microservice with Kafka)
- 16:00 ~ 16:45

## ~~대규모 클러스터 모니터링 도전기: 모니터링, 어디까지 알아보고 오셨어요? Cluster level 부터 User level까지~~
- 16:00~16:45