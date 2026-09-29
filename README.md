# BuildUp Docs

BuildUp 백엔드의 1차 배포 상태와 팀 협업 기준을 한곳에서 관리하는 문서 저장소입니다.

이 저장소에는 비밀번호, JWT Secret, 개인 IP, 실제 운영 서버 주소를 기록하지 않습니다. 배포 환경의 값은 권한이 있는 팀원에게 별도 보안 채널로 전달합니다.

## 문서 목차

1. [1차 배포 기능 정리](docs/01-1차-배포-기능.md)
2. [전체 시스템 구조](docs/02-전체-시스템-구조.md)
3. [DB 구조와 ERD](docs/03-DB-구조와-ERD.md)
4. [API 사용 안내](docs/04-API-사용-안내.md)
5. [GitHub 협업 규칙](docs/05-GitHub-협업-규칙.md)
6. [프론트엔드 연동 방법](docs/06-프론트엔드-연동-방법.md)
7. [추후 필수 보완](docs/07-추후-필수-보완.md)
8. [주요 기술 결정](docs/08-주요-기술-결정.md)

## 관련 저장소

- 백엔드: [hiallu60/buildup_backend](https://github.com/hiallu60/buildup_backend)
- 문서: [hiallu60/buildup_docs](https://github.com/hiallu60/buildup_docs)

## 현재 기준

- 런타임: Java 17, Spring Boot 4.1.1
- 데이터베이스: MySQL, Flyway V1~V8
- 인증: Spring Security, BCrypt, Access JWT, Refresh Token
- 배포: EC2 애플리케이션 서버와 비공개 RDS
- 파일 원본 저장소와 HTTPS 도메인은 후속 작업

문서는 구현과 배포가 바뀔 때 같은 PR에서 함께 갱신하는 것을 원칙으로 합니다.
