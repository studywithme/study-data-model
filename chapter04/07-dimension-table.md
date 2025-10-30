# 4-7. 차원 모델링 용어 탐방(3): Dimension, Dimension 테이블 🎭

## 📖 학습 목표
- Dimension(차원)의 개념과 역할 이해
- Dimension 테이블의 구조와 설계 방법 학습
- 다양한 차원 테이블 유형과 고급 기법 파악

## 🎯 Dimension(차원)이란?

### 정의
**Dimension(차원)**은 팩트를 분석하고 해석하는 관점이나 맥락을 제공하는 
기준으로, 비즈니스 사용자가 데이터를 이해하는 방식을 반영합니다.

### 카메라 렌즈와의 비유
```
카메라 렌즈              Dimension
─────────────────       ─────────────────
다양한 각도에서 촬영 →   다양한 관점에서 분석
줌인/줌아웃          →   드릴다운/롤업
필터 효과            →   슬라이싱/다이싱
초점 조절            →   세부사항 조절
렌즈 교체            →   분석 관점 변경
```

## 📐 Dimension의 특성

### 1. 서술적 특성 (Descriptive)
```
특징:
- 주로 텍스트 데이터
- 분류 및 그룹핑 기준
- 비즈니스 용어 사용
- 사용자 친화적 표현

예시:
고객차원: VIP고객, 일반고객, 신규고객
상품차원: 전자제품, 의류, 도서
시간차원: 2024년, 1분기, 3월, 월요일
지역차원: 서울, 경기, 부산
```

### 2. 계층적 특성 (Hierarchical)
```
자연스러운 계층 구조:

시간 계층:
년도 → 분기 → 월 → 일

지역 계층:
국가 → 시도 → 시군구 → 동

상품 계층:
대분류 → 중분류 → 소분류 → 상품

조직 계층:
본부 → 부서 → 팀 → 개인
```

### 3. 안정적 특성 (Stable)
```
변화 특성:
- 팩트에 비해 상대적으로 안정적
- 천천히 변화 (Slowly Changing)
- 이력 관리 필요
- 참조 데이터 성격

예시:
고객정보: 이름, 주소 등은 가끔 변경
상품정보: 카테고리, 브랜드는 거의 고정
조직정보: 부서 구조는 주기적 변경
```

## 🏗️ Dimension 테이블의 구조

### 기본 구조
```sql
CREATE TABLE 고객차원 (
    -- 대리키 (Primary Key)
    고객키 NUMBER PRIMARY KEY,
    
    -- 자연키 (Business Key)
    고객ID VARCHAR(10) UNIQUE NOT NULL,
    
    -- 서술적 속성들
    고객명 VARCHAR(50),
    성별 CHAR(1),
    생년월일 DATE,
    연령대 VARCHAR(10),
    
    -- 계층적 속성들
    고객등급 VARCHAR(20),
    고객등급코드 VARCHAR(5),
    
    -- 지역 정보
    시도 VARCHAR(20),
    시군구 VARCHAR(30),
    우편번호 VARCHAR(10),
    
    -- 관리 정보
    가입일자 DATE,
    최종수정일자 DATE,
    유효시작일자 DATE,
    유효종료일자 DATE,
    현재여부 CHAR(1) DEFAULT 'Y'
);
```

### 구성 요소

#### 1. 대리키 (Surrogate Key)
```
특징:
- 시스템에서 생성하는 인공키
- 팩트 테이블에서 참조
- 안정적이고 단순한 구조
- 성능 최적화

예시:
고객키: 1, 2, 3, 4, 5...
상품키: 101, 102, 103, 104...
일자키: 20240315, 20240316...
```

#### 2. 자연키 (Natural Key)
```
특징:
- 비즈니스에서 사용하는 실제 키
- 의미있는 식별자
- 외부 시스템과의 연계
- 사용자가 이해하기 쉬움

예시:
고객ID: CU001, CU002, CU003
상품코드: PRD001, PRD002, PRD003
날짜: 2024-03-15, 2024-03-16
```

#### 3. 서술적 속성 (Descriptive Attributes)
```
특징:
- 분석 및 리포팅에 사용
- 사용자 친화적 표현
- 비정규화된 형태
- 계층 정보 포함

예시:
고객명, 고객등급명, 연령대명
상품명, 카테고리명, 브랜드명
년도, 분기명, 월명, 요일명
```

## 📊 Dimension 테이블 유형

