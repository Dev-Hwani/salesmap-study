# MySQL 정리

MySQL은  
가장 널리 사용되는 오픈소스 관계형 데이터베이스 관리 시스템(RDBMS) 중 하나로  
웹 서비스·SaaS·백엔드 시스템에서 사실상 표준처럼 사용됩니다.

---

## 1. MySQL이란 무엇인가?

- 관계형 데이터베이스(RDBMS)
- SQL(Standard Query Language)을 사용
- Oracle이 관리하는 오픈소스 DB
- 빠른 읽기 성능과 안정성으로 웹 서비스에 최적화

👉 “데이터를 테이블 간 관계로 구조화해 관리하는 DB”

---

## 2. 왜 MySQL을 사용하는가?

### MySQL이 선택되는 이유

- 오픈소스 & 무료
- 풍부한 레퍼런스와 커뮤니티
- 웹 서비스에 최적화된 성능
- 대부분의 백엔드 프레임워크와 궁합이 좋음
- 클라우드, Docker, CI/CD 환경에서 표준처럼 사용

---

## 3. 관계형 데이터베이스(RDBMS) 개념

### 3-1. 테이블 구조

| 컬럼 | 타입 | 설명 |
|----|----|----|
| id | INT | 기본 키 |
| name | VARCHAR | 이름 |
| created_at | DATETIME | 생성일 |

- 행(Row) = 데이터 한 건
- 열(Column) = 속성
- 스키마(Schema) = 테이블 구조 정의

---

### 3-2. 관계(Relationship)

- 1:1
- 1:N
- N:M

예시:
- 회사 1 : 직원 N
- 사용자 N : 권한 M (중간 테이블 필요)

---

## 4. MySQL의 핵심 개념

### 4-1. 데이터베이스(Database)

- 테이블의 논리적 묶음
- 서비스 단위로 분리하는 것이 일반적

```sql
CREATE DATABASE salesmap;
```

### 4.2 테이블(Table)

```sql
CREATE TABLE users (
  id INT PRIMARY KEY AUTO_INCREMENT,
  email VARCHAR(255) NOT NULL,
  role VARCHAR(20),
  created_at DATETIME
);
```

### 4-3. 기본 키(Primary Key)

- 테이블 내에서 행을 유일하게 식별
- NULL 불가
- 중복 불가

```sql
PRIMARY KEY (id)
```
### 4-4. 외래 키(Foreign Key)

- 다른 테이블의 PK 참조
- 데이터 무결성 보장

```sql
FOREIGN KEY (company_id) REFERENCES companies(id)
```

## 5. SQL 기본 문법

### 5-1. CRUD

INSERT

```sql
INSERT INTO users (email, role)
VALUES ('test@test.com', 'A');
```

SELECT

```sql
SELECT * FROM users WHERE role = 'A';
```

UPDATE

```sql
UPDATE users SET role = 'B' WHERE id = 1;
```

DELETE

```sql
DELETE FROM users WHERE id = 1;
```

## 6. JOIN (관계형 DB의 핵심)

INNER JOIN

```sql
6. JOIN (관계형 DB의 핵심)
INNER JOIN
```

LFET JOIN

```sql
SELECT u.email, c.name
FROM users u
LEFT JOIN companies c ON u.company_id = c.id;
```

👉 관계를 어떻게 가져올지 설계 의도가 중요

## 7. 인덱스(Index)

### 7-1. 인덱스란?

- 검색 성능을 높이기 위한 구조
- 책의 목차와 유사

```sql
CREATE INDEX idx_users_email ON users(email);
```

### 7-2. 인덱스 주의점

- 읽기는 빨라짐
- 쓰기 성능은 느려질 수 있음
- 무분별한 인덱스 ❌

## 8. 정규화(Normalization)

정규화 목적

- 중복 제거
- 데이터 무결성 유지
- 변경 시 영향 최소화

3차 정규형까지가 실무 기준

- 1NF: 원자값
- 2NF: 부분 종속 제거
- 3NF: 이행 종속 제거

## 9. 트랜잭션(Transaction)

### 9-1. 트랜잭션이란?

- 하나의 논리적 작업 단위

```sql
START TRANSACTION;
INSERT INTO orders ...
INSERT INTO order_items ...
COMMIT;
```

### 9-2. ACID

- Atomicity: 원자성
- Consistency: 일관성
- Isolation: 격리성
- Durability: 지속성

## 10. 스토리지 엔진 (InnoDB)

MySQL의 기본 스토리지 엔진은 InnoDB

InnoDB 특징

- 트랜잭션 지원
- 외래 키 지원
- Row-level Lock
- 장애 복구 안정적

👉 실무에서는 거의 InnoDB만 사용

## 11. 락(Lock)

- Row Lock
- Table Lock

InnoDB는 행 단위 락을 사용 → 동시성에 유리

## 12. MySQL에서 자주 발생하는 문제

- N+1 문제
- 인덱스 미사용
- 잘못된 JOIN
- 정규화 부족
- 트랜잭션 범위 과다

## 13. MySQL + ORM (Prisma)

ORM 사용 이유

- SQL 직접 작성 최소화
- 타입 안정성
- 마이그레이션 관리

주의점

- ORM이 SQL을 대신 설계해주지는 않음
- DB 구조 이해 필수

## 14. 마이그레이션(Migration)

왜 필요한가?

- 스키마 변경 이력 관리
- 팀 협업 필수 요소

```bash
pnpm prisma migrate dev
```

## 15. 실무에서 중요한 MySQL 관점

- DB는 “데이터 저장소”가 아니라 “비즈니스 규칙의 일부”
- 무결성은 DB에서도 보장해야 함
- API 이전에 DB 설계가 먼저

## 16. MySQL을 잘 쓴다는 것

- 정규화된 스키마 설계
- 관계를 명확히 정의
- 인덱스를 의도적으로 사용
- 트랜잭션을 정확히 묶음
- ORM을 쓰더라도 SQL을 이해

## 17. 이 프로젝트에서 MySQL을 사용한 이유

- CRM 도메인에 적합한 관계 모델
- 명확한 데이터 무결성 필요
- Prisma와의 궁합
- 과제 요구 스택 충족

## 18. 정리

MySQL은 단순한 데이터 저장소가 아니라
도메인 구조와 비즈니스 규칙을 가장 명확하게 표현하는 계층입니다.

MySQL을 제대로 쓴다는 것은:

- 데이터 구조를 이해하고
- 관계를 설계하며
- 변경에 강한 구조를 만든다는 의미입니다.