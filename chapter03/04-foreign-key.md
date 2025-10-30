# 3-4. FK, 부모 없이는 살 수 없지만 🔗

## 📖 학습 목표
- 외래키(Foreign Key)의 개념과 역할 이해
- 참조 무결성의 중요성 인식
- 효과적인 외래키 설계 방법 학습

## 🎯 외래키(Foreign Key)란?

### 정의
**외래키(FK)**는 다른 테이블의 기본키를 참조하는 컬럼으로,
테이블 간의 관계를 정의하고 참조 무결성을 보장하는 제약조건입니다.

### 가족 관계와의 비유
```
가족 관계              외래키 관계
─────────────────    ─────────────────
부모-자식 관계    →   부모테이블-자식테이블
부모 없는 자식 불가 →  참조 대상 없는 FK 불가
가족 구성원 확인   →   참조 무결성 검사
```

## 🔍 외래키의 특성

### 1. 참조 무결성 (Referential Integrity)
```sql
-- 부모 테이블 (고객)
CREATE TABLE 고객 (
    고객ID NUMBER PRIMARY KEY,
    고객명 VARCHAR(50),
    이메일 VARCHAR(100)
);

-- 자식 테이블 (주문)
CREATE TABLE 주문 (
    주문번호 VARCHAR(15) PRIMARY KEY,
    고객ID NUMBER,                    -- 외래키
    주문일자 DATE,
    총금액 NUMBER,
    FOREIGN KEY (고객ID) REFERENCES 고객(고객ID)
);

-- 참조 무결성 위반 시 오류 발생
INSERT INTO 주문 VALUES ('OR202401150000001', 999, SYSDATE, 50000);
-- 오류: 고객ID 999가 고객 테이블에 존재하지 않음
```

### 2. 연쇄 작업 (Cascade Operations)
```sql
-- 연쇄 삭제 (CASCADE DELETE)
ALTER TABLE 주문 
ADD CONSTRAINT FK_주문_고객ID 
FOREIGN KEY (고객ID) REFERENCES 고객(고객ID) 
ON DELETE CASCADE;

-- 부모 레코드 삭제 시 자식 레코드도 함께 삭제
DELETE FROM 고객 WHERE 고객ID = 1;  -- 해당 고객의 모든 주문도 삭제됨

-- 연쇄 수정 (CASCADE UPDATE)  
ON UPDATE CASCADE;  -- 부모 키 변경 시 자식 키도 함께 변경
```

### 3. 제한 작업 (Restrict Operations)
```sql
-- 삭제 제한 (RESTRICT/NO ACTION)
ALTER TABLE 주문 
ADD CONSTRAINT FK_주문_고객ID 
FOREIGN KEY (고객ID) REFERENCES 고객(고객ID) 
ON DELETE RESTRICT;

-- 자식 레코드가 있으면 부모 레코드 삭제 불가
DELETE FROM 고객 WHERE 고객ID = 1;  
-- 오류: 해당 고객의 주문이 존재하여 삭제 불가
```

## 🏗️ 외래키 설계 패턴

### 패턴 1: 단순 참조 (Simple Reference)
```sql
-- 1:N 관계의 기본 패턴
CREATE TABLE 부서 (
    부서ID NUMBER PRIMARY KEY,
    부서명 VARCHAR(50)
);

CREATE TABLE 직원 (
    직원ID NUMBER PRIMARY KEY,
    직원명 VARCHAR(50),
    부서ID NUMBER,                    -- 외래키
    FOREIGN KEY (부서ID) REFERENCES 부서(부서ID)
);
```

### 패턴 2: 다중 참조 (Multiple References)
```sql
-- 하나의 테이블이 여러 테이블을 참조
CREATE TABLE 주문 (
    주문번호 VARCHAR(15) PRIMARY KEY,
    고객ID NUMBER,                    -- 고객 테이블 참조
    배송지ID NUMBER,                  -- 배송지 테이블 참조
    쿠폰ID NUMBER,                    -- 쿠폰 테이블 참조
    주문일자 DATE,
    FOREIGN KEY (고객ID) REFERENCES 고객(고객ID),
    FOREIGN KEY (배송지ID) REFERENCES 배송지(배송지ID),
    FOREIGN KEY (쿠폰ID) REFERENCES 쿠폰(쿠폰ID)
);
```

### 패턴 3: 자기 참조 (Self Reference)
```sql
-- 계층 구조를 표현하는 자기 참조
CREATE TABLE 부서 (
    부서ID NUMBER PRIMARY KEY,
    부서명 VARCHAR(50),
    상위부서ID NUMBER,                -- 자기 자신을 참조
    FOREIGN KEY (상위부서ID) REFERENCES 부서(부서ID)
);

-- 데이터 예시
INSERT INTO 부서 VALUES (1, '본사', NULL);        -- 최상위 부서
INSERT INTO 부서 VALUES (2, '영업본부', 1);       -- 본사 하위
INSERT INTO 부서 VALUES (3, '개발본부', 1);       -- 본사 하위
INSERT INTO 부서 VALUES (4, '영업1팀', 2);        -- 영업본부 하위
```

