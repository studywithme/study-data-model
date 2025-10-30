# 3-3. PK, 데이터 구조를 지배하는 마스터키 🔑

## 📖 학습 목표
- 기본키(Primary Key)의 개념과 중요성 이해
- 효과적인 기본키 설계 방법 학습
- 기본키가 데이터 구조에 미치는 영향 파악

## 🎯 기본키(Primary Key)란?

### 정의
**기본키(PK)**는 테이블의 각 행을 유일하게 식별하는 컬럼 또는 컬럼의 조합으로,
데이터베이스에서 가장 중요한 제약조건입니다.

### 열쇠와의 비유
```
물리적 열쇠        기본키
─────────────────  ─────────────────
집 문을 여는 유일한 키  →  행을 찾는 유일한 키
복사 불가능한 고유성    →  중복 불가능한 고유성
분실하면 큰 문제       →  없으면 데이터 무결성 문제
```

## 🔍 기본키의 특성

### 1. 유일성 (Uniqueness)
```sql
-- 기본키는 테이블 내에서 유일해야 함
CREATE TABLE 고객 (
    고객ID VARCHAR(10) PRIMARY KEY,  -- 유일한 값만 허용
    고객명 VARCHAR(50),
    이메일 VARCHAR(100)
);

-- 중복 값 입력 시도 시 오류 발생
INSERT INTO 고객 VALUES ('CU00000001', '홍길동', 'hong@email.com');
INSERT INTO 고객 VALUES ('CU00000001', '김철수', 'kim@email.com');  -- 오류!
```

### 2. 비어있지 않음 (Not Null)
```sql
-- 기본키는 NULL 값을 가질 수 없음
INSERT INTO 고객 VALUES (NULL, '이영희', 'lee@email.com');  -- 오류!
INSERT INTO 고객 VALUES ('', '박민수', 'park@email.com');   -- 오류!
```

### 3. 불변성 (Immutability)
```sql
-- 기본키 값은 가급적 변경하지 않아야 함
-- 변경 시 참조하는 모든 외래키도 함께 변경 필요
UPDATE 고객 SET 고객ID = 'CU00000999' WHERE 고객ID = 'CU00000001';
-- 이 경우 주문, 결제 등 모든 관련 테이블의 외래키도 변경 필요
```

## 🏗️ 기본키 설계 전략

### 전략 1: 자연키 (Natural Key)
```
정의: 비즈니스에서 자연스럽게 사용되는 식별자

장점:
✅ 비즈니스 의미가 명확
✅ 사용자가 이해하기 쉬움
✅ 별도 키 생성 불필요

단점:
❌ 값이 변경될 가능성
❌ 복합키가 될 가능성
❌ 성능 이슈 가능성
```

#### 자연키 예시
```sql
-- 주민등록번호를 기본키로 사용
CREATE TABLE 개인고객 (
    주민등록번호 VARCHAR(13) PRIMARY KEY,
    고객명 VARCHAR(50),
    생년월일 DATE
);

-- 사업자등록번호를 기본키로 사용  
CREATE TABLE 법인고객 (
    사업자등록번호 VARCHAR(10) PRIMARY KEY,
    법인명 VARCHAR(100),
    대표자명 VARCHAR(50)
);
```

### 전략 2: 대리키 (Surrogate Key)
```
정의: 시스템에서 인위적으로 생성하는 식별자

장점:
✅ 값이 변경되지 않음
✅ 단순한 구조 (단일 컬럼)
✅ 성능 최적화 용이
✅ 비즈니스 규칙 변경에 독립적

단점:
❌ 비즈니스 의미 없음
❌ 별도 키 생성 메커니즘 필요
❌ 저장 공간 추가 필요
```

#### 대리키 예시
```sql
-- 시퀀스를 이용한 대리키
CREATE SEQUENCE 고객_SEQ START WITH 1;

CREATE TABLE 고객 (
    고객ID NUMBER PRIMARY KEY,           -- 대리키
    주민등록번호 VARCHAR(13) UNIQUE,     -- 자연키는 유니크 제약조건
    고객명 VARCHAR(50),
    생년월일 DATE
);

-- 의미있는 형태의 대리키
CREATE TABLE 주문 (
    주문번호 VARCHAR(15) PRIMARY KEY,    -- OR + YYYYMMDD + 순번
    고객ID NUMBER,
    주문일자 DATE,
    총금액 NUMBER
);
```

### 전략 3: 복합키 (Composite Key)
```
정의: 여러 컬럼을 조합하여 만든 기본키

사용 시기:
- 자연스러운 단일 식별자가 없는 경우
- 연결 테이블 (M:N 관계 해결)
- 이력 테이블 (시간 차원 포함)
```

#### 복합키 예시
```sql
-- 주문상세 테이블 (주문번호 + 순번)
CREATE TABLE 주문상세 (
    주문번호 VARCHAR(15),
    순번 NUMBER,
    상품코드 VARCHAR(10),
    수량 NUMBER,
    단가 NUMBER,
    PRIMARY KEY (주문번호, 순번)
);

-- 고객등급이력 테이블 (고객ID + 적용일자)
CREATE TABLE 고객등급이력 (
    고객ID NUMBER,
    적용일자 DATE,
    등급코드 VARCHAR(10),
    변경사유 VARCHAR(200),
    PRIMARY KEY (고객ID, 적용일자)
);
```

## 📊 기본키 설계 가이드라인

### 가이드라인 1: 단순성 우선
```
권장사항:
✅ 단일 컬럼 기본키 선호
✅ 숫자형 데이터 타입 선호
✅ 고정 길이 선호

예시:
-- 좋은 예
고객ID NUMBER(10)
주문번호 VARCHAR(15)

-- 피해야 할 예  
복합키 (고객명, 생년월일, 전화번호)
가변길이 (이메일주소)
```

