# API 사용 안내

## 기본 규칙

- 개발 중 API 주소는 팀에서 전달받은 값을 사용합니다.
- 예시 문서에서는 실제 주소 대신 `https://api.example.com`을 사용합니다.
- 인증 API와 헬스 API를 제외한 보호 API에는 Access Token이 필요합니다.

```http
Authorization: Bearer {accessToken}
Content-Type: application/json
```

DB 주소, DB 계정, DB 비밀번호, JWT Secret은 프론트엔드에 전달하지 않습니다.

## 엔드포인트 목록

### 상태 확인

| Method | Path | 설명 |
|---|---|---|
| GET | `/api/health` | 서버 실행 상태 확인 |

### 인증과 사용자

| Method | Path | 설명 |
|---|---|---|
| POST | `/api/auth/signup` | 회원가입 |
| POST | `/api/auth/login` | 로그인 및 토큰 발급 |
| POST | `/api/auth/refresh` | Refresh Token으로 토큰 갱신 |
| POST | `/api/auth/logout` | Refresh Token 폐기 |
| GET | `/api/users/me` | 현재 로그인 사용자 조회 |

### 현장과 공정

| Method | Path | 설명 |
|---|---|---|
| POST | `/api/sites` | 현장 생성 |
| POST | `/api/sites/join` | 참여 코드로 현장 가입 |
| GET | `/api/sites` | 참여 현장 목록 |
| GET | `/api/sites/{siteId}` | 현장 상세 |
| GET | `/api/sites/{siteId}/members` | 현장 구성원 목록 |
| GET | `/api/sites/{siteId}/join-code` | 현장 참여 코드 조회 |
| PATCH | `/api/sites/{siteId}/members/{membershipId}/role` | 현장 역할 변경 |
| GET | `/api/sites/{siteId}/processes` | 공정 목록 |
| POST | `/api/sites/{siteId}/processes` | 공정 생성 |
| PUT | `/api/sites/{siteId}/processes/{processId}` | 공정 수정 |

### 보고서

| Method | Path | 설명 |
|---|---|---|
| POST | `/api/sites/{siteId}/reports` | 보고서 작성 |
| GET | `/api/sites/{siteId}/reports` | 현장 보고서 목록 |
| GET | `/api/reports/{reportId}` | 보고서 상세 |
| POST | `/api/reports/{reportId}/approve` | 보고서 승인 |
| POST | `/api/reports/{reportId}/reject` | 보고서 반려 |

### 자재 요청

| Method | Path | 설명 |
|---|---|---|
| POST | `/api/sites/{siteId}/material-requests` | 자재 요청 작성 |
| GET | `/api/sites/{siteId}/material-requests` | 자재 요청 목록 |
| GET | `/api/material-requests/{requestId}` | 자재 요청 상세 |
| PATCH | `/api/material-requests/{requestId}/status` | 자재 요청 상태 변경 |

### 알림과 디바이스

| Method | Path | 설명 |
|---|---|---|
| GET | `/api/notifications` | 알림 목록 |
| GET | `/api/notifications/unread-count` | 미읽음 개수 |
| PATCH | `/api/notifications/{notificationId}/read` | 알림 읽음 처리 |
| PATCH | `/api/notifications/read-all` | 전체 읽음 처리 |
| POST | `/api/devices/tokens` | 디바이스 토큰 등록 |
| DELETE | `/api/devices/tokens` | 디바이스 토큰 삭제 |

## 토큰 사용 흐름

1. 로그인 성공 응답에서 Access Token과 Refresh Token을 받습니다.
2. 일반 API 요청에는 Access Token만 `Authorization` 헤더에 넣습니다.
3. Access Token 만료 시 Refresh API를 한 번 호출합니다.
4. 성공하면 응답으로 받은 새 토큰 쌍으로 기존 값을 교체합니다.
5. 같은 Refresh Token을 여러 요청에서 동시에 재사용하지 않습니다.
6. Refresh Token도 만료되거나 거절되면 로그인 화면으로 이동합니다.

현재 설정은 Access JWT 15분, Refresh Token 14일입니다.

## 대표 상태 코드

| 상태 | 의미 | 프론트 처리 예시 |
|---|---|---|
| 200/201 | 성공 | 응답 반영 |
| 400 | 요청 형식 또는 검증 실패 | 입력값 안내 |
| 401 | 로그인 또는 토큰 문제 | 갱신 시도 후 로그인 이동 |
| 403 | 로그인했지만 권한 부족 | 권한 안내 |
| 404 | 대상 없음 또는 접근 가능한 대상이 아님 | 목록으로 이동 |
| 409 | 중복 또는 현재 상태와 충돌 | 중복·상태 안내 |

## 오류 응답 규격

```json
{
  "timestamp": "2026-10-03T03:00:00Z",
  "status": 400,
  "code": "VALIDATION_FAILED",
  "message": "Request validation failed",
  "path": "/api/auth/signup",
  "fieldErrors": {
    "email": "must be a well-formed email address"
  }
}
```

- 프론트엔드는 변경될 수 있는 `message`가 아니라 안정적인 `code`를 기준으로 동작과 문구를 결정합니다.
- 입력 검증 오류는 `fieldErrors`의 필드명과 메시지를 화면 입력란에 연결합니다.
- 토큰이 없으면 `AUTHENTICATION_REQUIRED`, 토큰이 유효하지 않으면 `INVALID_ACCESS_TOKEN`, 권한이 없으면 `ACCESS_DENIED`가 반환됩니다.
- 처리되지 않은 내부 오류는 `INTERNAL_SERVER_ERROR`로 반환되며 내부 상세 정보는 응답에 노출하지 않습니다.

간단한 호출 예시는 [HTTP 예제](../api-examples/buildup-api.http)에 있습니다. 전체 공통 오류 코드와 도메인 오류 코드는 백엔드 저장소의 `docs/ERRORS.md`를 기준으로 관리합니다.
