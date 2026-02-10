# TypeScript 완전 정리 (A to Z)

이 문서는 TypeScript의 개념, 목적, 동작 원리, 문법, 실무 활용,  
그리고 **면접에서 설명할 수 있는 수준**까지 이해하는 것을 목표로 정리한 문서입니다.

---

## 1. TypeScript란 무엇인가?

### 1.1 TypeScript 정의

**TypeScript는 JavaScript에 타입 시스템을 추가한 언어**입니다.

- Microsoft에서 개발
- JavaScript의 상위 집합(Superset)
- 컴파일 시점에 타입 오류를 잡아줌
- 최종적으로 **JavaScript로 변환되어 실행**

👉 브라우저와 Node.js는 TypeScript를 직접 실행하지 않습니다.

---

### 1.2 왜 TypeScript가 필요한가?

JavaScript는 **동적 타입 언어**입니다.

```js
let value = 10;
value = "hello"; // 에러 없음
```

👉 실행 전까지 오류를 알 수 없음
👉 규모가 커질수록 버그 추적이 어려움

TypeScript는 이를 컴파일 단계에서 차단합니다.

```ts
let value: number = 10;
value = "hello"; // 컴파일 에러
```

## 2. TypeScript의 핵심 목표

1. 정적 타입 검사
2. 코드 안정성 향상
3. 의도 명확화
4. 대규모 프로젝트에 적합
5. 개발자 경험(DX) 향상

## 3. TypeScript 동작 구조

```scss
TypeScript (.ts, .tsx)
        ↓
   Type Checker
        ↓
   JavaScript (.js)
        ↓
 실행 (Browser / Node.js)
```

👉 타입은 컴파일 단계에서만 존재
👉 런타임에는 모두 사라짐

## 4. 기본 타입 (Primitive Types)

### 4.1 기본 타입 목록

```ts
string
number
boolean
null
undefined
symbol
bigint
```

예시:

```ts
let username: string = "kim";
let age: number = 30;
let isAdmin: boolean = false;
```

### 4.2 배열 타입

```ts
let numbers: number[] = [1, 2, 3];
let users: string[] = ["a", "b"];
```

또는

```ts
let numbers: Array<number> = [1, 2, 3];
```

## 5. 객체 타입 (Object Type)

```ts
const user: {
  id: number;
  name: string;
  email?: string;
} = {
  id: 1,
  name: "kim",
};
```

- ? → optional (선택 속성)

## 6. 함수 타입

### 6.1 함수 파라미터 & 반환 타입

```ts
function add(a: number, b: number): number {
  return a + b;
}
```

### 6.2 void / never

```ts
function log(msg: string): void {
  console.log(msg);
}
```

```ts
function throwError(): never {
  throw new Error("error");
}
```

## 7. Type vs Interface

### 7.1 type

```ts
type User = {
  id: number;
  name: string;
};
```

### 7.2 interface

```ts
interface User {
  id: number;
  name: string;
}
```

### 7.3 차이점 요약

| 구분    | type | interface |
| ----- | ---- | --------- |
| 확장    | & 사용 | extends   |
| 선언 병합 | ❌    | ⭕         |
| 객체 표현 | ⭕    | ⭕         |
| 유니온   | ⭕    | ❌         |

👉 도메인 모델 → interface
👉 유틸/조합 타입 → type

## 8. Union & Intersection

### 8.1 Union (|)

```ts
type Role = "A" | "B" | "C";
```
```ts
function printId(id: number | string) {}
```

### 8.2 Intersection (&)

```ts
type User = { id: number };
type Admin = { role: "admin" };

type AdminUser = User & Admin;
```

## 9. Literal Type

```ts
type Status = "OPEN" | "CLOSED" | "PENDING";
```

👉 CRM, 상태값, 권한에서 매우 중요

## 10. Enum

```ts
enum Role {
  A = "A",
  B = "B",
  C = "C",
}
```

👉 런타임에도 존재
👉 요즘은 Literal Union을 더 많이 사용

## 11. Generic (제네릭)

### 11.1 기본 개념

```ts
function identity<T>(value: T): T {
  return value;
}
```

### 11.2 실무 예시

```ts
function apiResponse<T>(data: T) {
  return {
    success: true,
    data,
  };
}
```

## 12. Type Narrowing (타입 좁히기)

```ts
function print(value: string | number) {
  if (typeof value === "string") {
    value.toUpperCase();
  }
}
```

## 13. Utility Types

### 13.1 자주 쓰는 유틸 타입

```ts
Partial<T>
Required<T>
Pick<T, K>
Omit<T, K>
Record<K, V>
```

예시:

```ts
type User = {
  id: number;
  name: string;
  email: string;
};

type UserPreview = Pick<User, "id" | "name">;
```

## 14. Type Assertion (타입 단언)

```ts
const el = document.getElementById("root") as HTMLElement;
```

⚠️ 타입 체크를 무시하므로 남용 금지

## 15. any vs unknown

```ts
let a: any;
let u: unknown;
```

| 구분    | any | unknown |
| ----- | --- | ------- |
| 타입 체크 | ❌   | ⭕       |
| 안전성   | 낮음  | 높음      |

👉 unknown + narrowing 권장

## 16. TypeScript 설정 (tsconfig.json)

중요 옵션:

```json
{
  "strict": true,
  "noImplicitAny": true,
  "strictNullChecks": true
}
```

👉 strict 모드는 실무에서 필수

## 17. React + TypeScript

### 17.1 Props 타입

```ts
type Props = {
  title: string;
};

function Header({ title }: Props) {
  return <h1>{title}</h1>;
}
```

### 17.2 이벤트 타입

```ts
function onChange(e: React.ChangeEvent<HTMLInputElement>) {}
```

18. Next.js + TypeScript 실무 포인트

- API Request / Response 타입 정의
- 서버/클라이언트 타입 공유
- Prisma 타입 자동 연동
- 정책 로직 타입 안정성 확보

19. TypeScript의 장점 요약

- 컴파일 단계에서 버그 차단
- 코드 의도 명확
- 리팩토링 안정성
- 자동완성/IDE 지원 극대화
- 대규모 프로젝트에 필수

20. 단점 및 주의점

- 초기 학습 비용
- 타입 설계에 시간 필요
- 과도한 타입은 가독성 저하

👉 타입은 목적을 위해 존재해야 함

21. 면접에서 자주 나오는 질문
Q. TypeScript는 런타임 성능에 영향 있나요?

❌ 없습니다.
TypeScript는 컴파일 타임에만 사용됩니다.

Q. any는 언제 쓰나요?

- 외부 라이브러리
- 점진적 마이그레이션
- 하지만 최대한 지양

Q. interface와 type은 어떻게 구분해서 쓰나요?

- 도메인 모델 → interface
- 조합/유틸 → type

22. 한 줄 요약

TypeScript는 코드를 “잘 돌아가게” 만드는 언어가 아니라
“깨지지 않게” 만드는 언어다.