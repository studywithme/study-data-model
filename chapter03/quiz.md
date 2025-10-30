# 📝 제3장 퀴즈: 데이터 구조 설계

## 🎯 학습 확인 퀴즈

### 객관식 문제

#### 1. 데이터 구조 설계의 가장 중요한 원칙은?
a) 최신 기술 활용  
b) 복잡한 구조 설계  
c) 비즈니스 요구사항 반영  
d) 저장 공간 최소화  

<details>
<summary>정답 확인</summary>
<b>정답: c) 비즈니스 요구사항 반영</b><br>
데이터 구조 설계의 핵심은 비즈니스 요구사항을 정확히 반영하여 업무를 효과적으로 지원하는 것입니다.
</details>

#### 2. 기본키(Primary Key)의 특성으로 올바르지 않은 것은?
a) 유일성 (Uniqueness)  
b) 비어있지 않음 (Not Null)  
c) 변경 가능성 (Mutability)  
d) 최소성 (Minimality)  

<details>
<summary>정답 확인</summary>
<b>정답: c) 변경 가능성 (Mutability)</b><br>
기본키는 가급적 변경되지 않아야 하는 불변성(Immutability)을 가져야 합니다.
</details>

#### 3. 대리키(Surrogate Key)의 장점이 아닌 것은?
a) 값이 변경되지 않음  
b) 단순한 구조  
c) 비즈니스 의미가 명확함  
d) 성능 최적화 용이  

<details>
<summary>정답 확인</summary>
<b>정답: c) 비즈니스 의미가 명확함</b><br>
대리키는 시스템에서 인위적으로 생성하는 키로 비즈니스 의미가 없는 것이 특징입니다.
</details>

#### 4. 외래키 제약조건의 ON DELETE CASCADE 옵션의 의미는?
a) 부모 레코드 삭제 시 자식 레코드도 함께 삭제  
b) 부모 레코드 삭제 시 자식의 외래키를 NULL로 설정  
c) 자식 레코드가 있으면 부모 레코드 삭제 불가  
d) 외래키 제약조건을 비활성화  

<details>
<summary>정답 확인</summary>
<b>정답: a) 부모 레코드 삭제 시 자식 레코드도 함께 삭제</b><br>
CASCADE 옵션은 부모 테이블의 레코드가 삭제될 때 이를 참조하는 자식 테이블의 레코드도 함께 삭제됩니다.
</details>

#### 5. 데이터 구성도에 포함되어야 할 요소가 아닌 것은?
a) 핵심 엔티티  
b) 주요 관계  
c) 모든 속성의 상세 정보  
d) 업무 영역별 그룹핑  

<details>
<summary>정답 확인</summary>
<b>정답: c) 모든 속성의 상세 정보</b><br>
데이터 구성도는 고수준 관점에서 전체 구조를 파악하는 것이 목적이므로 상세 속성 정보는 포함하지 않습니다.
</details>

### 주관식 문제

#### 6. 다음 상황에서 적절한 기본키 설계 전략을 제시하고 이유를 설명하세요.

**상황**: 온라인 쇼핑몰의 고객 테이블을 설계합니다. 고객은 이메일로 가입하며, 이메일 변경이 가능합니다. 향후 소셜 로그인도 지원할 예정입니다.

<details>
<summary>답안 예시</summary>
<b>추천 전략: 대리키 사용</b><br><br>

<b>설계안:</b><br>
```sql
CREATE TABLE 고객 (
    고객ID NUMBER PRIMARY KEY,           -- 대리키 (시퀀스 사용)
    이메일 VARCHAR(100) UNIQUE NOT NULL, -- 자연키 (유니크 제약조건)
    고객명 VARCHAR(50),
    가입일자 DATE
);
```

