# [ 2장 ] SQL 활용

 - 본 문서의 SQL 예제의 벤더는 OracleDB를 기준으로 작성되었습니다.

## 1절 : 표준 조인(STANDARD JOIN)

### 1. STANDARD SQL 개요
 - 현재 사용하는 SQL의 많은 기능이 관계형 데이터베이스의 이론을 수립한 E.F.Codd 박사의 논문에 언급이 되어 있다. 논문에 언급된 8가지 관계형 대수는 각각 4개의 일반 집합 연산자와 순수 관계 연산자로 나눌 수 있으며, 관계형 데이터베이스 엔진 및 SQL의 기반 이론이 되었다.

 - 일반 집합 연산자를 현재의 SQL과 비교하면
1. UNION 연산은 UNION 기능으로,
2. INTERSECTION 연산은 INTERSECT 기능으로,
3. DIFFERENCE 연산은 EXCEPT(Oracle은 MINUS) 기능으로,
4. PRODUCT 연산은 CROSS JOIN 기능으로 구현되었다.

#### UNION 연산
 수학적 합집합을 제공하기 위해 공통 교집합의 중복을 없애기 위한 사전 작업으로 시스템에 부하를 주는 사전 정렬 작업이 발생한다. `UNION`은 정렬이 발생하지만 `UNION ALL`은 정렬이 발생하지 않아 둘의 출력 결과가 같다면 응답 속도 향상이나 자원 효율화 측면에서 데이터 정렬 작업이 발생하지 않은 UNION ALL을 사용하는 것을 권장한다.

 - 순수 관계 연산자를 현재의 SQL 문장과 비교하면
5. SELECT 연산은 WHERE 절로 구현되었다.
6. PROJECT 연산은 SELECT 절로 구현되었다.
7. (NATURAL) JOIN 연산은 다양한 JOIN 기능으로 구현되었다.
8. DIVIDE 연산은 현재 사용되지 않는다.

#### SELECT 연산과 SELECT 절은 다르다.
#### JOIN 연산
|WHERE절|INNER JOIN|
|:---:|:---|
|**FROM 절**|**NATRURAL JOIN, INNER JOIN, OUTER JOIN, USING 조건절, ON 조건절 등**|

 - 관계형 데이터베이스는 다음과 같은 절차를 거친다.
    - 1. 요구사항 분석
    - 2. 개념적 데이터 모델링
    - 3. 논리적 데이터 모델링
    - 4. 물리적 데이터 모델링

 - 정규화 과정의 경우 데이터 정합성과 데이터 저장 공간의 절약을 위해 엔터티를 최대한 분리하는 작업으로, 일반적으로 3차 정규형이나 보이스코드 정규형까지 진행하게 된다.

---

### 2. FROM 절 JOIN 형태

 - INNER JOIN은 WHERE 절부터 사용하던 JOIN의 DEFAULT 옵션으로 JOIN 조건에서 동일한 값이 있는 행만 반환한다.

 - NATURAL JOIN은 INNER JOIN의 하위 개념으로 NATRUAL JOIN은 두 테이블 간의 동일한 이름을 갖는 모든 칼럼들에 대해 EQUI(=) JOIN을 수행한다. NATURAL INNER JOIN이라고도 표시할 수 있으며, 결과는 NATURAL JOIN과 같다.

 - 과거 WHERE 절에서 JOIN 조건과 데이터 검증 조건이 같이 사용되어 용도가 불분명한 경우가 발생할 수 있었는데, WHERE 절의 JOIN 조건을 FROM 절의 ON 조건절로 분리하여 표시함으로써 사용자가 이해하기 쉽도록 한다.


---

### 3. INNER JOIN

INNER JOIN은 OUTER JOIN과 대비하여 내부 JOIN이라고 하며 JOIN 조건에서 동일한 값이 있는 행만 반환한다. INNER JOIN 표시는 그동안 WHERE 절에서 사용하던 JOIN 조건을 FROM 절에서 정의하겠다는 표시이므로 USING 조건절이나 ON 조건절을 필수적으로 사용해야 한다.

#### WHERE 절 JOIN 조건
```sql
SELECT emp.deptno, empno, ename, dname
FROM emp, dept
WHERE emp.deptno = dept.deptno;
```

#### FROM 절 JOIN 조건
```sql
SELECT emp.deptno, empno, ename, dname
FROM emp INNER JOIN dept 
ON emp.deptno = dept.deptno
```

#### INNER JOIN의 DEFAULT 옵션으로 아래 SQL문과 같이 생략 가능하다.
```sql
SELECT emp.deptno, empno, ename, dname
FROM emp JOIN dept
ON emp.deptno = dept.deptno;
```

---

### 4. NATURAL JOIN

NATURAL JOIN은 두 테이블 간의 **동일한 이름을 갖는 모든 칼럼에 대해** EQUI(=) JOIN을 수행한다. NATURAL JOIN이 명시되면, 추가로 USING 조건절, ON 조건절, WHERE 절에서 JOIN 조건을 정의할 수 없다. 

```sql
SELECT *
FROM emp NATURAL JOIN dept;
```
별도의 JOIN 칼럼을 지정하지 않았지만, 두 개의 테이블에서 deptno이라는 공통된 칼럼을 자동으로 인식하여 JOIN을 처리한 것이다. JOIN에 사용된 칼럼들은 같은 데이터 유형이어야 하며, ALIAS나 테이블명과 같은 접두사를 붙일 수 없다.
```sql
SELECT emp.deptno, empno, ename, dname
FROM emp NATURAL JOIN dept;

ERROR : NATURAL JOIN에 사용된 열은 식별자를 가질 수 없음
```

 - Inner은 컬럼이 따로, Natural은 합쳐지면서 맨 앞에 위치

---

### 5. USING 조건절
NATURAL JOIN에서는 모든 일치되는 칼럼들에 대해 JOIN이 이루어지지만, FROM 절의 USING 조건절을 이용하면 같은 이름을 가진 칼럼들 중에서 원한느 칼럼에 대해서만 선택적으로 EQUI JOIN을 할 수 있다. 

```sql
SELECT *
FROM emp JOIN dept
USING (deptno);
```

---

### 6. ON 조건절

---

### 7. CROSS JOIN

---

### 8. OUTER JOIN

---

### 9. INNER vs OUTER vs CROSS JOIN 비교

---

## 2절 : 집합 연산자(SET OPERATOR)

---