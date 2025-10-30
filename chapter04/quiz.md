# 📝 제4장 퀴즈: 데이터 웨어하우스 모델링

## 🎯 학습 확인 퀴즈

### 객관식 문제

#### 1. OLTP와 OLAP 시스템의 가장 큰 차이점은?
a) 사용하는 데이터베이스 종류  
b) 데이터 처리 목적과 방식  
c) 프로그래밍 언어  
d) 하드웨어 사양  

<details>
<summary>정답 확인</summary>
<b>정답: b) 데이터 처리 목적과 방식</b><br>
OLTP는 일상 업무 처리(트랜잭션)를 위한 시스템이고, OLAP는 분석과 의사결정 지원을 위한 시스템입니다.
</details>

#### 2. 데이터 웨어하우스의 특징이 아닌 것은?
a) 주제 지향적 (Subject-Oriented)  
b) 통합적 (Integrated)  
c) 휘발성 (Volatile)  
d) 시계열적 (Time-Variant)  

<details>
<summary>정답 확인</summary>
<b>정답: c) 휘발성 (Volatile)</b><br>
데이터 웨어하우스는 비휘발성(Non-Volatile) 특징을 가집니다. 한번 저장된 데이터는 변경되지 않고 이력으로 보관됩니다.
</details>

#### 3. 스타 스키마의 중심에 위치하는 것은?
a) 차원 테이블  
b) 팩트 테이블  
c) 브리지 테이블  
d) 룩업 테이블  

<details>
<summary>정답 확인</summary>
<b>정답: b) 팩트 테이블</b><br>
스타 스키마는 중앙의 팩트 테이블을 여러 차원 테이블이 둘러싸는 별 모양의 구조입니다.
</details>

#### 4. 다음 중 가산형(Additive) 측정값의 예시는?
a) 평균 단가  
b) 비율  
c) 매출금액  
d) 온도  

<details>
<summary>정답 확인</summary>
<b>정답: c) 매출금액</b><br>
매출금액은 모든 차원에서 합계가 의미있는 가산형 측정값입니다. 평균, 비율, 온도는 합계가 무의미한 비가산형입니다.
</details>

#### 5. SCD Type 2의 특징은?
a) 기존 데이터를 새로운 값으로 덮어쓰기  
b) 변경 시 새로운 레코드 추가하여 이력 유지  
c) 별도 컬럼에 이전 값 저장  
d) 데이터 변경을 허용하지 않음  

<details>
<summary>정답 확인</summary>
<b>정답: b) 변경 시 새로운 레코드 추가하여 이력 유지</b><br>
SCD Type 2는 데이터 변경 시 기존 레코드를 종료하고 새로운 레코드를 생성하여 완전한 이력을 유지합니다.
</details>

### 주관식 문제

#### 6. 차원 모델링과 정규화 모델링의 차이점을 3가지 이상 설명하세요.

<details>
<summary>답안 예시</summary>
<b>차원 모델링 vs 정규화 모델링 차이점:</b><br><br>

<b>1. 설계 철학</b><br>
- 차원 모델링: 사용자 편의성과 성능 최적화 중심<br>
- 정규화 모델링: 데이터 중복 제거와 무결성 중심<br><br>

<b>2. 구조적 특징</b><br>
- 차원 모델링: 비정규화된 스타/스노우플레이크 스키마<br>
- 정규화 모델링: 3정규형 이상의 정규화된 구조<br><br>

<b>3. 조인 복잡도</b><br>
- 차원 모델링: 단순한 조인 (2-5개 테이블)<br>
- 정규화 모델링: 복잡한 조인 (5-10개 이상 테이블)<br><br>

<b>4. 성능 특성</b><br>
- 차원 모델링: 조회 성능 최적화<br>
- 정규화 모델링: 입력/수정/삭제 성능 최적화<br><br>

<b>5. 사용 목적</b><br>
- 차원 모델링: 분석 및 리포팅 (OLAP)<br>
- 정규화 모델링: 업무 처리 (OLTP)
</details>

#### 7. 팩트 테이블과 차원 테이블의 특징을 비교하여 설명하세요.

<details>
<summary>답안 예시</summary>
<b>팩트 테이블 vs 차원 테이블 비교:</b><br><br>

<b>팩트 테이블 특징:</b><br>
- 수치형 데이터 중심 (매출금액, 수량 등)<br>
- 측정 가능한 비즈니스 이벤트 저장<br>
- 대용량 데이터 (수백만~수억 건)<br>
- 시간에 따라 지속적으로 증가<br>
- 외래키들과 측정값들로 구성<br>
- 집계 연산의 대상<br><br>