### 가이드라인 2: 안정성 보장
```
고려사항:
- 값이 변경될 가능성 최소화
- 비즈니스 규칙 변경에 독립적
- 외부 시스템 의존성 최소화

예시:
-- 안정적인 키
고객ID (시스템 생성)
주문번호 (규칙 기반 생성)

-- 불안정한 키
이메일주소 (사용자가 변경 가능)
전화번호 (통신사 변경 시 변경)
```

### 가이드라인 3: 성능 고려
```
성능 최적화 요소:
- 인덱스 효율성
- 조인 성능
- 저장 공간 효율성
- 정렬 성능

예시:
-- 성능 우수
NUMBER(10)        -- 4바이트, 빠른 비교
VARCHAR(10)       -- 고정 길이, 예측 가능

-- 성능 저하 가능
VARCHAR(100)      -- 가변 길이, 큰 크기
CLOB              -- 대용량, 느린 비교
```

## 🎨 기본키 명명 규칙

### 규칙 1: 일관된 패턴
```
패턴 예시:
[테이블명] + ID
- 고객ID, 상품ID, 주문ID

[테이블명] + 번호  
- 주문번호, 송장번호, 계약번호

[테이블명] + 코드
- 상품코드, 지역코드, 부서코드
```

### 규칙 2: 의미있는 형식
```sql
-- 의미있는 기본키 형식
CREATE TABLE 주문 (
    주문번호 VARCHAR(15) PRIMARY KEY  -- OR + YYYYMMDD + 6자리순번
    -- 예: OR202401150000001
);

CREATE TABLE 상품 (
    상품코드 VARCHAR(10) PRIMARY KEY  -- 카테고리(2) + 브랜드(2) + 순번(6)
    -- 예: ELSA000001 (전자제품-삼성-순번)
);

CREATE TABLE 고객 (
    고객ID VARCHAR(10) PRIMARY KEY    -- CU + 8자리순번
    -- 예: CU12345678
);
```

## 🔧 기본키와 성능

### 인덱스 자동 생성
```sql
-- 기본키 생성 시 자동으로 유니크 인덱스 생성
CREATE TABLE 고객 (
    고객ID NUMBER PRIMARY KEY,  -- 자동으로 인덱스 생성됨
    고객명 VARCHAR(50)
);

-- 조회 성능 최적화
SELECT * FROM 고객 WHERE 고객ID = 12345;  -- 인덱스 사용으로 빠른 조회
```

### 클러스터링 효과
```sql
-- 기본키 순서로 데이터가 물리적으로 정렬됨 (DBMS에 따라 다름)
-- 범위 조회 시 성능 향상
SELECT * FROM 주문 
WHERE 주문번호 BETWEEN 'OR20240101000001' AND 'OR20240131999999';
```

### 외래키 참조 성능
```sql
-- 기본키를 참조하는 외래키 조인 성능 최적화
SELECT c.고객명, o.주문일자, o.총금액
FROM 고객 c
JOIN 주문 o ON c.고객ID = o.고객ID  -- 기본키-외래키 조인으로 빠른 성능
WHERE c.고객ID = 12345;
```

## 💡 기본키 설계 실무 팁

### 팁 1: 비즈니스 키와 기술 키 분리
```sql
-- 기술적 기본키 + 비즈니스 유니크 키 조합
CREATE TABLE 고객 (
    고객ID NUMBER PRIMARY KEY,              -- 기술적 기본키 (대리키)
    고객번호 VARCHAR(10) UNIQUE NOT NULL,   -- 비즈니스 키 (자연키)
    고객명 VARCHAR(50),
    이메일 VARCHAR(100) UNIQUE
);
```

### 팁 2: 의미있는 키 생성 함수
```sql
-- 주문번호 생성 함수 예시
CREATE OR REPLACE FUNCTION 주문번호생성
RETURN VARCHAR2
IS
    v_seq NUMBER;
    v_order_no VARCHAR2(15);
BEGIN
    SELECT 주문번호_SEQ.NEXTVAL INTO v_seq FROM DUAL;
    v_order_no := 'OR' || TO_CHAR(SYSDATE, 'YYYYMMDD') || 
                  LPAD(v_seq, 6, '0');
    RETURN v_order_no;
END;
```

### 팁 3: 기본키 변경 시 고려사항
```sql
-- 기본키 변경 전 영향도 분석
SELECT table_name, constraint_name
FROM user_constraints
WHERE r_constraint_name = 'PK_고객';  -- 고객 테이블을 참조하는 외래키 조회

-- 변경 순서
-- 1. 외래키 제약조건 삭제
-- 2. 기본키 값 변경
-- 3. 참조 테이블의 외래키 값 변경
-- 4. 외래키 제약조건 재생성
```

## 🎯 체크리스트: 좋은 기본키 설계인가?

### 기능적 측면
- [ ] 각 행을 유일하게 식별하는가?
- [ ] NULL 값이 없는가?
- [ ] 값이 변경될 가능성이 낮은가?
- [ ] 비즈니스 규칙과 독립적인가?

### 성능적 측면
- [ ] 인덱스 효율성이 좋은가?
- [ ] 조인 성능이 최적화되는가?
- [ ] 저장 공간이 효율적인가?
- [ ] 정렬 성능이 좋은가?

### 관리적 측면
- [ ] 명명 규칙을 준수했는가?
- [ ] 일관된 패턴을 사용했는가?
- [ ] 이해하기 쉬운 형식인가?
- [ ] 확장성을 고려했는가?

---

**다음**: [3-4. FK, 부모 없이는 살 수 없지만](./04-foreign-key.md)