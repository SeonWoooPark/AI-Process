# 🔗 API Spec 설계 지침

## 목적
API Spec은 클라이언트와 서버 간의 통신 규격을 명확히 정의하기 위한 문서이다.  
Claude는 RESTful 또는 GraphQL 설계 원칙을 기반으로, 일관된 데이터 구조, 에러 처리, 인증 로직을 포함한 안정적인 API 명세를 생성해야 한다.

---

## 문서 구성

### 1. 기본 규칙
- API는 RESTful 원칙을 준수한다.  
- HTTP Method의 역할을 명확히 구분한다.  
  - GET: 데이터 조회  
  - POST: 데이터 생성  
  - PUT/PATCH: 데이터 수정  
  - DELETE: 데이터 삭제  
- 엔드포인트는 **kebab-case** 또는 **소문자 복수형**으로 작성한다.  
  - 예: `/api/v1/users`, `/api/v1/user-profiles`  
- 모든 응답은 JSON 형식으로 통일한다.

---

### 2. 요청(Request) 명세
- **Method**: 요청에 사용되는 HTTP 메서드  
- **Endpoint**: `/api/v1/...` 형태의 URI  
- **Headers**: Authorization, Content-Type 등 명시  
- **Parameters / Body 구조**: 요청 필드, 타입, 필수 여부  
- **Validation 규칙**: 유효성 검증 조건 명시  

#### 예시
```json
POST /api/v1/users
{
  "email": "string (required)",
  "password": "string (required, minLength:8)"
}
```

---

### 3. 응답(Response) 명세
- 모든 응답은 공통 구조를 따른다.

**공통 응답 포맷**
```json
{
  "status": "success",
  "message": "optional",
  "data": {}
}
```

**성공 응답 예시**
```json
{
  "status": "success",
  "message": "User created successfully",
  "data": {
    "id": "uuid",
    "email": "user@example.com"
  }
}
```

**실패 응답 예시**
```json
{
  "status": "error",
  "message": "Invalid credentials",
  "code": 401
}
```

---

### 4. 에러 처리(Error Handling)
- 공통 에러 코드 테이블을 정의한다.

| HTTP 코드 | 상태                   | 설명                       |
|-----------|------------------------|----------------------------|
| 200       | OK                     | 요청 성공                  |
| 201       | Created                | 데이터 생성 성공           |
| 400       | Bad Request            | 잘못된 요청                |
| 401       | Unauthorized           | 인증 실패                  |
| 403       | Forbidden              | 접근 권한 없음             |
| 404       | Not Found              | 리소스 없음                |
| 409       | Conflict               | 중복 데이터 등 충돌        |
| 422       | Unprocessable Entity   | 검증 실패                  |
| 500       | Internal Server Error  | 서버 내부 오류             |

**에러 응답 구조 예시**
```json
{
  "status": "error",
  "message": "Invalid request data",
  "code": 422,
  "details": {
    "email": "This field is required"
  }
}
```

**에러 처리 규칙**
- Validation 실패 시 422 사용
- 인증 관련 문제는 401 / 403 구분
- 서버 내부 오류는 사용자 친화적인 메시지로 처리

---

### 5. 인증 및 보안(Authentication & Security)
- 기본 인증 방식: JWT (JSON Web Token) 사용
- Authorization: Bearer <token> 헤더 포함
- 토큰에는 사용자 ID 및 권한 정보를 포함
- 토큰 만료 처리
- Access Token: 짧은 수명 (예: 1시간)
- Refresh Token: 장기 수명 (예: 7일)
- Refresh Token은 /api/v1/auth/refresh 엔드포인트로 갱신
- 역할 기반 접근 제어(Role-based Access Control)
- 예: admin, user, guest
- 보안 정책
- HTTPS 통신 필수
- CORS 정책 명시 (허용 origin, method 등)
- Rate Limiting 적용 기준 설정

---

### 6. 버전 관리(API Versioning)
- 버전 명시 규칙: /api/v1/ 형식으로 명시
- 버전 변경 시 고려사항
- 기존 버전 호환성 유지 (deprecation 기간 제공)
- 주요 변경사항은 CHANGELOG.md에 기록
- 버전 간 차이점 명시 (필드명 변경, 응답 구조 수정 등)

**예시**

/api/v1/users  → 기존 버전
/api/v2/users  → 개선된 필드, 성능 최적화 등

**버전 관리 원칙**
- Breaking change 발생 시 반드시 새로운 버전 생성
- Minor update는 동일 버전 내 문서 갱신으로 처리

---

### 7. 문서화
- API 문서는 OpenAPI 3.0 (Swagger) 형식으로 자동화 가능
- 각 엔드포인트에 요청/응답 예시 포함
- 변경 시 version, date, author 메타데이터 기록

---

### ✅ 최종 출력 형식 예시

Claude가 생성할 API Spec은 아래와 같은 구조로 작성한다.

```json
{
  "method": "POST",
  "endpoint": "/api/v1/users",
  "description": "새로운 사용자 계정 생성",
  "request": {
    "headers": { "Authorization": "Bearer token" },
    "body": { "email": "string", "password": "string" }
  },
  "response": {
    "status": "success",
    "message": "User created",
    "data": { "id": "uuid" }
  },
  "errors": [
    { "code": 400, "message": "Invalid email format" },
    { "code": 409, "message": "Email already exists" }
  ]
}
```