### 1. 일반 차원 테이블
```sql
-- 상품 차원 테이블
CREATE TABLE 상품차원 (
    상품키 NUMBER PRIMARY KEY,
    상품코드 VARCHAR(15) UNIQUE NOT NULL,
    상품명 VARCHAR(100),
    
    -- 계층 정보 (비정규화)
    대분류코드 VARCHAR(10),
    대분류명 VARCHAR(50),
    중분류코드 VARCHAR(10),
    중분류명 VARCHAR(50),
    소분류코드 VARCHAR(10),
    소분류명 VARCHAR(50),
    
    -- 기타 속성
    브랜드 VARCHAR(50),
    제조사 VARCHAR(50),
    출시일자 DATE,
    단종여부 CHAR(1),
    
    -- 관리 정보
    등록일자 DATE,
    수정일자 DATE
);
```

### 2. 시간 차원 테이블 (Date Dimension)
```sql
-- 시간 차원 테이블 (가장 중요한 차원)
CREATE TABLE 일자차원 (
    일자키 NUMBER PRIMARY KEY,        -- 20240315
    일자 DATE UNIQUE NOT NULL,        -- 2024-03-15
    
    -- 년도 정보
    년도 NUMBER(4),                   -- 2024
    년도명 VARCHAR(10),               -- '2024년'
    
    -- 분기 정보
    분기 NUMBER(1),                   -- 1
    분기명 VARCHAR(10),               -- '1분기'
    년분기 VARCHAR(10),               -- '2024-Q1'
    
    -- 월 정보
    월 NUMBER(2),                     -- 3
    월명 VARCHAR(10),                 -- '3월'
    년월 NUMBER(6),                   -- 202403
    년월명 VARCHAR(15),               -- '2024년 3월'
    
    -- 일 정보
    일 NUMBER(2),                     -- 15
    요일 NUMBER(1),                   -- 5 (금요일)
    요일명 VARCHAR(10),               -- '금요일'
    요일명영문 VARCHAR(10),           -- 'Friday'
    
    -- 특별일 정보
    주말여부 CHAR(1),                 -- 'Y'/'N'
    공휴일여부 CHAR(1),               -- 'Y'/'N'
    공휴일명 VARCHAR(50),             -- '어린이날'
    
    -- 주 정보
    주차 NUMBER(2),                   -- 11 (년중 몇째주)
    월주차 NUMBER(1),                 -- 3 (월중 몇째주)
    
    -- 기타 정보
    분기시작일 DATE,
    분기종료일 DATE,
    월시작일 DATE,
    월종료일 DATE,
    전년동일일자 DATE,
    전월동일일자 DATE
);

-- 시간 차원 데이터 생성 예시
INSERT INTO 일자차원 (일자키, 일자, 년도, 분기, 월, 일, 요일명, 주말여부)
SELECT 
    TO_NUMBER(TO_CHAR(날짜, 'YYYYMMDD')) as 일자키,
    날짜,
    EXTRACT(YEAR FROM 날짜) as 년도,
    CEIL(EXTRACT(MONTH FROM 날짜)/3) as 분기,
    EXTRACT(MONTH FROM 날짜) as 월,
    EXTRACT(DAY FROM 날짜) as 일,
    TO_CHAR(날짜, 'DAY') as 요일명,
    CASE WHEN TO_CHAR(날짜, 'D') IN ('1','7') THEN 'Y' ELSE 'N' END as 주말여부
FROM (
    SELECT DATE '2024-01-01' + LEVEL - 1 as 날짜
    FROM DUAL
    CONNECT BY LEVEL <= 366  -- 2024년 전체
);
```

### 3. 퇴화 차원 (Degenerate Dimension)
```
특징:
- 차원 테이블 없이 팩트 테이블에 직접 저장
- 주로 트랜잭션 식별자
- 별도 테이블 생성이 비효율적인 경우

예시:
CREATE TABLE 주문팩트 (
    일자키 NUMBER,
    고객키 NUMBER,
    상품키 NUMBER,
    주문번호 VARCHAR(15),  -- 퇴화 차원 (별도 테이블 없음)
    송장번호 VARCHAR(15),  -- 퇴화 차원
    매출금액 NUMBER(15,2),
    수량 NUMBER
);
```

