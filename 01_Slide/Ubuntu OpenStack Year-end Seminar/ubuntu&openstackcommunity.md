# 19:00 ~ 19:20: "Stella-OpenStack:성능 SLA를 제공하는 OpenStack기반 클라우드"  -   Stella team Operating Systems Lab , Korea University

- 클라우드, 블록체인?

## Stella project 소개
    - SLA(Service Level Agreement)<br>
    클라우드 서비스 제공자와 사용자 간 서비스의 품질 수준을 정의하는 계약

    - SLA를 만족하지 못할경우 <br> 
    Amazon시 시간지역시 상당히 큰 금액 손실이 있음

    - 다양한 자원 요구로 인한 SLA 위반 발생 <br>
        CPU 자원 부족에 의한 급격한 스토리지 io 성능 저하 발견

    - SLA 위반 원인 <br>
    VM1과 VM2의 io처리를 위한 cpu 자원 부족

    - Stella Team 프로젝트 책임자 고려대 유혁 교수<br>
        - 프로젝트 목표<br>
        성능 SLA를 보장할 수 있는 클라우드 소프트웨어 개발
        노드 수준 및 클라우드 수준의 SLA 보장

## Stella 테스트 베드 구성
    - Stella-Horizon 프로토 타입 : Open Stack horizon 기반 SLO 설정 기능 추가 (사용자를 위한 웹 UI 제공)<br>

    - Stella-OpenStack 프로토 타입 : xGoS를 위한 클라우드 수준 컴포넌트, OpenStack과 연동 및 Stella API 처리 컴포넌트<br>

    - xGoS(eXtended Guarentee of SLA) : 노드 수준 cpu, 네트워크, 블록io자원 통합 스케줄러  가상머신 별 cpu, network, block i/o성능 할당 제공
    시연을 위한 모니터링

## Stella 성능 보장 테스트<br>
    - 스텔라 클라우드에서 3개의 vm에서 벤치마크를 동시에 실행
        - vm1 : 네트워크 io 작업 (netperf)
        - vm2 : 스토리지 io 작업(iometer)
        - vm3 : cpu 작업 (sysbench-cpu)

    - Stella-Horizon을 통해 SLO를 세팅
        - vm1: 20000pps, vm2 : 300MB/s, vm3 : 20%

## 시연

    - Stella 프로젝트 홈페이지<br>
    http://stella.korea.ac.kr

    - GitHub<br>
    https://github.com/KUoslab

    - Launchpad<br>
    https://launchpad.net/ku.stella

        


# 19:20 ~ 20:00: "커뮤니티 기반 오픈스택과 비즈니스" - OpenStack Korea Group 김호진 이사 (오픈소스컨설팅)

- IBM에서도 OpenStack을 만들려던 시도가 있었음
- 유닉스를 사용하다가 Power Linux로 넘어감

## Why Linux / OpenSource?
- 고객이 오픈소스를 바라보는 시각

- 이전에는 서비스가 얼마나 안정적인가? 를 주로 봤었음
- Unix Vendor 처럼 가능했던 이유 리눅스에서는 불가능

- 리눅스/오픈소스는 x86하드웨어 위에 도는 os/app일뿐 한계점이 있다.

- 고객이 원하는바 -> <br>절대 죽지 않는 서비스, It`s not OS, Service Stability<br>
- 고객은 리눅스에 열광한 것이 아니라, 구글/페이스북의 seamless한 대용량 서비스에 열광<br>
- 모든 관점은 이미 서비스 관점으로 넘어온 상태 -> <br>더이상 하드웨어 안정성은 고민하지 않음

- 오픈스택이 연시장 매우큼 

- 기존 인프라 구성의 문제점 -> 하드웨어 SPOF와 장애에 떨던 시절/ HACMP

## 일반 가상화/ 베어메탈 인프라

- 인프라를 가지게된 모든 하드웨어/ 소프트웨어 업체들
- 인프라는 다 함께 쓰는 오픈소스 오픈스택
- 모든 벤더들이 자체 패키징을 해버림 

- 모든 하드웨어 업체가 인프라를 가지고 싶어하는 이유? <br>-- Case of Oracle
RAC환경에서 Cluster File System을 만들기 위한 노력

- 오라클은 어떤 벤더 의존성없이 오라클 자체 솔루션을 고객에게 제공할 수 있게됨.

## 오픈스택 커뮤니티가 다른 커뮤니티와 다른 이유
- 인프라를 가지고 싶어하는 하드웨어 업체들의 참여와 기여

- 어느 누구도 모든 프로젝트를 다 감당할 수 없음 -> <br>각자 장점을 내세운 분야에서의 기여 -> <br>모든 지식이 커뮤니티에 모임<br>
- neutron, tacker, nova, cinder, manilla, ....

- 가장 높은 참여도를 가지고 있는 오픈소스 프로젝트 - OHLOH (Open Hub and the Open Hub logo) <br>현재 전체 오픈소스에서 가장 인기 높은 프로젝트

## openstack by 오픈소스컨설팅 packaging구성

- 코인제스트 하이브리드 + 가상화폐거래소 (글로벌 9위 / 국내 2위 거래량 2018.09.05 기준)

- Kubernetes on OpenStack이 가장 이상적인 형태(?)가 아닐까..

- Kube on OpenStack의 형태로 실제로 서비스 하는 사례가 있는지..? - > <br>개발 목적으론 있지만 실제로 서비스는 kube위에만 올려서 사용했음

