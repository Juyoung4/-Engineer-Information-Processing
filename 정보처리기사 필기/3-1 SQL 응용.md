#### 3-1 SQL 응용
##### 절차형 SQL 작성
* 트리거
    + 개념: 절차형 SQL -> DML 이벤트 발생 시 작동하는 프로그램, 트리거는 DB에 저장
    + 특징: 
        
            저장 프로시저와 작동은 비슷하지만 직접 실행시킬 수 없음[오직, 테이블 OR 뷰에 이벤트 발생시 실행]
            매개변수, 반환 값 사용 X
            DCL(TCL)[COMMIT, ROLLBACK] 사용 X
            트리거는 데이터 변경, 무결성 유지, 로그메시지 출력 등의 목적
    + 트리거 종류(타입)
            
            SQL문 실행 시기: BEFORE(SQL 실행 전 트리거 실행), AFTER(SQL 실행 후 트리거 실행)
            SQL문 실행 범위: ROW(각 ROW에 대해 한번씩 실행), STATEMENT(SQL문장에 대해 한번만 실행됨)
            
            (즉 트리거 = SQL문 실행 시기 하나 + SQL문 실행 범위 하나)
            
* 이벤트
    + 트리거를 실행시키는 이벤트 = 특정 시간 + 특정 쿼리 + 프로시저 + 함수
    + 트리거를 실행(Fire)시키는 이벤트
        
        - DML event: INSERT, DELETE, UPDATE
        - DDL event: CREATE, ALTER, DROP
        - DATABASE event: [AFTER] SERVERERROR, [AFTER] LOGON, [AFTER] STARTUP*, [BEFORE] LOGOFF, [BEFORE] SHUTDOWN
    + 이벤트와 트리거 작성 순서
        1. 인식이 가능한(요구사항 만족 가능한) 이벤트 정의 [<U>데이터의 트랜잭션 확인</U>]
            * 트랜잭션: 업무를 처리하기 위한 하나의 논리적인 작업 단위[일처리 단위, SQL의 묶음]<br>
                **트랜잭션의 특성: 원자성[commit, rollback], 일관성, 독립성, 영속성**
        2. 이벤트와 연관된 테이블 및 데이터 확인
        3. 기존 테이블 간의 상호 구조 및 결과값 반환을 위한 관련 데이터 분석
        4. 트리거의 데이터흐름 작성, 트리거 내부에 생성해야하는 변수를 분석
        5. 트리거 작성
        6. 트리거를 컴파일하여 DBMS에 반영
        
* 사용자 정의함수
    + 개념: 프로시저와 동일하게 절차형 SQL을 활용하여 일련의 연산 처리 결과를 단일 값으로 반환
        * 프로시저와 차이점: 처리 결과 -> 단일 값 반환
    + 사용자 정의 함수 특징
    
            내장함수처럼 사용가능
            복잡한 프로그래밍 가능
            RETURN문에서 특정 값(단일 값) 반환

