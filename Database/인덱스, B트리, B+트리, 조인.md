## 인덱스, B트리, B+트리, 조인

## 목차

1. [인덱스란](#인덱스란)
    - [인덱스의 특징](#인덱스의-특징)
        - [인덱스 테이블](#인덱스-테이블)
        - [정렬](#정렬)
    - [INDEX는 어떻게 동작하는가?](#index는-어떻게-동작하는가)
        - [B+Tree 인덱스 알고리즘](#btree-인덱스-알고리즘)
    - [결합 인덱스(다중 컬럼 인덱스)란](#결합-인덱스다중-컬럼-인덱스란)
        - [결합 인덱스를 타는 경우와 안 타는 경우](#결합-인덱스를-타는-경우와-안-타는-경우)
        - [결합 인덱스 순서의 중요성](#결합-인덱스-순서의-중요성)
    - [INDEX 설계 시 고려할 점](#index-설계-시-고려할-점)
        - [인덱스 컬럼 기준](#인덱스-컬럼-기준)
    - [ORDER BY 와 GROUP BY에 대한 INDEX](#order-by-와-group-by에-대한-index)
    - [인덱스 조회시 주의 사항](#인덱스-조회시-주의-사항)
        - [범위 조건(LIKE, BETWEEN, <, >)과 INDEX](#범위-조건like-between--과-index)
        - [=, in과 INDEX](#-in과-index)
        - [컬럼값 가공금지](#컬럼값-가공금지)
        - [null 값의 경우](#null-값의-경우)
        - [OR 조건](#or-조건)
1. [B트리]()
1. [B+트리]()
1. [조인]()

## 인덱스란

- 인덱스는 `테이블의 조회 성능을 높여주는 자료구조`로 데이터의 위치를 빠르게 찾아주는 역할
- `인덱스는` MYI(MySQL Index)`파일에 저장`되며, 인덱스가 설정되지 않았다면 `Table Full Scan`이 일어나 성능이 저하되거나 치명적인 장애가 발생한다.
   
## 인덱스의 특징

### 인덱스 테이블

- 인덱스는 `하나의 테이블을 생성해 값을 저장해놓고 사용`한다.
- 그래서 `기존 테이블에 의존적인 새로운 테이블이 하나 생성된다`는 점에서 무분별한 인덱스 생성은 오히려 성능 저하를 초래할 수 있음

### 정렬

- 인덱스 테이블은 `이진트리 검색을 사용`하므로 `기본적으로 정렬되어 있음`
- 그래서 인덱스 테이블이 참조하는 테이블에서 삽입, 삭제, 수정이 자주 일어나게 되면
- 인덱스 테이블에서는 `데이터를 정렬하면서` 삽입, 삭제, 수정이 일어나기 때문에 전체적인 성능 저하 초래
  - 조회속도는 빨라지지만 `UPDATE`, `INSERT`, `DELETE`의 `속도는 저하`된다는 단점이 있다. 
  - `Table의 index 색인 정보를 갱신`하는 `추가적인 비용` 소요

## INDEX는 어떻게 동작하는가?

- SCOUNTER TABLE 생성 시 AIRPORT 컬럼에 대한 인덱스를 주면 AIRPORT 컬럼에 대한 인덱스 테이블이 생성된다.
- 그리고 나중에 SCOUNTER 테이블에 AIRPORT 컬럼에 대한 WHERE문이 포함된 쿼리가 나갈 때,
- AIRPORT 인덱스 테이블에 저장된 key-value 값을 참조해서 SCOUNTER TABLE 테이블에서 결과 값을 반환해온다.

### 동작순서

1. Index Table에서 where에 포함된 값을 찾음
1. 해당 값의 table_id[PK] 값을 가져옴
1. 가져온 table_id[PK] 값으로 원본 테이블에서 값을 조회해옴

#### 그리고 DBMS는 INDEX를 다양한 알고리즘으로 관리하고 있는데, 일반적으로 사용하는 알고리즘은 B+Tree 알고리즘

### B+Tree 인덱스 알고리즘

<img width="700" alt="스크린샷 2022-09-29 오후 10 10 42" src="https://user-images.githubusercontent.com/75410527/193040581-e6b57462-1eb9-4a30-8970-12a44aec33a8.png">

#### B+Tree 구성

> 루트 노드: 경로의 출발점 노드  
> 논리 노드: `리프 노드까지의 경로` 역할을 하는 노드  
> 리프 노드: `실제 데이터가 저장`되는 노드  

- B+Tree는 리프 노드에 이르기까지 자식 노드에 대한 포인터가 저장되어 있어 탐색 시 루트 노드에서 `한 개의 경로만 검색`하면 되기 때문에 검색에 있어 효율적
- 각 노드의 `자료는 정렬`되어 있습니다. 
- `모든 left node는 같은 레벨`에 있습니다.

## 결합 인덱스(다중 컬럼 인덱스)란

- 다중 컬럼 인덱스는 두개 이상의 필드를 조합해서 생성한 INDEX
- 주 용도는 SQL에서 WHERE절의 조건 `컬럼이 2개 이상 AND로 연결되어 함께 사용되는 경우`에 많이 사용
- 다중 컬럼 인덱스는 단일 컬럼 인덱스 보다 `더 비효율적으로 INDEX/UPDATE/DELETE를 수행`하기 때문에 신중해야한다.

#### Table1(단일 인덱스)

```sql
CREATE TABLE table1(
    uid INT(11) NOT NULL auto_increment,
    id VARCHAR(20) NOT NULL,
    name VARCHAR(50) NOT NULL,
    address VARCHAR(100) NOT NULL,
    PRIMARY KEY('uid'),
    key idx_name(name),
    key idx_address(address)
)
```

#### Table2(다중 컬럼 인덱스)

```sql
CREATE TABLE table2(
    uid INT(11) NOT NULL auto_increment,
    id VARCHAR(20) NOT NULL,
    name VARCHAR(50) NOT NULL,
    address VARCHAR(100) NOT NULL,
    PRIMARY KEY('uid'),
    key idx_name(name, address)    
)
```

#### QUREY문

```sql
SELECT * FROM table1 WHERE name='홍길동' AND address='경기도';
```

#### Table1

- table1의 경우에 각각 컬럼(name),(address)에 INDEX가 걸려있기 때문에 
- MySQL은 name컬럼과 address컬럼을 보고 `둘 중에 어떤 컬럼의 수가 더 빠르게 검색되는지 판단 후` 
- 빠른쪽을 먼저 검색하고 그 다음 다른 컬럼을 검색하게 된다.

#### Table2

- table2의 경우 `바로 원하는 값을 찾는데` 그 이유는 `INDEX를 저장할 때 name과 address를 같이 저장`하기 때문이다. 
- 즉, name과 address의 `값을 같이 색인`하고 검색에서도 '홍길동경기도'로 검색을 시도하게 된다.
- 이렇게 사용할 경우 table1보다 table2의 경우가 더 빠른 검색을 할 수 있다.

### 결합 인덱스를 타는 경우와 안 타는 경우

- 다중 컬럼 인덱스를 사용할 때는 INDEX로 설정해준 `제일 왼쪽컬럼`이 `WHERE절에 사용되어야` 한다.
  - 인덱스는 `B*Tree 자료구조 탐색`을 하기 때문

#### 결합 인덱스를 타는 경우

- 조회 조건이 `B*Tree 자료구조 탐색`에 위배되지 않는다면 인덱스와 완전히 동일한 조회 조건이 아니어도 결합 인덱스를 탐
- 결합 인덱스의 `제일 왼쪽 컬럼`인 `name`이 사용되었으므로 인덱스를 탐

```sql
key idx_name(name, address)    

SELECT * FROM table2 WHERE name='홍길동';
```

```sql
key idx_name(name, address, age)   

WHERE name = ? AND address = ?;
```

#### 결합 인덱스를 안 타는 경우

- 조회 조건이 `B*Tree 자료구조 탐색`에 위배되는 경우 결합 인덱스를 타지 않음
  - 제일 왼쪽 컬럼부터 `연속된 조회 조건이 아닌 경우`  
- 결합 인덱스의 `제일 왼쪽 컬럼이 조회 조건에 사용되지 않았으므로` 인덱스를 타지 않음

```sql
key idx_name(name, address)    

SELECT * FROM table2 WHERE address='경기도';
```

- age 컬럼은 인덱스가 적용되지 않는다.

```sql
key idx_name(name, address, age)   

WHERE name = ? AND age = ?; 
```

### 결합 인덱스 순서의 중요성

- `카디널리티가 높은 순서대로` `내림차순으로 적용`하는 게 성능상 제일 좋음 
  - 결합 인덱스에서는 첫 번째 조건에서 최대한 많은 데이터를 걸러내서 두 번째 조건을 검사하도록 해야 합니다.
  
```sql
# 시간 정보는 카디널리티가 높으므로 그나마 나음
key idx_name(created_at, payment_state)   

# ㅈ망. 1댑스 인덱스에서 데이터를 거의 걸러내지 못함 
key idx_name(payment_state, created_at)
```

## INDEX 설계 시 고려할 점

- 무조건 많이 설정하지 않는다. (한 테이블당 3~5개가 적당 목적에 따라 상이)
  - 왜냐면 Table의 `index 색인 정보를 갱신하는 추가적인 비용이 많이 소모`됨
  - 또한, index 데이터 크기가 커지므로(`인덱스도 엄연히 테이블처럼 크기를 차지`) 불필요한 인덱스로 크기가 커지는 건 바람직하지 않음. 

### 인덱스 컬럼 기준

- 조회시 자주 사용하는 컬럼
- UPDATE가 빈번하지 않은 컬럼
- JOIN 시 자주 사용하는 컬럼
- 카디널리티가 높은 컬럼 (주민등록번호 vs 성별, 한 컬럼이 가지고 있는 중복의 정도)


## ORDER BY 와 GROUP BY에 대한 INDEX

INDEX는 ORDER BY와 GROUP BY에도 영향을 끼치는데 다음과 같은 경우에는 INDEX를 타지 않는다.

- WHERE 컬럼1='값' ORDER BY 인덱스 컬럼 : 연속하지 않은 컬럼에 대해 ORDER BY를 실행한 경우
  - ORDER BY도 똑같이 `B*Tree 자료구조 탐색` 조건에 위배되지 않아야 인덱스가 걸림
- ORDER BY 인덱스컬럼1 DESC, 인덱스컬럼2 ASC : `DESC와 ASC를 혼합해서 사용`한 경우
  - 정렬되는 각 컬럼의 오름차순 및 내림차순 옵션이 `인덱스와 같거나 정반대인 경우`에만 사용가능 
- GROUP BY 컬럼1 ORDER BY 컬럼2 : GROUP BY와 ORDER BY의 컬럼이 다른 경우
- ORDER BY ABS(컬럼) : ORDER BY 절에 `다른 표현`을 사용한 경우

## 인덱스 조회시 주의 사항

### 범위 조건(LIKE, BETWEEN, <, >)과 INDEX

between, like, <, > 등 `범위 조건은 해당 컬럼은 인덱스를 타지만`, `그 뒤 인덱스 컬럼들은 인덱스가 사용되지 않습니다.`

- `group_no, from_date, is_bonus`으로 인덱스가 잡혀있다고 가정하겠습니다.
- 조회 쿼리를 `where group_no=XX and is_bonus=YY and from_date > ZZ` 등으로 잡으면 is_bonus는 인덱스가 사용되지 않습니다.
  - `중간에 from_date에서 범위 탐색`을 함으로써 인덱스가 먹히지 않음

### =, in과 INDEX

- `=`, `in`은 다음 컬럼도 인덱스를 사용합니다.
  - 왜냐면 `in`은 결국 `=`를 `여러번 실행`시킨 것이기 때문입니다.
  - 단, in은 `인자값으로 상수가 포함되면 문제 없지만`, 서브쿼리를 넣게되면 성능상 이슈가 발생합니다. 

### 컬럼값 가공금지

- 인덱스는 가공된 데이터를 저장하고 있지 않습니다.
- 인덱스로 사용된 `컬럼값 그대로 사용해야만 인덱스가 사용`됩니다.
  - `where salary * 10 > 150000;`는 인덱스를 사용하지 않습니다.
  - 하지만, `where salary > 150000 / 10;`는 인덱스를 사용합니다.
  - `ABS(컬럼)` 이런것도 인덱스가 

### null 값의 경우

- null 값의 경우 `is null 조건`으로 `인덱스 레인지 스캔 가능`

### OR 조건

- `AND` 연산자는 각 조건들이 읽어와야할 ROW 수를 줄이는 역할을 합니다.
- 하지만 `OR` 연산자는 비교해야할 ROW가 더 늘어나기 때문에 풀 테이블 스캔이 발생할 확률이 높습니다.

## References

- [[MySQL] 인덱스(INDEX) 정리(효과적인 INDEX 설계)](https://spiderwebcoding.tistory.com/6)
- [[DataBase] DB 성능을 위한 Index](https://brunch.co.kr/@skeks463/25)
- [[자료구조] 간단히 알아보는 B-Tree, B+Tree, B*Tree](https://ssocoit.tistory.com/217#2._B*Tree)
- [GROUP BY, ORDER BY 인덱스 튜닝](https://lannstark.tistory.com/40)
- [[mysql] 인덱스 정리 및 팁](https://jojoldu.tistory.com/243)