### 4. 역할 수행 차원 (Role-Playing Dimension)
```
특징:
- 하나의 차원이 여러 역할 수행
- 동일한 구조, 다른 의미
- 별칭(Alias) 사용

예시:
-- 하나의 일자차원이 여러 역할 수행
CREATE TABLE 주문팩트 (
    주문일자키 NUMBER,     -- 일자차원 참조 (주문 역할)
    배송일자키 NUMBER,     -- 일자차원 참조 (배송 역할)
    완료일자키 NUMBER,     -- 일자차원 참조 (완료 역할)
    고객키 NUMBER,
    매출금액 NUMBER(15,2)
);

-- 조회 시 별칭 사용
SELECT 
    od.년월명 as 주문년월,
    dd.년월명 as 배송년월,
    SUM(f.매출금액) as 총매출
FROM 주문팩트 f
JOIN 일자차원 od ON f.주문일자키 = od.일자키  -- 주문일자 역할
JOIN 일자차원 dd ON f.배송일자키 = dd.일자키  -- 배송일자 역할
GROUP BY od.년월명, dd.년월명;
```

## 🔄 천천히 변하는 차원 (SCD: Slowly Changing Dimension)

### Type 0: 변경 없음
```
특징:
- 한번 설정되면 변경하지 않음
- 고정된 속성에 적용

예시:
생년월일, 성별 등은 변경하지 않음
```

### Type 1: 덮어쓰기
```
특징:
- 새로운 값으로 덮어쓰기
- 이력 정보 유지하지 않음
- 가장 단순한 방법

예시:
-- 고객 주소 변경
UPDATE 고객차원 
SET 주소 = '새로운 주소',
    수정일자 = SYSDATE
WHERE 고객키 = 12345;

장점: 단순함, 저장공간 절약
단점: 이력 정보 손실
```

### Type 2: 새로운 레코드 추가
```
특징:
- 변경 시 새로운 레코드 생성
- 완전한 이력 정보 유지
- 가장 일반적인 방법

예시:
-- 기존 레코드 종료
UPDATE 고객차원 
SET 유효종료일자 = SYSDATE - 1,
    현재여부 = 'N'
WHERE 고객키 = 12345 AND 현재여부 = 'Y';

-- 새로운 레코드 생성
INSERT INTO 고객차원 (
    고객키, 고객ID, 고객명, 고객등급,
    유효시작일자, 유효종료일자, 현재여부
) VALUES (
    고객키_SEQ.NEXTVAL, 'CU001', '홍길동', 'VIP',
    SYSDATE, DATE '9999-12-31', 'Y'
);

장점: 완전한 이력 유지
단점: 저장공간 증가, 복잡성 증가
```

### Type 3: 별도 컬럼 추가
```
특징:
- 이전 값을 별도 컬럼에 저장
- 제한적 이력 정보 유지
- 특정 속성에만 적용

예시:
ALTER TABLE 고객차원 ADD (
    이전고객등급 VARCHAR(20),
    등급변경일자 DATE
);

-- 등급 변경 시
UPDATE 고객차원 
SET 이전고객등급 = 고객등급,
    고객등급 = '새로운등급',
    등급변경일자 = SYSDATE
WHERE 고객키 = 12345;

장점: 단순함, 최근 이력 유지
단점: 제한적 이력, 컬럼 증가
```

## 💡 고급 차원 기법

### 1. 미니 차원 (Mini Dimension)
```
문제: 자주 변하는 속성으로 인한 SCD Type 2 급증

해결책: 자주 변하는 속성을 별도 차원으로 분리

예시:
-- 기본 고객차원 (안정적 속성)
CREATE TABLE 고객차원 (
    고객키 NUMBER PRIMARY KEY,
    고객ID VARCHAR(10),
    고객명 VARCHAR(50),
    성별 CHAR(1),
    생년월일 DATE
);

-- 고객프로필 미니차원 (변동적 속성)
CREATE TABLE 고객프로필차원 (
    프로필키 NUMBER PRIMARY KEY,
    고객등급 VARCHAR(20),
    연소득구간 VARCHAR(20),
    거주지역 VARCHAR(50),
    유효시작일자 DATE,
    유효종료일자 DATE
);

-- 팩트 테이블에서 두 차원 모두 참조
CREATE TABLE 매출팩트 (
    일자키 NUMBER,
    고객키 NUMBER,        -- 고객차원 참조
    프로필키 NUMBER,      -- 고객프로필차원 참조
    상품키 NUMBER,
    매출금액 NUMBER(15,2)
);
```

