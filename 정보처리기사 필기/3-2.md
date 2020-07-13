### 3-2 SQL 활용
##### 기본 SQL 작성
* DDL(데이터 정의어) = 테이블, 뷰 테이블, 인덱스 등 생성, 변경, 제거하는데 사용되는 명령어[DBMS에서는 DDL=오브젝트]
    - DDL 대상: 스키마, 도메인, 테이블, 뷰, 인덱스
    - DDL 조작 방법
        + 생성하기 : CREATE
            
                구문
                    CREATE TABLE [IF NOT EXISTS] 신규테이블명
                    (
                        칼럼명1 datatype(길이) [NOT NULL|NULL] [DEFAULT 값],
                        칼럼명2 datatype(길이) [NOT NULL|NULL] [DEFAULT 값],
                        [PRIMARY KEY (칼럼명)], //기본키 = NOT NULL+UNIQUE함
                        [UNIQUE KEY (칼럼명)], //열은 유일 값(동일 값 있으면 X)
                        [FOREIGN KEY (외래키 칼럼명)] [REFERENCES 참조테이블(참조 테이블의 KEY)],
                        // 옵션인 아래 두가지는 **참조 무결성 위배 처리 방법**
                        [ON DELETE {NO ACTION|CASCADE|SET NULL|SET DEFAULT}],
                        [ON UPDATE {NO ACTION|CASCADE|SET NULL|SET DEFAULT}],
                  
                        [CONSTRAINT 제약조건명 CHECK(조건식)]
                     );
                     
                 * 다른 테이블 이용해서 신규 테이블 작성 경우
                        CREATE TABLE table_name AS SELECT문;
                 * ALTER문을 통해 제약조건 변경 가능 
                 
        + 변경하기 : ALTER
        
                구문
                    ALTER TABLE table_name {ADD|ALTER(MODIFY,CHANGE)|DROP} 변경하고자 하는 속성;
                * 열추가[ADD 주소 char(30)], 열데이터 변경[alter table 회원 alter(성명 char(10) not null)]
                
        + 삭제하기 : DROP(오브젝트 삭제), TRUNCATE(내용 삭제)
        
                구문
                    DROP TABLE table_name [CASCADE|RESTRICT]
                    DROP VIEW view_name [CASCADE|RESTRICT]
                    DROP INDEX index_name
                (ex)
                    table 삭제 = DROP TABLE table_name
                    table 내용 삭제 = TRUNCATE TABLE table_name
                    talbe 이름 변경 = RENAME TABLE 이전table_name TO 새로운table, ALTER TABLE 이전table_name RENAME 새로운table_name
        
                    
* 관계형 데이터 모델 = 2차원 구조의 테이블 형태
    + 특징: 간결, 보기 편리, 다른 DB로 변환 용이 / BUT 성능이 떨어짐!
    + 용어
    
        |relation| table구조(data간의 관계 -> table) (똑같은 tuple 가지면x)
        |---|---:|
        |attribute|열 (파일시스템의 record field와 같은의미, 가장 작은 논리적 단위)
        |tuple|행 (파일시스템의 record와 같은 의미)
        |degree|속성 수
        |cardinality|행 수(기수, 대응수)
        |domain| 하나의 속성이 가질 수 있는 값들의 집합 (선언 타입, 값의 합법 여부 판단)
        |null| 정보의 부재
        |relation schema|relation 이름+속성 이름의 집합
        |relation instance|relation의 어느 시점까지 입력된 tuple 집합
    + 개체 관계(E-R) 모델과 관계형 데이터 모델
        속성=동그라미, 테이블=네모, 관계=다이아몬드

* 트랜잭션
    + 특성
        - 원자성: 가장 중요 부분, 분해 불가능, **COMMIT/ROLLBACK(관리 주체별 기법-트랜잭션 관리자)**
        - 일관성: 무결성 유지, **참조 무결성 기법(관리 주체별 기법-무결성 제어기)**
        - 격리성: 동일 자원에 대해 동시에 사용 불가능, **병행제어(관리 주체별 기법)**
        - 영속성,지속성,계속성: 변화 상태 유지, 트랜잭션 완료 후 효과 지속, **회복(관리 주체별 기법)**
    + 상태
        - 상태 전이도
        
                               --성공--> 부분 완료 --COMMIT--> 완료
                              |            |
                  -시작->활동--            오류(아래로)
                              |            |
                               --실패-->   실패   --ROLLBACK--> 철회
                  
                   + 부분완료: 트랜잭션이 마지막까지 연산 실행, COMMIT 실행 전 단계
                   + 장애/실패: 트랜잭션이 오류가 발생하여 중단 상태
                   + 철회(Abort): 트랜잭션이 비정상적오르 종료->ROLLBACK 실행(재시작, 폐기)
                   
                   *하나의 트랜잭션 = COMMIT or ROLLBACK함

    + 제어 = 트랜잭션의 결과 수용 OR 취소 [제어 언어=TCL]
        - COMMIT = 하나의 트랜잭션 과정 종료
        
            (EX) 1->2->COMMIT->3->4->5->ROLLBACK : 이 경우 3~5만 취소!
            
