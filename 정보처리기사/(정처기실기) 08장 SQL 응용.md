# SQL 응용

* 응용 SQL

```SQL
SELECT S_EMP.NAME, S_EMP.DEPT_ID, S_DEPT.DEPT_NAME
FROM S_EMP, S_DEPT 
WHERE S_EMP.DEPT_ID = S_DEPT.DEPT_ID 
ORDER BY S_EMP.AGE;

SELECT 학생명 FROM 학적 WHERE 전화번호 IS NOT NULL;

SELECT * FROM 공급자 WHERE 공급자명 LIKE '%신%';

INSERT INTO 학생 VALUES(202001, '홍길동', 3, '시스템분석설계');

DELETE FROM 학적 WHERE 학생명='홍길동';

UPDATE 학적 SET 대학코드=01 WHERE 학부 LIKE '경영%';

GRANT UPDATE ON 테이블A TO Park;

DROP TABLE 학적 CASCADE;

SELECT 성명, AVG(점수) FROM 성적 GROUP BY 성명;
```

* 절차형 SQL

```plsql
CREATE OR REPLACE FUNCTION GET_AGE(V_BIRTH_DATE IN CHAR(8))

IS
BEGIN
		V_CURRENT_YEAR CHAR(4);
		V_BIRTH_YEAR CHAR(4);
		V_AGE NUMBER;
		SELECT TO_CHAR(SYSDATE,'YYYY')
					,SUBSTR(V_BIRTH_DATE,1,4)
    INTO V_CURRENT_YEAR
    		, V_BIRTH_YEAR
    FROM DUAL;
    SET AGE=TO_NUMBER(V_CURRENT_YEAR)-TO_NUMBER(V_BIRTH_YEAR) +1;
    
    RETURN AGE;
END;
		
```

```plsql
CREATE OR REPLACE TRIGGER PUT_EMPLOYEE_INFO_HISTORY
AFTER UPDATE
ON EMPLOYEE_INFO_T
FOR EACH ROW 
BEGIN
	INSERT INTO EMPLOYEE_INFO_H
						(EMPLOYEE_ID
            , SEQ_VAL
            , EMPLOYEE_NAME
            , EMPLOYEE_DEPT
            , EMPLOYEE_CP_NO
            )
            (:OLD.EMPLOYEE_ID
            , SEQ_VAL.NEXT_VAL
            , :NEW.EMPLOYEE_NAME
            , :NEW.EMPLOYEE_DEPT
            , :NEW.EMPLOYEE_CP_NO
            );
END;
```

```plsql
CREATE FUNCTION GET_SEX(S_성별 IN INT)
RETURN VARCHAR2
IS
BEGIN
		IF S_성별 =1 THEN
			RETURN '남';
		ELSE
			RETURN '여';
		END IF;
END;
```

```	plsql
CREATE FUNCTION CAL_SUM()
DECLARE
	i INT :=0;
	v_sum INT :=0;
BEGIN
		LOOP
			i:= i+1;
			v_sum = v_sum+1;
		EXIT WHEN i>=10;
		END LOOP;
		DBMS_OUTPUT.PUT_LINE(v_sum) ;
END;
```

-------



* 트리거 
  * 특정 테이블에 삽입,수정,삭제 등의 데이터 변경 이벤트가 발생하면 DBMS에서 자동적으로 실행되도록 구현된 프로그램
* 옵티마이저 
  * SQL을 가장 빠르고 효율적으로 수행할 최적의 처리경로를 생성해주는 DBMS 핵심엔진
  * 유형
    * 규칙기반 옵티마이저(RBO) : 통계 정보가 없는 상태에서 사전 등록된 규칙에 따라 질의 실행계획을 선택
    * 비용기반 옵티마이저(CBO) : 통계 정보로부터 모든 접근 경로를 고려한 질의 실행계획을 선택
* 힌트 
  * 실행하려는 SQL문에 사전에 정보를 주어 SQL문 실행에 빠른 결과를 가져오는 효과를 만드는 기법
  * 옵티마이저의 실행 계획을 원하는 대로 변경할 수 있게 함
  * 옵티마이저가 항상 최선의 실행 계획을 수립할 수 없어 힌트를 사용