<b>차원 테이블 특징:</b><br>
- 텍스트형 데이터 중심 (이름, 분류 등)<br>
- 분석 관점과 맥락 정보 제공<br>
- 상대적으로 적은 데이터량<br>
- 천천히 변화하는 특성<br>
- 서술적 속성들로 구성<br>
- 필터링과 그룹핑의 기준<br><br>

<b>관계:</b><br>
- 팩트 테이블이 차원 테이블들을 외래키로 참조<br>
- 스타 스키마에서 팩트가 중심, 차원이 주변<br>
- 함께 사용되어 다차원 분석 지원
</details>

### 실습 문제

#### 8. 온라인 교육 플랫폼의 수강 분석을 위한 차원 모델을 설계하세요.

**요구사항:**
- 학생의 강의 수강 현황 분석
- 강의별 인기도 및 완주율 분석  
- 시간대별 학습 패턴 분석
- 강사별 강의 품질 분석

**과제:**
1. 팩트 테이블 설계 (20점)
2. 주요 차원 테이블 4개 설계 (20점)
3. 스타 스키마 구조도 작성 (10점)

<details>
<summary>답안 예시</summary>

<b>1. 팩트 테이블 설계 (20점)</b><br>
```sql
CREATE TABLE 수강팩트 (
    -- 차원 키들
    일자키 NUMBER NOT NULL,
    시간키 NUMBER NOT NULL,
    학생키 NUMBER NOT NULL,
    강의키 NUMBER NOT NULL,
    강사키 NUMBER NOT NULL,
    
    -- 측정값들
    학습시간 NUMBER(10,2),      -- 분 단위
    진도율 NUMBER(5,2),         -- 백분율
    완료여부 CHAR(1),           -- Y/N
    평가점수 NUMBER(3,1),       -- 1.0~5.0
    
    -- 외래키 제약조건
    FOREIGN KEY (일자키) REFERENCES 일자차원(일자키),
    FOREIGN KEY (시간키) REFERENCES 시간차원(시간키),
    FOREIGN KEY (학생키) REFERENCES 학생차원(학생키),
    FOREIGN KEY (강의키) REFERENCES 강의차원(강의키),
    FOREIGN KEY (강사키) REFERENCES 강사차원(강사키)
);
```

<b>2. 주요 차원 테이블 설계 (20점)</b><br>
```sql
-- 학생 차원
CREATE TABLE 학생차원 (
    학생키 NUMBER PRIMARY KEY,
    학생ID VARCHAR(20) UNIQUE NOT NULL,
    학생명 VARCHAR(50),
    연령대 VARCHAR(10),
    성별 CHAR(1),
    지역 VARCHAR(50),
    가입일자 DATE,
    학습레벨 VARCHAR(20)
);

-- 강의 차원  
CREATE TABLE 강의차원 (
    강의키 NUMBER PRIMARY KEY,
    강의ID VARCHAR(20) UNIQUE NOT NULL,
    강의명 VARCHAR(100),
    카테고리 VARCHAR(50),
    난이도 VARCHAR(20),
    총차시수 NUMBER,
    강의시간 NUMBER,
    가격 NUMBER(10,2)
);

-- 강사 차원
CREATE TABLE 강사차원 (
    강사키 NUMBER PRIMARY KEY,
    강사ID VARCHAR(20) UNIQUE NOT NULL,
    강사명 VARCHAR(50),
    전문분야 VARCHAR(50),
    경력년수 NUMBER,
    평균평점 NUMBER(3,2),
    강의수 NUMBER
);

-- 시간 차원
CREATE TABLE 시간차원 (
    시간키 NUMBER PRIMARY KEY,
    시간 NUMBER(2),             -- 0~23
    시간대구분 VARCHAR(20),     -- 새벽, 오전, 오후, 저녁
    업무시간여부 CHAR(1),       -- Y/N
    피크시간여부 CHAR(1)        -- Y/N
);
```

<b>3. 스타 스키마 구조도 (10점)</b><br>
```
        일자차원
           │
시간차원 ── 수강팩트 ── 학생차원
           │
        강의차원
           │
        강사차원

중심: 수강팩트 (학습시간, 진도율, 완료여부, 평가점수)
주변: 5개 차원 테이블 (일자, 시간, 학생, 강의, 강사)
```
</details>

#### 9. 다음 정규화된 운영 시스템을 차원 모델로 변환하세요.

