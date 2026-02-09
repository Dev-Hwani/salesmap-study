# JWT (JSON Web Token) 정리

JWT(JSON Web Token)는  
클라이언트와 서버 간에 인증·인가 정보를 안전하게 전달하기 위한 토큰 기반 표준입니다.

주로 로그인 이후 사용자의 인증 상태를 유지하고  
API 요청 시 “이 사용자가 누구인지”를 서버가 식별하기 위해 사용됩니다.

---

## 1. JWT가 왜 필요한가?

### 기존 세션 기반 인증의 한계

기존 세션 방식은 다음과 같은 구조입니다.

- 로그인 시 서버에 세션 저장
- 클라이언트는 세션 ID를 쿠키로 전달
- 서버는 메모리 또는 Redis 등에서 세션 조회

문제점:
- 서버가 상태(state)를 저장해야 함
- 서버 확장 시 세션 공유 필요
- 서버 장애 시 세션 유실 가능

### JWT의 등장 배경

JWT는 서버가 인증 상태를 저장하지 않는(stateless) 방식을 목표로 등장했습니다.

- 인증 정보를 토큰에 담아 클라이언트에 전달
- 서버는 토큰의 유효성만 검증
- 세션 저장소가 필요 없음

---

## 2. JWT란 무엇인가?

JWT는 다음 특징을 가집니다.

- JSON 기반
- 서명(Signature)을 통해 위변조 방지
- URL-safe 문자열
- 상태를 서버에 저장하지 않는 인증 방식에 적합

JWT는 단순 문자열이지만  
그 자체에 인증 정보를 담고 있는 “자기 설명적 토큰(Self-contained Token)”입니다.

---

## 3. JWT의 구조

JWT는 항상 **3개의 부분**으로 구성됩니다.


각 부분은 Base64URL로 인코딩되어 `.`으로 구분됩니다.

---

### 3-1. Header

토큰의 메타 정보가 들어갑니다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

- alg: 서명에 사용한 알고리즘
- typ: 토큰 타입 (JWT)

### 3-2. Payload

실제 전달하고 싶은 정보(Claim)가 들어갑니다.

```json
{
  "sub": "userId123",
  "email": "user@example.com",
  "role": "ADMIN",
  "iat": 1700000000,
  "exp": 1700003600
}
```

대표적인 Claim 종류

- sub (Subject): 사용자 식별자
- iat (Issued At): 토큰 발급 시간
- exp (Expiration): 만료 시간
- iss (Issuer): 발급자
- aud (Audience): 대상

### 3-3. Signature

토큰의 위변조를 방지하는 핵심 부분입니다.

```text
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secretKey
)
```

- 서버만 알고 있는 secretKey로 서명 생성
- 클라이언트는 서명을 위조할 수 없음
- Payload가 조금이라도 바뀌면 검증 실패

## 4. JWT 인증 흐름

### 로그인 시

1. 사용자가 ID/PW로 로그인 요청
2. 서버에서 사용자 검증
3. 서버가 JWT 생성
4. JWT를 클라이언트에 전달

### 이후 요청 시

1. 클라이언트가 JWT를 함께 전송
2. 서버가 JWT 검증
3. 유효하면 요청 처리

## 5. JWT 전달 방식

### 5-1. Authorization Header

```http
Authorization: Bearer <JWT>
```

- REST API에서 가장 일반적인 방식
- XSS 공격에 취약할 수 있음 (localStorage 사용 시)

### 5-2. HttpOnly Cookie (권장)

- JavaScript에서 접근 불가
- XSS 공격 방어에 유리
- 서버 중심 인증 구조에 적합

실무에서는 JWT + HttpOnly Cookie 조합을 많이 사용합니다.

## 6. JWT의 특징 정리

### 장점

- 서버 상태 저장 불필요 (Stateless)
- 서버 확장에 유리
- API 서버, 마이크로서비스에 적합
- 모바일 / SPA 환경과 궁합 좋음

### 단점

- 토큰 길이가 김
- 한 번 발급되면 서버에서 즉시 무효화 어려움
- Payload가 노출됨
- 탈취 시 만료 전까지 사용 가능

## 7. JWT 보안에서 반드시 알아야 할 것

### 7-1. 만료 시간(exp)은 필수

- JWT는 만료 시간이 없으면 위험
- 짧은 access token + refresh token 구조 권장

### 7-2. JWT는 암호화가 아니다

- Base64 디코딩 가능
- 서명은 위변조 방지용
- 민감 정보 저장 X

### 7-3. 토큰 저장 위치 중요

| 저장 위치           | 위험       |
| --------------- | -------- |
| localStorage    | XSS에 취약  |
| sessionStorage  | XSS에 취약  |
| HttpOnly Cookie | 상대적으로 안전 |

## 8. Access Token vs Refresh Token

### Access Token

- 짧은 만료 시간
- API 요청용

### Refresh Token

- 긴 만료 시간
- Access Token 재발급용
- 보통 DB 또는 Redis에 저장

## 9. JWT를 사용하면 안 되는 경우

- 즉각적인 강제 로그아웃이 중요한 서비스
- 토큰 폐기 요구가 잦은 경우
- 단일 서버 + 단순 서비스

→ 이런 경우 세션 기반이 더 적합할 수 있음

## 10. JWT에 대한 흔한 오해

❌ JWT = 보안
⭕ JWT = 인증 전달 수단

❌ JWT 쓰면 무조건 안전
⭕ 구조와 저장 방식이 더 중요

❌ JWT는 암호화됨
⭕ 서명만 되어 있음

## 11. 이 프로젝트에서 JWT를 사용한 이유

- 서버가 인증 책임을 명확히 가지도록 설계
- 상태 저장 없이 인증 처리
- HttpOnly 쿠키를 사용해 보안 단순화
- API 중심 구조와 잘 맞음

## 12. 정리

JWT는 단순한 토큰이 아니라
인증 책임을 어떻게 나눌 것인지에 대한 설계 선택입니다.

JWT를 쓴다는 것은:
- 인증 상태를 서버에 저장하지 않겠다는 결정
- 클라이언트와 서버의 책임을 명확히 나누겠다는 의미

따라서 JWT를 사용할 때는
항상 “왜 JWT인가?”를 설명할 수 있어야 합니다.