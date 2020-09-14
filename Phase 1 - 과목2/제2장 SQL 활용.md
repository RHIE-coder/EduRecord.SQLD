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
 - NATURAL JOIN과 마찬가지로 JOIN 칼럼에 대해서는 ALIAS나 테이블 이름과 같은 접두사를 붙일 수 없다.

 - Natural처럼 출력시 와일드카드(*)를 사용하면 공통된 칼럼이 맨앞에 위치하게 된다.

---

### 6. ON 조건절
JOIN 서술부(ON 조건절)와 비 JOIN 서술부(WHERE 조건절)를 분리하여 이해가 쉬우며, 칼럼명이 다르더라도 JOIN 조건을 사용할 수 있는 장점이 있다.

```sql
SELECT e.empno, e.ename, e.deptno, d.dname
FROM emp e JOIN dept d
ON (e.deptno = d.deptno)
```
 - 괄호는 옵션사항이다.
 - ON 조건절에 JOIN 조건 외에도 데이터 검색 조건을 추가할 수는 있으나 검색 조건 목적인 경우는 WHERE 절을 사용할 것을 권고한다.(다만, 아우터 조인에서 조인의 대상을 제한하기 위한 목적으로 사용되는 추가 조건의 경우는 ON 절에 표기되어야 한다.)

```sql
SELECT e.ename, e.mgr, d.deptno, d.dname
FROM emp e JOIN dept d
ON (e.deptno = d.deptno
    AND e.mgr = 7698);
```
or
```sql
SELECT e.ename, e.mgr, d.deptno, d.dname
FROM emp e JOIN dept d
ON (e.deptno = d.deptno)
WHERE e.mgr = 7698;
```

---

### 7. CROSS JOIN
E.F.CODD 박사가 언급한 일반 집합 연산자의 PRODUCT의 개념으로 JOIN 조건이 없는 경우 생길 수 있는 모든 데이터 조합을 말한다.
```sql
SELECT ename, dname
FROM emp CROSS JOIN dept
ORDER By ename;
```
 - NATURAL JOIN의 경우 WHERE 절에서 JOIN 조건을 추가할 수 없지만, CROSS JOIN의 경우 WHERE 절에 JOIN 조건을 추가할 수 있다. 그러나 INNER JOIN과 같은 결과를 얻기 때문에 구지 CROSS JOIN을 사용하기는....

---

### 8. OUTER JOIN
 - JOIN 조건에서 동일한 값이 없는 행도 반환할 때 사용할 수 있다. 즉, `tab1`과 `tab2`를 JOIN할 때 `tab2`의 JOIN 데이터가 없는 경우에도 `tab1`의 모든 데이터를 표시하고 싶을 수 있다.

 - 과거 OUTER JOIN을 위해 Oracle은 JOIN 칼럼 뒤에 '(+)'를 표시하였고, Sybase는 비교 연산자의 앞이나 뒤에 '(+)'를 표기했었는데, JOIN조건과 WHERE 절 검색 조건이 불명확한 단점, IN이나 OR 연산자 사용시 에러 발생. '(+)'표시가 누락된 칼럼 존재시 OUTER JOIN 오류 발생, FULL OUTER JOIN 미지원 등 불편함이 많았다.

 - OUTER JOIN 역시 JOIN 조건을 FROM 절에서 정의하겠다는 표시이므로 USING 조건절이나 ON 조건절을 필수적으로 사용해야 한다. 그리고 LEFT/RIGHT OUTER JOIN의 경우에는 기준이 되는 테이블이 조인 수행시 무조건 드라이빙 테이블이 된다. 

#### LEFT OUTER JOIN
 - JOIN 수행시 먼저 표기된 좌측 테이블에 해당하는 데이터를 먼저 읽은 후, 나중 표기도니 우측 테이블에서 JOIN 대상 데이터를 읽어 온다. 즉, A테이블, B테이블이 있을 때 A와 B를 비교했을 때 JOIN 칼럼에서 같은 값이 있을 때 그 해당 데이터를 가져오고 없으면 NULL로 채운다.

```sql
SELECT *
FROM emp LEFT OUTER JOIN dept
ON emp.deptno = dept.deptno;
```
 - OUTER 키워드 생략 가능

#### RIGHT OUTER JOIN
```sql
SELECT *
FROM emp RIGHT OUTER JOIN dept
ON emp.deptno = dept.deptno;
```
 - OUTER 키워드 생략 가능

#### FULL OUTER JOIN
 - LEFT OUTER JOIN과 RIGHT OUTER JOIN의 합집합. 단 UNION ALL이 아닌 UNION 기능과 같아서 중복되는 데이터 삭제
 - JOIN 수행시 좌측, 우측 테이블의 모든 데이터를 읽어 JOIN 하여 결과를 생성한다. 즉 A테이블, B테이블이 있을 때 둘다 기준이 된다는 것이다.

 ```sql
SELECT *
FROM employees e FULL OUTER JOIN departments d
ON e.department_id = d.department_id;
 ```

or

```sql
SELECT *
FROM employees e LEFT OUTER JOIN departments d
ON e.department_id = d.department_id;
UNION
SELECT *
FROM employees e RIGHT OUTER JOIN departments d
ON e.department_id = d.department_id;
```
 - OUTER 키워드 생략 가능

---

### 9. INNER vs OUTER vs CROSS JOIN 비교
![사진](../mdsrc/Phase1/과목2-2.1.jpg)

---

## 2절 : 집합 연산자(SET OPERATOR)

---