### 패턴 4: 복합 외래키 (Composite Foreign Key)
```sql
-- 복합 기본키를 참조하는 복합 외래키
CREATE TABLE 주문상세 (
    주문번호 VARCHAR(15),
    순번 NUMBER,
    상품코드 VARCHAR(10),
    수량 NUMBER,
    PRIMARY KEY (주문번호, 순번)
);

CREATE TABLE 배송상세 (
    배송번호 VARCHAR(15) PRIMARY KEY,
    주문번호 VARCHAR(15),             -- 복합 외래키 1
    순번 NUMBER,                      -- 복합 외래키 2
    배송수량 NUMBER,
    FOREIGN KEY (주문번호, 순번) REFERENCES 주문상세(주문번호, 순번)
);
```

## 📊 외래키 제약조건 옵션

### ON DELETE 옵션

#### CASCADE
```sql
-- 부모 삭제 시 자식도 함께 삭제
FOREIGN KEY (고객ID) REFERENCES 고객(고객ID) ON DELETE CASCADE;

예시:
고객 삭제 → 해당 고객의 모든 주문 자동 삭제
```

#### SET NULL
```sql
-- 부모 삭제 시 자식의 외래키를 NULL로 설정
FOREIGN KEY (관리자ID) REFERENCES 직원(직원ID) ON DELETE SET NULL;

예시:
관리자 퇴사 → 해당 관리자가 관리하던 직원들의 관리자ID가 NULL로 변경
```

#### RESTRICT / NO ACTION
```sql
-- 자식이 있으면 부모 삭제 불가
FOREIGN KEY (부서ID) REFERENCES 부서(부서ID) ON DELETE RESTRICT;

예시:
부서에 직원이 있으면 부서 삭제 불가
```

### ON UPDATE 옵션

#### CASCADE
```sql
-- 부모 키 변경 시 자식 키도 함께 변경
FOREIGN KEY (고객ID) REFERENCES 고객(고객ID) ON UPDATE CASCADE;
```

#### SET NULL
```sql
-- 부모 키 변경 시 자식의 외래키를 NULL로 설정
FOREIGN KEY (관리자ID) REFERENCES 직원(직원ID) ON UPDATE SET NULL;
```

## 🎨 외래키 명명 규칙

### 규칙 1: 일관된 패턴
```sql
-- FK_[자식테이블]_[부모테이블]_[컬럼명]
ALTER TABLE 주문 
ADD CONSTRAINT FK_주문_고객_고객ID 
FOREIGN KEY (고객ID) REFERENCES 고객(고객ID);

-- FK_[자식테이블]_[컬럼명]  
ALTER TABLE 직원
ADD CONSTRAINT FK_직원_부서ID
FOREIGN KEY (부서ID) REFERENCES 부서(부서ID);
```

### 규칙 2: 의미있는 이름
```sql
-- 역할이 명확한 경우 역할명 포함
ALTER TABLE 주문
ADD CONSTRAINT FK_주문_배송고객
FOREIGN KEY (배송고객ID) REFERENCES 고객(고객ID);

ALTER TABLE 주문  
ADD CONSTRAINT FK_주문_주문고객
FOREIGN KEY (주문고객ID) REFERENCES 고객(고객ID);
```

## 🔧 외래키와 성능

### 성능에 미치는 영향

#### 긍정적 영향
```sql
-- 조인 최적화
SELECT c.고객명, o.주문일자, o.총금액
FROM 고객 c
JOIN 주문 o ON c.고객ID = o.고객ID  -- 외래키 관계로 최적화된 조인
WHERE c.고객등급 = 'VIP';
```

#### 부정적 영향
```sql
-- DML 작업 시 참조 무결성 검사로 인한 성능 저하
INSERT INTO 주문 VALUES (...);  -- 고객ID 존재 여부 검사
UPDATE 주문 SET 고객ID = ...;   -- 새로운 고객ID 존재 여부 검사  
DELETE FROM 고객 WHERE ...;    -- 참조하는 주문 존재 여부 검사
```

### 성능 최적화 방안

#### 1. 외래키 컬럼 인덱스 생성
```sql
-- 외래키 컬럼에 인덱스 생성으로 조인 성능 향상
CREATE INDEX IDX_주문_고객ID ON 주문(고객ID);
CREATE INDEX IDX_주문상세_주문번호 ON 주문상세(주문번호);
CREATE INDEX IDX_직원_부서ID ON 직원(부서ID);
```

