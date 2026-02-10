# Prisma 정리

## 1. Prisma란?

**Prisma는 Node.js / TypeScript 환경에서 사용하는 차세대 ORM(Object-Relational Mapping)** 이다.  
데이터베이스(MySQL, PostgreSQL 등)와 애플리케이션 사이에서 **타입 안전한 데이터 접근 계층**을 제공한다.

> SQL을 직접 작성하지 않고도  
> 타입 안전한 코드로 데이터베이스를 다룰 수 있게 해주는 도구

---

## 2. Prisma를 사용하는 이유

### 기존 ORM의 한계
- 런타임 시점에 쿼리 오류 발생
- 타입 불일치로 인한 버그
- 복잡한 쿼리 가독성 저하
- 스키마와 코드 간 불일치

### Prisma의 강점
| 항목 | 설명 |
|---|---|
| 타입 안정성 | Prisma Client가 타입 자동 생성 |
| 생산성 | 자동 완성 + 직관적인 API |
| 유지보수 | 스키마 중심 개발 |
| 안정성 | 컴파일 타임 오류 탐지 |

---

## 3. Prisma 핵심 구성 요소

Prisma
├─ Prisma Schema
├─ Prisma Client
├─ Prisma Migrate
└─ Prisma Studio


---

## 4. Prisma Schema

### 4.1 schema.prisma 기본 구조

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}
```

- generator: Prisma Client 생성 설정
- datasource: 데이터베이스 연결 정보

### 4.2 Model 정의

```prisma
model User {
  id        Int      @id @default(autoincrement())
  email     String   @unique
  name      String?
  createdAt DateTime @default(now())
}
```

| 키워드      | 의미     |
| -------- | ------ |
| model    | 테이블    |
| @id      | 기본 키   |
| @unique  | 유니크 제약 |
| @default | 기본값    |

## 5. 관계(Relation) 설정

### 5.1 1:N 관계

```prisma
model User {
  id    Int    @id @default(autoincrement())
  posts Post[]
}

model Post {
  id     Int  @id @default(autoincrement())
  userId Int
  user   User @relation(fields: [userId], references: [id])
}
```

### 5.2 N:M 관계

```prisma
model Student {
  id      Int      @id @default(autoincrement())
  courses Course[]
}

model Course {
  id       Int        @id @default(autoincrement())
  students Student[]
}
```

Prisma가 중간 테이블을 자동 생성한다.

## 6. Prisma Client

### 6.1 Client 생성

```bash
npx prisma generate
```

### 6.2 Client 사용

```ts
import { PrismaClient } from '@prisma/client'
const prisma = new PrismaClient()
```

## 7. CRUD 기본 사용법

Create

```ts
await prisma.user.create({
  data: {
    email: 'test@test.com',
    name: 'Kim'
  }
})
```

Read

```ts
await prisma.user.findMany()

await prisma.user.findUnique({
  where: { id: 1 }
})
```

Update

```ts
await prisma.user.update({
  where: { id: 1 },
  data: { name: 'Lee' }
})
```

Delete

```ts
await prisma.user.delete({
  where: { id: 1 }
})
```

## 8. 조건 검색

```ts
await prisma.user.findMany({
  where: {
    email: {
      contains: '@gmail.com'
    }
  }
})
```

AND/OR 조건

```ts
where: {
  OR: [
    { name: 'Kim' },
    { name: 'Lee' }
  ]
}
```

## 9. Relation 조회

include

```ts
await prisma.user.findMany({
  include: {
    posts: true
  }
})
```

select

```ts
await prisma.user.findMany({
  select: {
    email: true
  }
})
```

## 10. Transaction

```ts
await prisma.$transaction([
  prisma.user.create({ data: {...} }),
  prisma.post.create({ data: {...} })
])
```

- ACID 특성 보장
- 여러 쿼리를 하나의 작업 단위로 처리

## 11. Prisma Migrate

마이그레이션 생성

```bash
npx prisma migrate dev --name add_user_table
```

동작 과정

1. schema.prisma 변경
2. SQL 마이그레이션 파일 생성
3. DB 반영 및 Client 재생성

## 12. Prisma Studio

```bash
npx prisma studio
```

- 웹 기반 DB 관리 UI
- 개발 중 데이터 확인 및 수정 가능

## 13. Prisma vs 다른 ORM

| 항목     | Prisma | TypeORM | Sequelize |
| ------ | ------ | ------- | --------- |
| 타입 안정성 | 매우 높음  | 보통      | 낮음        |
| 자동 완성  | 우수     | 보통      | 낮음        |
| 스키마 중심 | O      | X       | X         |
| 러닝 커브  | 낮음     | 높음      | 중간        |

## 14. Next.js + Prisma 권장 설정

```ts
// lib/prisma.ts
import { PrismaClient } from '@prisma/client'

export const prisma =
  globalThis.prisma || new PrismaClient()

if (process.env.NODE_ENV !== 'production') {
  globalThis.prisma = prisma
}
```

- 개발 환경 Hot Reload 시 Client 중복 생성 방지

## 15. 실무에서 중요한 포인트

- Relation 설계
- 트랜잭션 사용 시점
- include vs select 차이
- n+1 문제 방지
- 마이그레이션 관리 전략

## 16. 정리

Prisma는
타입 안정성, 생산성, 유지보수성을 모두 만족시키는 ORM이다.

특히

- Next.js
- API 서버
- SaaS / 스타트업 환경
에서 매우 강력한 선택지다.