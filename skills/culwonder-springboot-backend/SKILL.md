---
name: culwonder-springboot-backend
description: >-
  Implements Spring Boot 3.5 backend APIs following Culwonder / Leeds Profile
  conventions — Allman braces, DDD aggregates, QueryDSL-only repositories,
  CommonResponse pagination, API prefix rules, naming formula, and refresh-token
  policy. Use when building, reviewing, or bootstrapping Culwonder Java backends,
  Leeds Profile APIs, Controllers/Services/Repositories/Entities, or copying this
  architecture to a new Spring Boot project — even if the user does not name this
  skill. Trigger on Allman, QueryDSL, CommonResponse, /api-guest, /api-logined,
  /api-business, /api-admin, aggregate root, or "백엔드 규칙" questions.
---

# Culwonder Spring Boot Backend

Culwonder 프로젝트용 백엔드 표준 (Leeds Profile Spring Boot Core 계열).

> **읽는 순서:** 절대 규칙 → 워크플로·체크리스트 → 필요 시 `references/` (네이밍·API·계층·스타일·셋업). 도메인 규칙은 본문·references에 유지한다.

## 사용 시점

- 도메인 추가·수정 (`Controller`, `Service`, `Repository`, `Entity`)
- Culwonder 백엔드 규칙 기준 PR 리뷰
- Leeds Profile 보일러플레이트로 새 서비스 부트스트랩
- "이 API는 어떻게 만들어야 하나?" 질문에 답할 때

## 빠른 워크플로

1. **범위** — Guest (`/api/`, `/api-guest/`), logined, business, admin 중 어느 접두사?
2. **요청서 → 프론트 가이드** — FE `docs/backend-request` 수신 시 path·enum·요청/응답 JSON을 **확정**하고, 분량과 무관하게 항상 **프론트 가이드**로 회신 (아래 「FE 요청서 수신」). 미확정 path로 구현 시작 금지.
3. **네이밍** — [네이밍 공식](references/naming-formula.md)에 `{D}`·`{d}`·`{A}`·`{S}` 치환
4. **계층** — Entity + aggregate 메서드 → Repository (QueryDSL) → Service (Entity만 반환) → Controller (DTO 인라인 + CommonResponse)
5. **검증** — 완료 전 아래 체크리스트 실행

## FE 요청서 수신 · 프론트 가이드 (필수)

하네스 `docs/FE_BE_DOC_HANDOFF.md` · 템플릿 `frontend-guide.md` / `backend-request-reply.md`(체크리스트)를 따른다.  
**작성 양식 중복 금지** — 요청서 본문은 FE가 씀. BE는 **수신 → 확정 → 프론트 가이드 전달**만.

| 규칙 | 내용 |
|------|------|
| 저장 | 길이(짧/중/김) **분기 없음**. 항상 Spring 레포 `docs/frontend-guide/{YYYYMMDD}_{HHmmss}_{주제}-frontend-guide.md` |
| 호칭 | **프론트 가이드** = BE→FE API 계약. FE 자체 화면 문서는 **FRONTEND_GUIDE** (혼동 금지) |
| 넣을 것 | Method+Path(접두사) · Request/Response 키(`{item}List`+pagination) · enum·에러코드 · 권한·쿠키/세션 · 미구현/거절/대안 한 줄 |
| 넣지 말 것 | 화면·컴포넌트·라우팅·React Query·`apis.js` 호출·UX 카피 등 **화면 코드** |
| 채널 | `@웹 화면 개발자` + **프론트 가이드 경로**만 (채팅만 스펙 ❌) |
| 요청서 「관련」 | **프론트 가이드 경로 1개** 필수 |
| FE SSOT | `apiPaths` 반영 기준 = **프론트 가이드만** (`*_BACKEND_REPLY.md`·채널 본문으로 계약 종료 금지) |
| 예외 | 운영 API만 / 기획 요결로 FE·BE **동시 지시** → 요청서 생략 가능(공통 기준 문서 경로만 채널 공유) |

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
| FE 회신 | 분량 무관 **프론트 가이드**만 (`docs/frontend-guide/`). 채팅·`*_BACKEND_REPLY`만으로 계약 종료 금지. 화면 코드 금지 |
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

## 실패 응답 (CommonResponse)

`CustomException` → `GlobalExceptionHandler` → 아래 키만. `message`/`data` 필드 **금지**.

```json
{
  "success": false,
  "successMessage": null,
  "errorMessage": "유효하지 않은 리프레시 토큰입니다. 다시 로그인해주세요",
  "errorCode": "E2003",
  "content": null
}
```

허용 키: `success` · `successMessage` · `errorMessage` · `errorCode` · `content` 만.
FE는 `errorCode`/`errorMessage`(및 방어적 `data.*` fallback)로 파싱한다. BE는 위 키를 SSOT로 고정.

## 완료 전 검증 체크리스트

도메인·API 작업 종료 전에 확인한다. (새 도메인 체크리스트와 함께 사용)

```
- [ ] API 접두사·권한(/api|/api-guest|/api-logined|/api-business|/api-admin)이 맞다
- [ ] FE 요청서 수신 시 path·enum·요청/응답 JSON을 확정하고 **프론트 가이드**(`docs/frontend-guide/…-frontend-guide.md`)로 전달했다 (채널·`*_BACKEND_REPLY`만으로 계약 종료 금지 · 미확정 path로 구현 시작 금지 · 화면 코드 미포함)
- [ ] 네이밍 공식 {D}{A}Controller / {D}{S}Service / {D}Repository* / {d}_module 준수
- [ ] 목록 경로는 /list, 페이징 키는 {item}List + PageResponseUtil
- [ ] 실패 응답은 success/errorCode/errorMessage/content/successMessage만 (message/data 금지)
- [ ] Service는 Entity만 반환, DTO 변환은 Controller 인라인
- [ ] QueryDSL만 사용 (JPQL 금지), Aggregate Root Repository만
- [ ] Allman·4칸·FQCN 금지·미사용 import 없음
- [ ] application*.properties를 AI 세션에서 편집하지 않음 (config/local/)
- [ ] Refresh 만료 시 재발급·sliding session 없음 (E2003 + 401만)
- [ ] 핵심 경로 테스트 또는 리뷰 메모가 있다
```

상세: [naming-formula.md](references/naming-formula.md) · [api-conventions.md](references/api-conventions.md) · [business-layer.md](references/business-layer.md) · [core-standards.md](references/core-standards.md) · [project-setup.md](references/project-setup.md)

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