### 2. 정크 차원 (Junk Dimension)
```
문제: 많은 플래그나 코드성 속성들

해결책: 관련 없는 저카디널리티 속성들을 하나의 차원으로 통합

예시:
CREATE TABLE 주문속성차원 (
    주문속성키 NUMBER PRIMARY KEY,
    결제방법 VARCHAR(20),      -- 카드, 현금, 계좌이체
    배송방법 VARCHAR(20),      -- 일반, 당일, 새벽
    포장방법 VARCHAR(20),      -- 일반, 선물, 친환경
    할인적용여부 CHAR(1),      -- Y, N
    멤버십적용여부 CHAR(1),    -- Y, N
    쿠폰사용여부 CHAR(1)       -- Y, N
);

-- 모든 조합을 사전에 생성 (데카르트 곱)
INSERT INTO 주문속성차원 
SELECT 
    ROW_NUMBER() OVER (ORDER BY 결제방법, 배송방법, 포장방법) as 주문속성키,
    결제방법, 배송방법, 포장방법, 할인적용여부, 멤버십적용여부, 쿠폰사용여부
FROM (
    SELECT '카드' as 결제방법 FROM DUAL UNION ALL
    SELECT '현금' FROM DUAL UNION ALL
    SELECT '계좌이체' FROM DUAL
) a
CROSS JOIN (
    SELECT '일반' as 배송방법 FROM DUAL UNION ALL
    SELECT '당일' FROM DUAL UNION ALL
    SELECT '새벽' FROM DUAL
) b
-- ... 다른 속성들도 동일하게
```

### 3. 브리지 테이블 (Bridge Table)
```
문제: 다대다 관계 처리

해결책: 중간 테이블로 다대다 관계 해결

예시:
-- 고객-계좌 다대다 관계
CREATE TABLE 고객계좌브리지 (
    고객키 NUMBER,
    계좌키 NUMBER,
    소유비율 NUMBER(5,2),    -- 공동 소유 시 비율
    주계좌여부 CHAR(1),      -- 주 계좌 여부
    개설일자 DATE,
    PRIMARY KEY (고객키, 계좌키)
);

-- 분석 시 브리지 테이블 활용
SELECT 
    c.고객등급,
    a.계좌유형,
    SUM(f.거래금액 * b.소유비율) as 가중거래금액
FROM 거래팩트 f
JOIN 고객계좌브리지 b ON f.계좌키 = b.계좌키
JOIN 고객차원 c ON b.고객키 = c.고객키
JOIN 계좌차원 a ON f.계좌키 = a.계좌키
GROUP BY c.고객등급, a.계좌유형;
```

## 🎯 실무 적용 가이드

### Dimension 테이블 설계 체크리스트
```
비즈니스 요구사항:
□ 분석 관점이 명확한가?
□ 계층 구조가 자연스러운가?
□ 사용자 친화적인 속성명인가?
□ 비즈니스 규칙이 반영되었는가?

기술적 요구사항:
□ 적절한 SCD 유형을 선택했는가?
□ 대리키와 자연키가 구분되는가?
□ 성능을 고려한 인덱스가 있는가?
□ 데이터 품질 검증 로직이 있는가?

확장성 고려사항:
□ 새로운 속성 추가가 용이한가?
□ 계층 구조 변경에 대응 가능한가?
□ 미래 요구사항을 고려했는가?
□ 유지보수가 용이한가?
```

### 성능 최적화 팁
```
1. 적절한 인덱스 설계
   - 자연키에 유니크 인덱스
   - 자주 조회되는 속성에 인덱스
   - 복합 인덱스 고려

2. SCD 성능 최적화
   - 현재여부 플래그 활용
   - 파티셔닝 적용 고려
   - 이력 테이블 분리 검토

3. 메모리 최적화
   - 차원 테이블 캐싱
   - 룩업 테이블 활용
   - 압축 기법 적용
```

## 🎯 체크리스트: Dimension 테이블 설계

### 설계 품질
- [ ] 비즈니스 관점을 정확히 반영하는가?
- [ ] 직관적이고 이해하기 쉬운 구조인가?
- [ ] 적절한 계층 구조를 가지는가?
- [ ] 사용자 친화적인 속성명을 사용하는가?

### 기술적 완성도
- [ ] 적절한 SCD 유형을 적용했는가?
- [ ] 대리키와 자연키를 올바르게 설계했는가?
- [ ] 성능을 고려한 인덱스가 있는가?
- [ ] 데이터 품질 관리 방안이 있는가?

### 확장성 및 유지보수
- [ ] 미래 요구사항 변화에 대응 가능한가?
- [ ] 새로운 속성 추가가 용이한가?
- [ ] 계층 구조 변경에 유연한가?
- [ ] 운영 및 관리가 용이한가?

---

**다음**: [4-8. 차원 모델링 용어 탐방(종합): 스타 스키마/Fact/Dimension](./08-comprehensive-terms.md)