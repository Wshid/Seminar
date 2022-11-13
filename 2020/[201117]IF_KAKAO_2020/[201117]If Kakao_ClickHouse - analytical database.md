## ClickHouse
- link : https://if.kakao.com/session/117
- column oriented database

---

### 도입 배경
- 기존 `druid` 사용
  - 기존 데이터를 aggregation하여 저장하기 용이
- clickhouse : non-aggregate하여 저장 가능하면 속도가 빠름

### ClickHouse?
- 속도에 중점을 둔 db
- 트랜잭션 기능 축소
  - 데이터 변경이나 삭제 기능을 매우 제한적으로 지원

### vetroized processing
- SIMD(Single Instruction Multiple Data)집합을 사용
- 한번에 여러 연산을 수행할 수 있는 방식
- short vector instructions
  - SIMD text parsing
  - SIMD data filtering
  - SIMD decompression
  - SIMD string operations
  - More specialized algorithm ...

### Merge Tree
```sql
`values Array(String)
...
) ENGINE = MergeTree() -- Table Engine type
PARTITION BY `date` -- 파티션을 나누는 단위
ORDER BY `id` -- 파티션 안에서 인덱스를 생성하고 정렬하는 기준
```
- 테이블은 파티션 단위로 나뉨
- 각 파티션별 인덱스 존재
- 데이터 저장시, 개별적인 파트로 나누어 저장
  - Insert 연산이 매우 가벼움
- 파트를 bg에서 머지하는 방식
- 머지 이후 압축하여 속도 증가
- 속도를 빠르게 하는 방식 예시
  - ![image](https://user-images.githubusercontent.com/10006290/102004524-c6653200-3d54-11eb-8597-4e23d2cc0246.png)

### Primary Index
- 파티션 내부 데이터를 줄이는 방식
- `primary.idx` => sparse 하게 저장
  - 그에 따라 저장 용량이 줄어듦
- columar db이기 때문에
  - 컬럼별로 데이터 존재
- primary.idx 번호가 매겨짐
  - 번호는 각 데이터 파일의 시작위치가 담긴 마크 파일을 참조(`id.mrk`)

### Data skipping index
- index에 없는 내용을 빠르게 조회하기
- primary index는
  - 데이터를 물리적으로 정렬하고
- data skipping index는
  - 여기에 스캔을 위한 자료구조 추가
- 테이블 생성 구문을 변경해야 함
  ```sql
  -- CREATE TABLE
  (
    ...
    INDEX bf_values values TYPE bloom_filter(0.1) GRANULARITY 8192
  )
  ```
  - bloom filter
    - 집합의 원소의 포함 여부를 확인하기 위한 자료 구조
    - 확률적으로 저장하기 때문에 긍정 오류의 가능성 존재
    - 저장 공간을 효율적으로 사용할 수 있음
    - ![image](https://user-images.githubusercontent.com/10006290/102004603-9f5b3000-3d55-11eb-908c-7460764fb84e.png)
      - `e.g.` 특정 값을 찾을 때
        - 해당 값의 해시 값을 뜬 다음
        - bloomFilter에 맞는 비트값과 일치하는지 검증
- data skipping index에는 `bloom_fileter`외에 `stack`, `min/max`등이 존재

### Replication
- 분산 시스템 관점
- 기본적으로 미동기 멀티 마스터 복제
- User로 부터 입력을 받게 되면
  - 즉시 응답하며
  - bg에서 replica로 데이터 복제
  - replica에서 조회시 일시적 오류가 날 수 있으나, bg copy가 매우 빠른편
    - ![image](https://user-images.githubusercontent.com/10006290/102004639-eea16080-3d55-11eb-823b-3c0a7e589a62.png)
- multi master
  - replica 모두가 master로 동작 가능
  - 데이터 저장시 압축본을 만들게 되며, 복제시에는 압축 내용만 전달
- fault tolrerent(고가용성)

### Districuted Processing
- Cluster
- 데이터를 여러 샤드에 나누어 저장
  
### Funnel Analysis
- Funnel : 깔때기
- 사용자가 일련의 행동을 거칠 수록, 모수가 적어짐
  - 깔때기 모양의 형태
  - `e.g. 이벤트 화면 -> 추천 상품 -> 구매완료..`
  - 어느 단계까지 진행했는지, 단계별 번호로 리턴 가능
- 유연한 분석이 가능 => non-aggregatee
- `windowFunnel` 함수를 사용해서 분석 가능
- 단 이벤트가 중복되거나, 지정하지 않는 이벤트가 들어가는 경우도 많음
  - 여기서 kakao에서 기여함
  - `strict` 옵션 및 `strict_order`등의 옵션 구현
- ![image](https://user-images.githubusercontent.com/10006290/102004724-8e5eee80-3d56-11eb-8620-3b026c4092ca.png)
