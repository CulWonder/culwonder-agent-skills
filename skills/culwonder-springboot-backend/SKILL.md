---
name: culwonder-springboot-backend
description: >-
  Implements Spring Boot 3.5 backend APIs following Culwonder / Leeds Profile
  conventions — Allman braces, DDD aggregates, QueryDSL-only repositories,
  CommonResponse pagination, API prefix rules, and refresh-token policy. Use when
  building or reviewing Culwonder Java backends, Leeds Profile APIs, or copying
  this architecture to a new Spring Boot project.
---

# Culwonder Spring Boot Backend

Culwonder 프로젝트용 백엔드 표준 (Leeds Profile Spring Boot Core 계열).

## 사용 시점

- 도메인 추가·수정 (`Controller`, `Service`, `Repository`, `Entity`)
- Culwonder 백엔드 규칙 기준 PR 리뷰
- Leeds Profile 보일러플레이트로 새 서비스 부트스트랩
- "이 API는 어떻게 만들어야 하나?" 질문에 답할 때

## 빠른 워크플로

1. **범위** — Guest (`/api/`, `/api-guest/`), logined, business, admin 중 어느 접두사?
2. **네이밍** — [네이밍 공식](references/naming-formula.md)에 `{D}`·`{d}`·`{A}`·`{S}` 치환
3. **계층** — Entity + aggregate 메서드 → Repository (QueryDSL) → Service (Entity만 반환) → Controller (DTO 인라인 + CommonResponse)
4. **검증** — 완료 전 아래 체크리스트 실행

## 네이밍 공식 (요약)

| 대상 | 공식 | 예 (D=Store) |
|------|------|--------------|
| Controller | `{D}{A}Controller` | `StoreController`, `StoreAdminController` |
| Service | `{D}{S}Service` | `StoreService`, `StoreCallService` |
| Repository | `{D}Repository{R}` | `StoreRepository`, `StoreRepositoryImpl` |
| Request/Response | `{D}{Act}Request`, `{D}{Type}Response` | `StoreCreateRequest`, `StoreListResponse` |
| 테이블 | `{d}_module[_{child}]` | `store_module`, `store_module_image` |
| 목록 API | `GET /{P}/{d}/list` | `GET /api/store/list` |
| 페이징 JSON 키 | `{item}List` | `storeList` |

상세 토큰·금지 접미·메서드 공식: [references/naming-formula.md](references/naming-formula.md)

## 절대 어기면 안 되는 규칙

| 영역 | 규칙 |
|------|------|
| 스타일 | Allman 중괄호, 4칸 들여쓰기, FQCN 금지, 미사용 import 제거 |
| API 경로 | 리소스 단수형 + 목록은 `/list` (예: `GET /api/store/list`) |
| 게스트 컨트롤러 | `{Domain}Controller` → `@RequestMapping("/")`, 메서드에 전체 경로 |
| DTO | Service는 Entity 반환; Controller에서 인라인 변환 (헬퍼 메서드 금지) |
| 응답 | `CommonResponse` 래핑; 페이징 목록은 `{item}List` + `PageResponseUtil` |
| 데이터 | QueryDSL만 (JPQL 금지); Aggregate Root Repository만 |
| 엔티티 | 정적 팩토리 메서드 금지; root 메서드로 DDD cascade |
| 인증 토큰 | Refresh 만료 → 에러 (재발급·sliding session 금지) |
| 설정 | AI 세션에서 `src/main/resources/application*.properties` 편집 금지; `config/local/` 사용 |

## 새 도메인 체크리스트

```
- [ ] api/{d}/ 및 {D}{A}Controller (A=∅|Logined|Business|Admin, 필요 시)
- [ ] {D}{S}Service (S=∅|Call|Provider)
- [ ] {D}Repository + Custom + Impl (QueryDSL)
- [ ] 테이블: {d}_module (root), {d}_module_{child} (하위)
- [ ] Audit 컬럼: createdId, createdAt, updatedId, updatedAt + @EntityListeners
- [ ] Swagger GroupedOpenApi bean (displayName 숫자 접두사, 예: "01. …")
- [ ] 목록 API는 /list로 끝남; PageResponseUtil로 페이징
- [ ] service/repository 핵심 경로 테스트
```

## 계층별 책임

**Controller** — HTTP, 검증, Entity→DTO 인라인, CommonResponse, 상태 코드.

**Service** — 비즈니스 로직, `@Transactional`, Entity 반환. CallService(타 모듈), Repository 호출 가능. ProviderService 호출 금지.

**CallService** — 타 모듈 아웃바운드 호출.

**ProviderService** — 타 모듈에 노출하는 파사드 (Service 호출, 역방향 금지).

**Repository** — Aggregate Root 영속화; 복잡 조회는 `*RepositoryImpl`에서 QueryDSL.

## API 접두사 맵

| 접두사 | 권한 | 예시 |
|--------|------|------|
| `/api/{domain}/` | 공개 (누구나 호출 OK) | 토큰 검증, 공개 조회 |
| `/api-guest/{domain}/` | 게스트 전용 | 로그인, 회원가입 |
| `/api-logined/{domain}/` | 로그인 필요 | 사용자 프로필 |
| `/api-business/{domain}/` | BUSINESS 권한 | 판매자 API |
| `/api-admin/{domain}/` | ADMIN 권한 | 관리자 CRUD |

Swagger 게스트 그룹은 `/api/{domain}/**`와 `/api-guest/{domain}/**`를 모두 등록.

## 페이징 응답 형식

```json
{
  "success": true,
  "content": {
    "storeList": {
      "content": [],
      "pagination": {
        "totalPages": 0,
        "pageSize": 20,
        "hasPrevious": false,
        "hasNext": false,
        "currentPage": 0,
        "totalElements": 0,
        "first": true,
        "last": true
      }
    }
  }
}
```

키 이름: camelCase + `List` (예: `foodList`, `usersAccountList`).

## 참고 문서 (필요 시 읽기)

- [네이밍 공식 (Naming Formula)](references/naming-formula.md)
- [API·컨트롤러 규칙](references/api-conventions.md)
- [엔티티·서비스·리포지토리 (DDD + QueryDSL)](references/business-layer.md)
- [코딩 스타일·토큰·설정 불변](references/core-standards.md)
- [신규 프로젝트·도메인 셋업](references/project-setup.md)

## 관련 스킬

- `querydsl-optimization` — QueryDSL 쿼리 성능 리뷰
- `oas-swagger-codegen` — OpenAPI와 컨트롤러 동기화
- `erd-schema-designer` — 마이그레이션 전 스키마 설계

## 설치

```bash
npx skills add CulWonder/culwonder-agent-skills@culwonder-springboot-backend -g -y
```

레포: [CulWonder/culwonder-agent-skills](https://github.com/CulWonder/culwonder-agent-skills)

애플리케이션 레포에는 `.cursor/rules/`를 두어 Cursor에서 항상 적용하고, 이 스킬은 휴대용·온디맨드 가이드로 사용.