<b>선택 이유:</b><br>
1. **안정성**: 이메일 변경 시에도 고객ID는 변경되지 않음<br>
2. **확장성**: 소셜 로그인 시 이메일이 없을 수도 있음<br>
3. **성능**: 숫자형 기본키로 조인 성능 최적화<br>
4. **단순성**: 단일 컬럼으로 구성되어 관리 용이<br>
5. **독립성**: 비즈니스 규칙 변경에 영향받지 않음
</details>

#### 7. 외래키 설계 시 고려해야 할 사항들을 3가지 이상 설명하세요.

<details>
<summary>답안 예시</summary>
<b>외래키 설계 고려사항:</b><br><br>

<b>1. 참조 무결성 보장</b><br>
- 존재하지 않는 부모 레코드 참조 방지<br>
- 데이터 일관성 유지<br><br>

<b>2. CASCADE 옵션 선택</b><br>
- ON DELETE CASCADE: 부모 삭제 시 자식도 삭제<br>
- ON DELETE SET NULL: 부모 삭제 시 자식 FK를 NULL로<br>
- ON DELETE RESTRICT: 자식이 있으면 부모 삭제 불가<br><br>

<b>3. 성능 고려</b><br>
- 외래키 컬럼에 인덱스 생성<br>
- 조인 성능 최적화<br>
- DML 작업 시 성능 영향 고려<br><br>

<b>4. 필수/선택 관계 정의</b><br>
- NOT NULL: 필수 관계 (주문-고객)<br>
- NULL 허용: 선택 관계 (직원-관리자)<br><br>

<b>5. 순환 참조 방지</b><br>
- 테이블 간 순환 참조 구조 피하기<br>
- 필요시 중간 테이블 활용
</details>

### 실습 문제

#### 8. 온라인 강의 플랫폼의 데이터 구조를 설계하세요.

**요구사항:**
- 강사가 강의를 개설하고 학생이 수강신청
- 강의는 여러 개의 차시(에피소드)로 구성
- 학생의 학습 진도 추적 필요
- 강의 평가 및 질의응답 기능
- 결제 및 수료증 발급

**과제:**
1. 핵심 엔티티 7개 이상 식별 (15점)
2. 각 엔티티의 기본키 설계 (15점)
3. 엔티티 간 관계 정의 (20점)

<details>
<summary>답안 예시</summary>

<b>1. 핵심 엔티티 식별 (15점)</b><br>
```
1. 회원 (강사, 학생 통합 관리)
2. 강의 
3. 강의차시 (강의의 세부 내용)
4. 수강신청
5. 학습진도
6. 강의평가
7. 질의응답
8. 결제
9. 수료증
```

<b>2. 기본키 설계 (15점)</b><br>
```sql
회원: 회원ID NUMBER (시퀀스)
강의: 강의ID NUMBER (시퀀스)  
강의차시: 차시ID NUMBER (시퀀스)
수강신청: 수강신청ID NUMBER (시퀀스)
학습진도: 진도ID NUMBER (시퀀스)
강의평가: 평가ID NUMBER (시퀀스)
질의응답: 질의응답ID NUMBER (시퀀스)
결제: 결제ID NUMBER (시퀀스)
수료증: 수료증ID NUMBER (시퀀스)
```

<b>3. 엔티티 간 관계 정의 (20점)</b><br>
```
회원 1:N 강의 (강사가 여러 강의 개설)
회원 1:N 수강신청 (학생이 여러 강의 수강)
강의 1:N 강의차시 (강의가 여러 차시로 구성)
강의 1:N 수강신청 (강의를 여러 학생이 수강)
수강신청 1:N 학습진도 (수강신청별 차시별 진도)
강의차시 1:N 학습진도 (차시별 여러 학생의 진도)
수강신청 1:N 강의평가 (수강 후 평가)
강의 1:N 질의응답 (강의별 질의응답)
회원 1:N 질의응답 (회원별 질의응답)
수강신청 1:N 결제 (수강신청별 결제)
수강신청 1:1 수료증 (수강완료 시 수료증 발급)
```
</details>

#### 9. 기존 시스템의 문제점을 분석하고 개선안을 제시하세요.