#### 2. 적절한 CASCADE 옵션 선택
```sql
-- 대용량 데이터의 경우 CASCADE 대신 애플리케이션에서 처리
-- 성능이 중요한 경우 제약조건 비활성화 후 배치 처리
ALTER TABLE 주문 DISABLE CONSTRAINT FK_주문_고객ID;
-- 대량 데이터 처리
ALTER TABLE 주문 ENABLE CONSTRAINT FK_주문_고객ID;
```

## 💡 외래키 설계 실무 팁

### 팁 1: 선택적 관계 vs 필수 관계
```sql
-- 필수 관계 (NOT NULL 외래키)
CREATE TABLE 주문 (
    주문번호 VARCHAR(15) PRIMARY KEY,
    고객ID NUMBER NOT NULL,          -- 주문은 반드시 고객이 있어야 함
    FOREIGN KEY (고객ID) REFERENCES 고객(고객ID)
);

-- 선택적 관계 (NULL 허용 외래키)
CREATE TABLE 직원 (
    직원ID NUMBER PRIMARY KEY,
    직원명 VARCHAR(50),
    관리자ID NUMBER,                  -- 관리자가 없을 수도 있음 (CEO 등)
    FOREIGN KEY (관리자ID) REFERENCES 직원(직원ID)
);
```

### 팁 2: 순환 참조 방지
```sql
-- 잘못된 설계 (순환 참조 가능성)
CREATE TABLE A (
    A_ID NUMBER PRIMARY KEY,
    B_ID NUMBER,
    FOREIGN KEY (B_ID) REFERENCES B(B_ID)
);

CREATE TABLE B (
    B_ID NUMBER PRIMARY KEY,  
    A_ID NUMBER,
    FOREIGN KEY (A_ID) REFERENCES A(A_ID)  -- 순환 참조!
);

-- 올바른 설계 (중간 테이블 사용)
CREATE TABLE A_B_관계 (
    A_ID NUMBER,
    B_ID NUMBER,
    관계유형 VARCHAR(20),
    PRIMARY KEY (A_ID, B_ID),
    FOREIGN KEY (A_ID) REFERENCES A(A_ID),
    FOREIGN KEY (B_ID) REFERENCES B(B_ID)
);
```

### 팁 3: 이력 테이블의 외래키
```sql
-- 이력 테이블에서는 외래키 제약조건 주의
CREATE TABLE 고객이력 (
    이력ID NUMBER PRIMARY KEY,
    고객ID NUMBER,                    -- 원본 고객이 삭제되어도 이력은 유지
    고객명 VARCHAR(50),
    변경일시 TIMESTAMP,
    변경사유 VARCHAR(200)
    -- FOREIGN KEY 제약조건 없음 (이력 보존을 위해)
);
```

## 🚨 외래키 관련 주의사항

### 주의사항 1: 성능 vs 무결성
```
고려사항:
- 참조 무결성 vs 성능 트레이드오프
- 대용량 시스템에서는 애플리케이션 레벨 체크 고려
- 배치 작업 시 임시 비활성화 고려
```

### 주의사항 2: 데이터 마이그레이션
```sql
-- 마이그레이션 시 외래키 제약조건 순서 고려
-- 1. 부모 테이블 데이터 먼저 입력
INSERT INTO 고객 VALUES (...);
-- 2. 자식 테이블 데이터 입력  
INSERT INTO 주문 VALUES (...);

-- 또는 제약조건 임시 비활성화
ALTER TABLE 주문 DISABLE CONSTRAINT FK_주문_고객ID;
-- 데이터 입력
ALTER TABLE 주문 ENABLE CONSTRAINT FK_주문_고객ID;
```

### 주의사항 3: 삭제 순서
```sql
-- 잘못된 삭제 순서
DELETE FROM 고객 WHERE 고객ID = 1;  -- 오류: 참조하는 주문이 존재
DELETE FROM 주문 WHERE 고객ID = 1;  -- 먼저 자식 삭제

-- 올바른 삭제 순서  
DELETE FROM 주문 WHERE 고객ID = 1;  -- 1. 자식 먼저 삭제
DELETE FROM 고객 WHERE 고객ID = 1;  -- 2. 부모 나중에 삭제
```

## 🎯 체크리스트: 좋은 외래키 설계인가?

### 무결성 측면
- [ ] 참조 무결성이 보장되는가?
- [ ] 적절한 CASCADE 옵션을 선택했는가?
- [ ] 순환 참조가 없는가?
- [ ] 필수/선택 관계가 명확한가?

### 성능 측면
- [ ] 외래키 컬럼에 인덱스가 있는가?
- [ ] 조인 성능이 최적화되는가?
- [ ] DML 성능 영향을 고려했는가?
- [ ] 대용량 처리 방안이 있는가?

### 관리 측면
- [ ] 명명 규칙을 준수했는가?
- [ ] 제약조건명이 의미있는가?
- [ ] 문서화가 충분한가?
- [ ] 변경 영향도를 고려했는가?

---

**다음**: [3-5. 모델링 툴로 '쉽게' 모델링하는 방법](./05-modeling-tools.md)