* SQL 문법
    * MyBatis: SQL Mapping 기반 오픈소스 access framework[SQL 쿼리를 별도의 XML 파일로 분리->Mapping]
        + 장점: 복잡한 JDBC 코드 단순화, SQL 그대로 사용, Spring기반 프레임워크와 통합 기능, 우수한 성능
        - 예제  [#{} 는 input 파라미터 입력 받는 곳] 
        
            DQL,DMLSQL 문장의 input parameter값 받기
        
                <mapper>
                    <select id="findid" resultType="map">  //이 부분이 mybatis부분[select,update,insert,delete]
                        SELECT USER_ID
                            FROM USER_INFO
                            WHERE USER_NM = #{user_nm}
                            <if input_para="user_birth_day != null">
                              AND BIRTH_DAY=#{user_birth_day}
                            </if>
                    </select>
                </mapper>
            
        - 예제 [동적 SQL = SQL 구문 변경]
            
            동적SQL 사용 경우=컴파일 시 SQL문장 확정 X, PL/SQL블록상 DDL문 실행, ALTER SYSTEM/SESSION 명령어 실행
            
                <mapper>
                    <select id="findid" resultType="map">  //이 부분이 mybatis부분[select,update,insert,delete]
                        SELECT USER_ID
                            FROM USER_INFO
                            WHERE USER_NM = #{user_nm}  //user_nm은 무조건 받아야함
                            <if input_para="user_birth_day != null">
                              AND BIRTH_DAY=#{user_birth_day}
                            </if>
                            <if input_para="user_email" != null">
                              AND EMAL=#{user_email}
                            </if>  
                    </select>
                </mapper>
                
        + 절차형 SQL 호출
        
                <select id="execSakesDailyBatch" resultType="map" statementType="CALLABLE">
                    {CALL RUN_SALES_AMT_BATCH(#{TARGET_DATE})} //프로시저 호출 전 "cALL"문장 사용
                </select>
                
* 프로시저 vs 트리거
    - 프로시저: CREATE PROCEDURE, 생성하면 소스코드+실행코드 생성, EXCUTE 명령어로 실행, COMMIT-ROLLBACK 실행가능
    - 트리거: CREATE TRIGGER, 생성하면 소스코드+실행코드 생성, 생성 후 자동실행, COMMIT-ROLLBACK 실행X
    
-------------------------------------------
##### 응용 SQL 작성
* DML(데이터 조작어) = 데이터 관점에서 생명 주기 제어
    + INSERT
        - INSERT INTO table_name (colum1, colum2, ..) VALUES (value1, value2);
        - INSERT INTO table_name VALUES (value1, value2);
        
            빈 속성값 = NULL
    +  SELECT
        - SELECT [DISTINCT/ALL] (column / *[와일드카드]) FROM table_name WHERE 조건명
        * 자주 사용되는 함수
        
                 SUM, AVG, MIN, MAX, COUNT, COUN_BIG(결과 값이 bigint), STDEV(표준 편차), VARIAN(분산)
                 (EX) SELECT SUM(등급) FROM 테이프;
        *  다른 연산자
        
                    BETWEEN: SELECT 성명 FROM 회원 WHERE 20 BETWEEN 30; //20포함 30까지 성명 검색
                    IN: SELECT 제목,등급 FROM 테이프 WHERE 등급 in (1,3); //등급이 1또는 3인 경우 출력[이산적인 값만 가능]
                    IS (NOT) NULL: SELECT 제목 FROM 테이프 WHERE 장르 IS NULL
                    LIKE '%': SELECT 성명 FROM 회원 WHERE 성명 LIKE '이%' //_는 한개 문자 의미!, 숫자 사용X
                    ANY: SELECT 성명,전화번호 FROM 회원 WHERE 회원번호=ANY (SELECT 회원번호 FROM 대여 WHERE 테이프번호='T3');
                    //하위 쿼리 둘 이상 반환->비교연산자 사용X, ANY는 하위쿼리 여러개중 하나만 만족해도 OK
    + UPDATE
         - UPDATE table_name SET colum1 = value1m, colum2=value2, ... [WHERE 절];
                
                UPDATE 회원 SET 전화번호="234-8765" WHERE 회원번호 IN (SELECT 회원번호 FROM~)
                UPDATE 회원 SET 회원번호 = (SELECT 회원번호 FROM 회원 WHERE 전화번호="234-~~") WHERE 테이프 번호="T5";
                
    + DELETE
        - DELETE FROM table_name [WHERE 절];
        - DELETE FROM table_name; //table 전체 삭제

* DCL(데이터 조절어?) = DB 접근, 객체에 권한 부여, DB에서 데이터 말고 오브젝트에 대해 조작하는 언어, 접근 통제 용
    - DCL는 DBA가 관리
    - 오브젝트 = 테이블, 뷰, 인덱스, 시노님[객체 별칭], 시퀀스, 함수, 프로시저, 패키지
    - DCL 유형
        
            사용자 권한 - 접근 통제[사용자 등록, 권리 부여] => GRANT, REVOKE
            트랜잭션 - 안정된 거래 보장[동시에 다수의 작업을 독립적으로 처리]
            TCL - COMMIT(확정), ROLLBACK(취소-복구), CHECKPOINT(설정-복귀 지점 설정)
    - DCL 활용
        * 권한 = 시스템 권환 + 객체 권환
        1. 사용자 권한 부여
            
                시스템 권한 = GRANT 권한1, 권한2 TO 사용자 계정
                            권한=CREATE USER/SESSION/TABLE/VIEW/SEQUENCE/PROCEDURE, DROP USER, DROP ANY TABLE
                객체 권환 = GRANT 권한1, 권한2 ON 객체명 TO 사용자 계정
                            권한 = INSERT, DELETE, SELECT, UPDATE, ALTER, EXECUTE
        2. 사용자 권한 회수
        
                시스템 권한 = REVOKE 권한1, 권한2 TO 사용자 계정
                객체 권환 = REVOKE 권한1, 권한2 ON 객체명 TO 사용자 계정
    - 접근 통제 정책에 의한 유형
        + 임의 접근 통제(DAC): 개인-그룹의 식별자 기반으로 제한, 통제 권한이 주체한테 있음, 주체가 임의적으로 접근 통제 권한을 배분하며 제어
        + 강제 접근 통제(MAC): 쌍방의 보안 레이블에 기초하여 객체(높은 보안)가 주체(낮은 보안)에게 노출되지 않도록 접근 제한, 통제 권한=제3자, 주체는 접근 통제와 무관
        + 역할기반 접근 통제(BRAC): 사용자가 주어진 역할에 대한 접근 권한 부여 받음(사용자 바뀌어도 역할은 그대로), DB/OS 작업권한 통제
        * DBMS가 채택한 접근 통제 정책 = DAC 방식
        
* TCL(Transaction control language) = 일 처리 단위, 논리적 연산 단위, SQL 묶음(하나 이상), 분할 할 수 X 최소 단위
    + 트랜잭션 제어 = 트랜잭션의 결과를 수용 OR 취소
        - EX
        
                1) TX 시작
                2) INSERT
                3) SAVEPOINT A
                4) UPDATE
                5) SAVEPOINT B
                6) DELETE
                - 여기서 ROLLBACK 경우 = 1번까지 취소
                - ROLLBACK TO A = 1~3 영향X, 4~6 취소
                - ROLLBACK TO B = 1~5 영향X, 6 취소
    + Auto Commit = COMMIT문이 없어도 DML이 성공적으로 수행되면 자동 COMMIT됨
                
                SHOW AUTOCOMMIT
                SET AUTOCOMMIT [ON/OFF] /둘중 하나 선택
                