* 테이블
    - 개념: DB의 기본적인 데이터 저장 단위, 데이터의 집합체, 물리적으로 DB에 저장X, 두 엔티티 간의 관계 표현!
    - 특징: 행들은 모두 다르다, 속성은 하나 이상, 속성(원자 값만 저장)
    - 관련 용어
        + 데이터 모델링: 현실 세계의 복잡한 개념을 단순화,추상화시켜 DB화 하는 과정
        + 정규화: 이상현상이 발생하는 테이블을 분해하여 없앰
        + 키 - 후보키(유일성+최소성 만족), 기본키(후보키 중 기준키), 대체키(후보키 중 기본키 뺀 나머지), 외래키, 슈퍼키(유일성만 만족)
        
* 데이터 사전=시스템 카탈로그
    + 개념
            
            -데이터베이스의 데이터를 제외한 모든 정보가 있다[데이터의 데이터(metadata)가 있다]
            -데이터 사전의 내용 변경하는 권한 = 시스템에 있다
            -사용자는 단순 조회만 가능[읽기 테이블로 제공]
            - DBA
    + 내용
    
        metadata: 사용자 정보(id/pw/권한), DB객체 정보(테이블, 뷰,인덱스), 무결성 제약정보, 데이터베이스 구조/성능 정보, 함수-프로시저
        
    + 용도
        - 사용자 입장: 단순 조회용
        - 시스템(컴파일러, 옵티마이저): 작업을 수행하는데 참조 정보 + 작업의 대상
    
    + 오라클과 MYSQL에서 데이터 사전
        1. 오라클
            - 데이터 사전 = 데이터 딕셔너리 View 테이블 이용 
            - 종류: DBA_XXX = DB의 모든 객체 조회 가능<br>
                    ALL_XXX = 자신 계정 + 타 계정의 모든 객체 조회 가능<br>
                    USER_XXX = 자신의 계정이 소유한 객체만 조회 가능
                    
        2. MySQL
             - 데이터 사전 = Information shcema에 존재한다

* 핵심 개념
    - 참조 무결성: 외래키 값은 null or 참조 릴레이션의 기본키 값과 동일[외래키 바꿈->참조의 기본키도 바꿈]
    - 개체 무결성: 기본키는 NOT NULL, 중복 값 X
-----------------
##### 고급 SQL 작성
* VIEW(뷰)
    - 개념: 논리 테이블(사용자 관점의 테이블), 가상 테이블(데이터를 직접 가지지 않는다!), 뷰는 table, view로부터 생성가능
    - 특징
        
            - view는 정의된 table이 변경되면 view도 변경
            - 외부 스키마 = view + 기본 table 정의
            - view는 삽입, 삭제, 갱신 제약을 받는다
            - CREATE로 생성, DROP으로 삭제된다
            - ALTER문으로 변경 불가 -> View는 한번 정의하면 변경 불가능하다!![삭제하고 다시 생성해야함]
            - 보안 측면에서 사용 가능
    - view의 장단점
        * view는 독립성 보장 + 단순성이 있어 사용됨
        
                 장점
                    - 논리적 독립성 제공(table 구조 변경->view를 사용하는 응용 프로그램 변경x)
                    - 사용자 데이터 관리 용이(단순 질의어 사용 가능)
                    - 데이터 보안 용이
                 단점
                    - 뷰 자체 인덱스 불가(인덱스는 물리적으로 저장된 data를 대상으로 함, view는 논리 테이블)
                    - view 정의 변경 불가(alter 사용불가)
                    - 데이터 변경 제약 존재(삽입, 삭제, 변경에 제약이 있음->view에 pk 해당 부분이 없으면 insert불가능)
    - 명령어
        + View 생성
            - CREATE [OR REPLACE] VIEW <view_name>(칼럼 목록) AS (쿼리문)[WITH READ ONLY]
            - 쿼리문:SELECT에서 ORDER BY, UNION빼고 사용 가능!
            - OR RELACE 옵션 사용 하면 View_name이 있어도 무시하고 새로운것 갱신[덮어쓰기]
            - WITH READ ONLY 옵션 사용 시 VIEW에 DML언어 사용 불가!
        + View 삭제
            - DROP VIEW <view_name> {CASCADE|RESTRCIT}
            