**현재 시스템:**
```sql
CREATE TABLE USERS (
    USER_EMAIL VARCHAR(100) PRIMARY KEY,
    USER_NAME VARCHAR(50),
    USER_PHONE VARCHAR(20)
);

CREATE TABLE ORDERS (
    ORDER_ID NUMBER PRIMARY KEY,
    USER_EMAIL VARCHAR(100),
    ORDER_DATE DATE,
    TOTAL_AMOUNT NUMBER
);
```

**과제:**
1. 현재 설계의 문제점 분석 (15점)
2. 개선된 설계안 제시 (15점)

<details>
<summary>답안 예시</summary>

<b>1. 문제점 분석 (15점)</b><br>

<b>기본키 문제:</b><br>
- 이메일을 기본키로 사용하여 변경 시 문제 발생<br>
- 외래키 참조 시 성능 저하 (문자열 조인)<br><br>

<b>참조 무결성 문제:</b><br>
- ORDERS 테이블에 외래키 제약조건 없음<br>
- 존재하지 않는 사용자의 주문 생성 가능<br><br>

<b>확장성 문제:</b><br>
- 소셜 로그인 시 이메일이 없을 수 있음<br>
- 이메일 변경 시 모든 관련 데이터 수정 필요<br><br>

<b>2. 개선된 설계안 (15점)</b><br>

```sql
-- 개선된 사용자 테이블
CREATE TABLE 사용자 (
    사용자ID NUMBER PRIMARY KEY,        -- 대리키 사용
    이메일 VARCHAR(100) UNIQUE,         -- 자연키는 유니크 제약조건
    사용자명 VARCHAR(50) NOT NULL,
    전화번호 VARCHAR(20),
    가입일자 DATE DEFAULT SYSDATE,
    상태 VARCHAR(10) DEFAULT 'ACTIVE'
);

-- 개선된 주문 테이블  
CREATE TABLE 주문 (
    주문ID NUMBER PRIMARY KEY,
    사용자ID NUMBER NOT NULL,           -- 외래키, NOT NULL
    주문일자 DATE DEFAULT SYSDATE,
    총금액 NUMBER(12,2) NOT NULL,
    주문상태 VARCHAR(20) DEFAULT '주문완료',
    FOREIGN KEY (사용자ID) REFERENCES 사용자(사용자ID)
);

-- 인덱스 생성
CREATE INDEX IDX_주문_사용자ID ON 주문(사용자ID);
CREATE INDEX IDX_주문_주문일자 ON 주문(주문일자);
```

<b>개선 효과:</b><br>
- 안정적인 기본키로 데이터 무결성 보장<br>
- 외래키 제약조건으로 참조 무결성 확보<br>
- 성능 최적화된 조인 구조<br>
- 향후 확장성 고려한 설계
</details>

## 🎯 점수 계산

- 객관식 (1-5번): 각 10점 = 50점
- 주관식 (6-7번): 각 20점 = 40점  
- 실습 (8-9번): 각 55점 = 110점

**총점: 200점**

### 평가 기준
- 180점 이상: 우수 (A) 🏆
- 160점 이상: 양호 (B) 👍  
- 140점 이상: 보통 (C) 📚
- 140점 미만: 복습 필요 (D) 📖

## 🔄 복습이 필요한 경우

점수가 낮다면 다음 내용을 다시 학습해보세요:

- [3-1. 데이터 모델링의 진수, 구조 설계](./01-structure-design-essence.md)
- [3-2. 데이터 구성도](./02-data-architecture-diagram.md)  
- [3-3. PK, 데이터 구조를 지배하는 마스터키](./03-primary-key.md)
- [3-4. FK, 부모 없이는 살 수 없지만](./04-foreign-key.md)
- [3-5. 모델링 툴로 '쉽게' 모델링하는 방법](./05-modeling-tools.md)

---

**다음 장**: [제4장 데이터 웨어하우스 모델링](../chapter04/01-difference-dw-modeling.md) 🚀