**기존 정규화 구조:**
```sql
-- 고객 테이블
CREATE TABLE 고객 (
    고객ID VARCHAR(10) PRIMARY KEY,
    고객명 VARCHAR(50),
    지역코드 VARCHAR(10)
);

-- 지역 테이블  
CREATE TABLE 지역 (
    지역코드 VARCHAR(10) PRIMARY KEY,
    지역명 VARCHAR(50),
    상위지역코드 VARCHAR(10)
);

-- 주문 테이블
CREATE TABLE 주문 (
    주문ID VARCHAR(15) PRIMARY KEY,
    고객ID VARCHAR(10),
    주문일자 DATE,
    총금액 NUMBER(15,2)
);
```

**과제:** 스타 스키마로 변환 (팩트 1개, 차원 2개 이상)

<details>
<summary>답안 예시</summary>

<b>차원 모델 변환 결과:</b><br>

```sql
-- 1. 고객차원 (비정규화)
CREATE TABLE 고객차원 (
    고객키 NUMBER PRIMARY KEY,        -- 대리키
    고객ID VARCHAR(10) UNIQUE NOT NULL, -- 자연키
    고객명 VARCHAR(50),
    
    -- 지역 정보 비정규화
    지역코드 VARCHAR(10),
    지역명 VARCHAR(50),
    상위지역코드 VARCHAR(10),
    상위지역명 VARCHAR(50),
    
    -- 관리 정보
    등록일자 DATE,
    수정일자 DATE
);

-- 2. 일자차원
CREATE TABLE 일자차원 (
    일자키 NUMBER PRIMARY KEY,        -- 20240315
    일자 DATE UNIQUE NOT NULL,        -- 2024-03-15
    년도 NUMBER(4),
    분기 NUMBER(1),
    월 NUMBER(2),
    일 NUMBER(2),
    요일명 VARCHAR(10),
    주말여부 CHAR(1)
);

-- 3. 주문팩트 (중심)
CREATE TABLE 주문팩트 (
    주문팩트키 NUMBER PRIMARY KEY,
    일자키 NUMBER NOT NULL,
    고객키 NUMBER NOT NULL,
    
    -- 퇴화 차원
    주문ID VARCHAR(15),
    
    -- 측정값
    주문금액 NUMBER(15,2),
    주문건수 NUMBER DEFAULT 1,
    
    -- 외래키 제약조건
    FOREIGN KEY (일자키) REFERENCES 일자차원(일자키),
    FOREIGN KEY (고객키) REFERENCES 고객차원(고객키)
);
```

<b>변환 포인트:</b><br>
1. **비정규화**: 고객-지역 조인을 제거하고 고객차원에 지역 정보 포함<br>
2. **대리키 도입**: 성능 최적화를 위한 숫자형 대리키 사용<br>
3. **시간차원 추가**: 날짜 분석을 위한 전용 차원 테이블<br>
4. **퇴화차원**: 주문ID는 별도 테이블 없이 팩트에 직접 저장<br>
5. **측정값 추가**: 주문건수 등 분석에 유용한 측정값 추가
</details>

## 🎯 점수 계산

- 객관식 (1-5번): 각 10점 = 50점
- 주관식 (6-7번): 각 25점 = 50점  
- 실습 (8-9번): 각 50점 = 100점

**총점: 200점**

### 평가 기준
- 180점 이상: 우수 (A) 🏆
- 160점 이상: 양호 (B) 👍  
- 140점 이상: 보통 (C) 📚
- 140점 미만: 복습 필요 (D) 📖

## 🔄 복습이 필요한 경우

점수가 낮다면 다음 내용을 다시 학습해보세요:

- [4-1. 데이터 모델링과 DW 모델링의 차이](./01-difference-dw-modeling.md)
- [4-2. 데이터 웨어하우스 개념](./02-dw-concepts.md)  
- [4-3. DW 아키텍처와 모델](./03-dw-architecture-vs-model.md)
- [4-4. 차원 모델링의 철학](./04-dimensional-modeling-philosophy.md)
- [4-5. 스타 스키마와 OLAP 큐브](./05-star-schema-olap.md)
- [4-6. Fact 테이블](./06-fact-table.md)
- [4-7. Dimension 테이블](./07-dimension-table.md)

---

**완료**: 🎉 **데이터 모델링 완전 정복 과정을 모두 마쳤습니다!** 

**최종 점검**: [종합 평가 퀴즈](../final-quiz.md)로 전체 실력을 확인해보세요! 🚀