* 인덱스
   - 개념: 데이터를 빠르게 찾을 수 있는 수단, 과다한 사용->성능 저하, DB 튜닝 효과 볼수 있음, 칼럼 순서에 따라 인덱스 사용이 달라질수있음
            테이블에 질의 할 때 결과가 10~15%미만인 경우 딱 좋음
   - 특징
   
            - B-tree 원리 기반 (B-tree는 칼럼안에 독특한 데이터가 많을 때 용이!)
            - DB의 성능향상 수단으로 가장 일반적임
            - SQL문을 특별히 수정하지 않아도 성능 개선 기대 가능, 데이터 영향X -> 일정한 효과 기대
            
   - 사용
      + 인덱스는 특정 레코드의 위치를 알려주는 용도이고 자동 생성 X
      + PK칼럼 = 자동으로 인덱스 생성
   - 종류
        
            B-Tree: 단일 열 인덱스, Unique 인덱스, Non Unique인덱스, 함수기반 인덱스, DESCENDING INDEX, 결합 인덱스
            비트맵: 비트를 이용하여 칼럼값 저장하고 ROWID를 자동으로 생성하는 인덱스 방법, 구조:Index key값+start ROWID+end ROWID+Bitmap
   - 조작 방법
        - 인덱스 생성
            + CREATE [UNIQUE] INDEX <index_name> ON <table_name> (< columns>);
        - 인덱스 삭제
            + DROP INDEX <index_name>;
            + 인덱스는 테이블의 종속 구조로 생각하기 때문에 삭제 시 ALTER TABLE 명령 뒤에 DROP INDEX 명령 추가!
        - 인덱스 변경
            + ALTER [UNIQUE] INDEX <index_name> ON <table_name> (< columns>);
   - 인덱스 스캔 방식
        + Index Range Scan: 인덱스 root ~ leaf까지 수직적 탐색
        + Index Full Scan: leaf를 찾기 위해 수직적 탐색 후, 전체 leaf sequential을 스캔
        + Index Unique Scan: unique index를 '='조건으로 탐색하는 경우 작동
        + Index Skip Scan: branch 탐색 후 data가 있을 가능한 하위쪽만 access, 
        + Index Fast Full Scan: Index Full Scan보다 빠름, 트리구조 사용 x multiblock read하는 방식
        + Index Range Scan Descending: 인덱스를 뒤~앞까지 스캔

* 집합 연산자
    - 개념: 여러 질의 결과를 연결하여 하나로 결합하는 방식
    - 연산자 종류
           
            - UNION: 합집합
                SELECT c1, c2 UNION [DISTINCT|ALL] SELECT c3,c4  //질의문들의 칼럼 수 동일해야함, datatype도 동일해야함
            - UNION ALL: 중복되는 교집합 다 나옴
            - INTERSECT: 교집합
                SELECT c1, c2 INTERSECT SELECT c3,c4
            - MINUS: 차집합
                SELECT c1, c2 MINUS SELECT c3,c4 //먼저 위치한 SELECT문을 기준
                
* 조인
    - 개념: 두 테이블의 공통값을 이용하여 칼럼을 조합하는 수단[보통 PK, FK값 결합], RDB의 대표적인 핵심 기술, 데이터 연결 방법
    - 유형: 논리적 조인(사용자가 직접 제어) + 물리적 조인(튜닝 관점)
        1. 논리적 조인 (보통 외부 조인 이용)
            - 내부 조인 : 두 테이블에 공통으로 존재하는 칼럼을 이용하는 방식[NULL 값 허용X] (동등 조인, 자연 조인, 교차 조인)
            - 외부 조인 : 특정 테이블의 모든 데이터를 기준으로 다른 테이블의 정보 추출[타 테이블 정보 없어도 ㄱㅊ] (왼쪽/오른쪽/완전 외부 조인)
        
        2. 물리적 조인
            - Nested Loop Join
            - Merge Join
            - Hash Join
            
* 서브 쿼리
    - main 쿼리와 sub 쿼리는 주종 관계
    - 유형
        + 동작 방식에 따른 서브쿼리 유형
            - 비연관 서브쿼리 : 서브쿼리가 메인쿼리의 칼럼을 가지고 있지 않은 형태
            - 연관 서브쿼리 : 서브쿼리가 메인쿼리의 칼럼을 가지고 있는 형태(메인쿼리 선수행)
        + 반환 데이터 형식에 따른 서브쿼리 유형
            - 단일 행 서브 쿼리: 단일 행 비교 연산자 사용
            - 다중 행 서브 쿼리: 다중 행 비교 연산자(IN, ANY, SOME, EXISTS)사용
            - 다중 칼럼 서브 쿼리: 
