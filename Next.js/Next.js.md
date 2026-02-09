# Next.js 정리

Next.js는  
**React 기반의 풀스택 웹 프레임워크**로  
프론트엔드 개발을 넘어 **라우팅, 서버 렌더링, API, 배포 전략까지 포함**하는 실무 표준 프레임워크.

---

## 1. Next.js란 무엇인가?

- React 기반 프레임워크
- Vercel에서 개발
- CSR, SSR, SSG, ISR을 모두 지원
- 프론트엔드 + 백엔드(API) 통합 가능

👉 **“React를 실무에서 제대로 쓰기 위한 프레임워크”**

---

## 2. 왜 Next.js를 사용하는가?

### React 단독 사용의 한계

- SEO 취약
- 라우팅 직접 구성 필요
- 빌드/배포 전략 복잡
- 서버 렌더링 직접 구현 어려움

### Next.js의 장점

- 파일 기반 라우팅
- 서버 사이드 렌더링 기본 지원
- SEO 친화적
- API 서버 내장
- 코드 분할 자동 처리
- 배포 최적화(Vercel)

---

## 3. Next.js의 핵심 특징 요약

| 기능 | 설명 |
|----|----|
| File Routing | 파일 = 라우트 |
| SSR | 서버에서 HTML 생성 |
| SSG | 빌드 시 HTML 생성 |
| ISR | 정적 페이지 점진적 재생성 |
| API Routes | 백엔드 API 구현 가능 |
| Image Optimization | 이미지 자동 최적화 |
| App Router | 최신 아키텍처 |

---

## 4. 렌더링 방식

### 4-1. CSR (Client Side Rendering)

- 브라우저에서 렌더링
- 초기 로딩 느림
- SEO 불리

```tsx
useEffect(() => {
  fetch(...)
}, [])
```

### 4-2. SSR (Server Side Rendering)

- 요청 시 서버에서 HTML 생성
- SEO 유리
- 요청마다 실행

```tsx
export async function getServerSideProps() {}
```

### 4-3. SSG (Static Site Generation)

- 빌드 시 HTML 생성
- 매우 빠름
- 데이터 변경에 취약

```tsx
export async function getStaticProps() {}
```

### 4-4. ISR (Incremental Static Regeneration)

- 정적 + 갱신
- 실무에서 가장 많이 사용

## 5. Pages Router vs App Router

### 5-1. Pages Router

- /pages 디렉토리
- 파일 기반 라우팅
- React 중심

```tsx
pages/index.tsx
```

### 5-2. App Router

- /app 디렉토리
- React Server Component 기반
- 레이아웃/로딩/에러 구조화

```tsx
app/page.tsx
app/layout.tsx
```

👉 현재 Next.js의 핵심 아키텍처

## 6. App Router 구조 이해

```tsx
app/
 ├─ layout.tsx
 ├─ page.tsx
 ├─ loading.tsx
 ├─ error.tsx
 └─ api/
```

주요 파일 역할

- layout.tsx: 공통 레이아웃
- page.tsx: 실제 페이지
- loading.tsx: Suspense 로딩 UI
- error.tsx: 에러 처리
- not-found.tsx: 404 처리

## 7. React Server Component (RSC)

개념

- 기본적으로 서버에서 실행
- 브라우저 JS 번들에서 제외
- 보안 & 성능 향상

```tsx
export default async function Page() {
  const data = await fetch(...)
}
```

Client Component

```tsx
"use client";

import { useState } from "react";
```

- 상태, 이벤트, 훅 사용 가능
- 필요한 곳에서만 사용

👉 Server 우선, Client 최소화

## 8. 데이터 패칭 전략

fetch 기본 동작

- 서버 컴포넌트에서 실행
- 캐싱 자동 처리

```ts
fetch(url, { cache: "no-store" })
```

옵션:

- force-cache
- no-store
- revalidate

## 9. 라우팅 & 네비게이션

동적 라우트

```txt
app/deals/[id]/page.tsx
```
```tsx
params.id
```

useRouter / Link
```tsx
import Link from "next/link";
```
```tsx
router.push("/login");
```

## 10. API Routes (백엔드 기능)

API 라우트 구조

```txt
app/api/deals/route.ts
```
```ts
export async function POST(req: Request) {}
```

- REST API 구현 가능
- 인증/DB 처리 가능

👉 Next.js = 풀스택 프레임워크

## 11. 미들웨어(Middleware)

```ts
11. 미들웨어(Middleware)
```

- 요청 전 처리
- 인증, 리다이렉트, 권한 체크

```ts
export function middleware(req) {}
```

## 12. 인증 (JWT + Next.js)

- Middleware에서 토큰 검증
- Server Component에서 세션 확인
- API Route에서 권한 검증

👉 인증 로직을 프론트 + 백엔드에 자연스럽게 분산

## 13. 환경 변수

```env
NEXT_PUBLIC_API_URL=
DATABASE_URL=
```
- NEXT_PUBLIC_ → 브라우저 노출
- 나머지 → 서버 전용

## 14. 스타일링

- CSS Module
- Tailwind CSS (실무 표준)
- styled-components 가능

```tsx
className="flex gap-2"
```

## 15. 성능 최적화 기능

Image Optimization

```tsx
<Image src="/img.png" />
```

- 자동 리사이즈
- lazy loading

Code Splitting

- 페이지 단위 자동 분할
- 필요한 JS만 로드

## 16. 배포

Vercel

- Next.js 최적화 플랫폼
- SSR, ISR 완벽 지원
- 환경 변수 관리

```bash
vercel deploy
```

## 17. 실무에서 중요한 설계 포인트

- Server Component 중심 설계
- Client Component 최소화
- 페이지 단위 책임 분리
- API Route는 도메인 기준으로 구성
- DB 접근은 서버에서만

## 18. Next.js를 잘 쓴다는 것

- 렌더링 방식을 의도적으로 선택
- Server/Client 경계를 명확히 함
- SEO·성능·보안을 함께 고려
- 프레임워크 기능을 “왜” 쓰는지 설명 가능

## 19. 이 프로젝트에서 Next.js를 선택한 이유

- 복잡한 CRM UI에 적합
- 서버 렌더링 기반 초기 로딩 개선
- API와 UI를 하나의 코드베이스로 관리
- 확장성과 유지보수 고려

## 20. 정리

Next.js는 단순한 React 프레임워크가 아니라
웹 애플리케이션을 구조적으로 설계하게 만드는 도구입니다.

잘 쓴 Next.js 프로젝트는
👉 코드만 봐도 설계 의도가 드러납니다.