* 윈도우 함수 = 테이블에서 로우 집합을 대상으로 계산하는 함수
    - 집계함수와의 차이점: 집계함수는 결과가 하나의 로우이지만, 윈도우 함수는 <u>로우 단위</u>로 처리 결과를 출력
    + 윈도우 함수 종류
    
        |   구분     |            종류              |   지원
        |---|:---:|---:|
        | 순위(rank) | RANK(2위 3개-> 2위,2위,2워,5위,..), DENSE_RANK(2위 3개-> 2위,2위,2위,3위,..), ROW_NUMBER(2위 3개->2위,3위,4위,5위,..)| 대부분 지원
        |집계| SUM, MAX, MIN, AVG, COUNT| Orderby 지원 X
        |순서|FIRST_VALUE(MIN과 동일), LAST_VALUE(MAX와 동일), LAG(1부터 이전 몇 번째 행 값), LEAD(1부터 이후 몇번째 행 값)|오라클만 지원
        |그룹 내 비율|CUME_DIST, PERCENT_RANK, NTILE,RATIO_TO_REPORT|PERCENT_RANK(ANSI/ISO, 오라클 DBMS), NTILE(오라클,SQL 서버), RATIO~(오라클)
        |선형분석+통계분석|...|..
         
    + 윈도우 함수 구문
        
            SELECT <WINDOWS_FUNCTION> [ARGUMENTS] OVER 
                    ([PARTITION BY <COLUMN_1, COLUMN_2, ...>]) 
                    //partition by는 선택사항[순위를 정할 대상 범위]
                    //즉, partition by는 group by 역할을 함[단, group by의 집약 기능 x->row수 줄어들지 X]
                    [ORDER BY <COLUMN_A, COLUMN_B,...>]
            FROM table_name
            
            (ex) 사원 데이터에서 급여가 높은 순 + JOB별로 급여가 높은 순 동시 출력
            SELECT JOB, ENAME, SAL,
                 RANK() OVER (ORDER BY SAL DESC) ALL_RANK,
                 RANK() OVER (PARTITION BY JOB ORDER BY SAL DESC) JOB_RANK
            FROM EMP; 
    
    + GROUP BY 특성
       - NULL 값을 가지는 ROW는 제외 후 산출
       - ALIAS 사용 X
       - WHERE절은 GROUP BY보다 먼저, WHERE절안에 GROUP BY 사용 X
       - HAVING = GROUP BY의 조건절(WHERE문 절과 비슷)
       
             (EX) 사원들을 급여 기준으로 정렬, 본인 급여보다 50적거나, 150만큼 많은 인원수 출력[본인 급여-50 ~ 본입급여+150]
             SELECT ENAME, SAL, 
                 COUNT(*) OVER (ORDER BY SAL
                 RANGE BETWEEN 50 PRECEDING AND 150 FOLLOWING) as SIM_CNT //이부분 알아두기
                 FROM EMP; 
    
    * 그룹 함수 = 집계함수와 유사하지만 집계함수보다 큰 범위
        - 그룹함수: 중간 합계 분석 데이터를 산출 [집계함수 사용-> UNION ALL 사용, 그룹 함수->단일 DQL 사용]
        - 유형
            + ROLLUP: 소그룹 간의 소계 계산
                
                    (EX) 부서명, 업무명 기준으로 집계한 GROUP BY 문장에 ROLLUP함수를 서용한다
                        SELECT DNAME, JOB FROM EMP, DEPT
                        WHERE DEPT.DEPTNO = EMP.DEPTNO
                        GROUP BY ROLLUP (DNAME, JOB)//ROLLUP의 칼럼은 계층별로 구성되기 때문에 순서 바꾸면 수행 결과도 다름
                        ORDER BY DNAME, JOB;
            + CUBE: GROUP BY 항목들 간 다차원적인 소계 계산, 계산이 많다(양쪽 쿼리에서 모두 수행 후 한쪽에서 제거)
                * ROLLUP과 달리 순서를 다르게 해도 표시만 다를뿐 집계결과는 동일
                
                        (EX)
                            SELECT DNAME, JOB FROM EMP, DEPT
                            WHERE DEPT.DEPTNO = EMP.DEPTNO
                            GROUP BY CUBE (DNAME, JOB)
            + GROUPUNG SETS: 특정 항목에 대한 소계 계산, 다양한 소계 집합 만든다(소계 집합 별로 개별 집계 구함)
                * ROLLUP, CUBE와 상관없이 순서와 무관
                
                        (EX)
                            SELECT DNAME, JOB FROM EMP, DEPT
                            WHERE DEPT.DEPTNO = EMP.DEPTNO
                            GROUP BY GROUPING SETS(DNAME, JOB)
                            
* 오류 처리
    - 프로그램밍 = 코딩 + 컴파일 + 실행과정
    - 에러 처리 구문: TRY/CATCH 문
    - 구문
    
            BEGIN Try
                //원래 sql
            End Try
            Begin Catch
                //begin try에서 오류 발생하면 처리할 일들
            End catch
    - 오류 처리 구문(오라클 경우)
        * 오류 종류 = 컴파일 오류(파싱 시 발생) + 런타임 오류(실행하는 동안 발생=exception)
        
