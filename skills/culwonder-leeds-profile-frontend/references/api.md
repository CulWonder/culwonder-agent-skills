# API 통신 참고 (Leeds Profile Next)

## 단일 진입점

- `src/utils/apis.js` — `api.get | post | patch | delete`
- `src/utils/apiPaths.js` — path 상수·함수, `getBackendUri()` → `NEXT_PUBLIC_BACKEND_URL`

## 호출 형태

```javascript
// GET
await api.get({ uri: getBackendUri(), path: users.profile, params: {} });

// POST
await api.post({ uri: getBackendUri(), path: auth.tokens, data: { provider, code } });

// PATCH / DELETE — 동일하게 객체 첫 인자
```

두 번째 인자(선택): `{ timeout, headers, ... }`

## React Query

```javascript
useQuery({
  queryKey: ['users', 'profile'],
  queryFn: () => api.get({ uri: getBackendUri(), path: users.profile, params: {} }),
});
```

- queryKey는 도메인별로 일관되게 (`useAuth`는 `['auth','status']` 등 기존 패턴 따름)

## apiPaths 도메인 예

`auth`, `sms`, `users`, `store`, `storeLogined`, `ecommerce`, `ecommerceLogined`, `admin`, `business` — 동적 path는 함수: `storeLogined.one(storeId)`

## Same-origin vs Backend

| 대상 | base | 예 |
|------|------|-----|
| Next API Route | `window.location.origin` 또는 path만 | `/api/auth/status` |
| Spring API | `getBackendUri()` | `/api-guest/auth/tokens` |

→ 인증·로그아웃 시 어느 쪽인지 먼저 확인 (`auth-frontend-backend-flow`).

## 에러 처리

- 실패 시 `Promise.reject`, `error.message` 활용
- try/catch 또는 React Query `onError`에서 UI 처리

---

## 백엔드 요청 가이드

> **에이전트·개발자 공통:** 백엔드에 API·스키마 변경을 요청할 때는 **이 절만** 따른다.  
> SKILL.md §1-1·§1-3과 동일. **저장 위치:** `docs/backend-request/`

### 작성 양식 SSOT (하네스 템플릿)

| 구분 | 역할 |
|------|------|
| **작성 양식(템플릿)·init 배포** | 하네스 `harness_culwonder` — `docs/FE_BE_DOC_HANDOFF.md` · `skills/culwonder/references/templates/backend-request.md` · `backend-request-reply.md` · `frontend-guide.md`(BE→FE **프론트 가이드**) |
| **제품 작성 세부(이 절)** | 언제 쓰는지 · AS-IS/TO-BE · path FE 미확정 · 체크리스트 · Leeds 관례 |

요청서 Markdown **본문 골격**은 하네스 `backend-request.md`를 복사해 채운다.  
이 절의 인라인 템플릿은 세부·예시용이며, 양식이 어긋나면 **하네스 템플릿을 우선**한다.

**프론트 가이드**(`frontend-guide.md`, BE→FE) = 백엔드가 프론트에게 전달하는 **API 계약만** (Method/Path·Request/Response·enum·에러).  
화면·컴포넌트·라우팅·React Query·호출 방식·UX 카피는 넣지 않는다 — 그건 FE **FRONTEND_GUIDE** / FE 자율.

회신 위치(짧음 채널 / 중간 `*_BACKEND_REPLY.md` / 김 **프론트 가이드**): 하네스 `FE_BE_DOC_HANDOFF.md` 「회신 파일 위치 · 짧/중/김」.  
파일 회신은 요청서 「관련」에 **경로 1개** 필수. 기획 요결로 FE·BE 동시 지시면 요청서 생략 가능.

### 프론트 요구사항 처리 중 — 언제 요청서를 쓰는가 (§1-3)

프론트 작업을 하다가 아래가 보이면 **즉시** `docs/backend-request/{YYYYMMDD}_{HHmmss}_{기능명}_BACKEND_REQUEST.md`를 작성한다.

| 트리거 | 조치 |
|--------|------|
| `apiPaths.js`에 path 없음 / API 404 | 요청서에 UI 필요 데이터·AS-IS/TO-BE 기술 |
| 응답 필드로 UI를 못 채움 | 부족 필드·화면 위치 명시, **회신 요청** |
| 필터·정렬·상태 변경이 서버 저장 | UI 동작 + 필요 API **역할** 기술 (path는 백엔드 확정) |
| enum·validation·업무 규칙 | 현재 UI vs 목표, 서버 정의 필요 항목 나열 |

순수 UI·copy 변경이거나 **운영 API로 충분**하면 요청서 생략. Spring·DB는 이 레포에서 수정하지 않는다.

### 원칙

1. **백엔드 코드·스키마 파일을 이 레포에서 수정하지 않는다.**
   - ❌ Spring/Java, DB 마이그레이션, 백엔드 `api-docs.yaml` 직접 수정
   - ❌ 백엔드 미구현을 프론트 mock·localStorage로 **영구** 대체 (임시 폴백은 문서에 명시 후 API 전환 전제)
2. **요청서만 작성** — `docs/backend-request/{YYYYMMDD}_{HHmmss}_{기능명}_BACKEND_REQUEST.md` (§문서 저장 위치·파일명)
3. **보관 개수** — `docs/backend-request/`의 `*_BACKEND_REQUEST.md`는 **최신 10개만** 유지 (`README.md` 제외). 새 요청서 저장 직후 파일명 타임스탬프 기준으로 11번째 이후 파일 삭제
4. **프론트는 「무엇이 필요한지」만**, **백엔드는 「URL·요청·응답」을 정한다**

### URL·요청·응답 — 백엔드 확정 (핵심)

**신규·미구현 API** 요청서에서는 path·query·body·response JSON을 **프론트가 확정하지 않는다.**

| 구분 | 프론트 요청서에 쓸 내용 | 백엔드가 정해서 **회신**할 내용 |
|------|------------------------|--------------------------------|
| URL | 필요한 **기능·역할** (접수, 목록, 수정 등) | `METHOD`, path, prefix (`/api`, `/api-logined`, `/api-admin` 등) |
| 요청값 | UI에서 **보내야 하는 정보** (유형, 내용, 현재 페이지 등) | 필드명, 타입, 필수 여부, validation, query/body 구조 |
| 응답값 | UI에 **표시·판단에 필요한 정보** (목록, 상태, 본인 여부 등) | 필드명, 타입, pagination shape, `CommonResponse` content 구조 |
| 에러 | UI에서 **어떻게 보여줄지** (toast, 빈 상태 등) | `errorCode`, HTTP status, `errorMessage` |

요청서 **필수 문구** (각 API 블록 또는 문서 하단 §백엔드 확정·회신 요청):

```markdown
## 백엔드 확정·회신 요청

아래 API의 **URL(path)·요청 스키마·응답 스키마·에러 코드**는 백엔드에서 설계·확정한 뒤
Swagger(또는 스펙 문서)와 함께 프론트에 알려 주세요.
프론트는 회신된 스펙에 맞춰 `apiPaths.js`·호출 코드를 연동합니다.
```

**예외 — 프론트가 path를 적어도 되는 경우**

- 이미 **운영 중**이고 `apiPaths.js`·실제 연동으로 검증된 API
- 백엔드가 **이미 문서화·회신**한 스펙을 요청서에서 **인용**하는 경우 (source 명시)

### 변경사항 — AS-IS / TO-BE (필수)

요청서 §0 또는 각 기능 절에서 **현재(AS-IS)** 와 **목표(TO-BE)** 를 반드시 구분한다.  
백엔드·프론트가 같은 문서로 diff를 공유하기 위함.

| 작성 규칙 | 설명 |
|-----------|------|
| **AS-IS** | 지금 상태 — 미구현·폴백·운영 API·UI 동작·enum·블로커 |
| **TO-BE** | 요청 후 기대 상태 — UI 목표·필요 API 역할·업무 결과 (path·JSON은 백엔드 회신) |
| 형식 | `### AS-IS` / `### TO-BE` 소제목 + 표·목록 (가능하면 표) |
| 신규 기능 | AS-IS = 「없음 / localStorage 폴백」 등, TO-BE = UI·업무 목표 |
| enum·필드 변경 | AS-IS 허용값 표 → TO-BE 허용값 표 + 매핑·비고 열 |
| API 변경 | AS-IS 동작·연동 상태 → TO-BE 필요 역할 (URL은 §백엔드 확정·회신) |

**예시** (AS-IS/TO-BE enum 변경):

```markdown
### AS-IS (현재)

| id | label |
|----|-------|
| `challenge` | 시작 (도전 선언) |
| … | … |

### TO-BE (변경 후)

| id | label | 비고 |
|----|-------|------|
| `study` | 공부 | 신규 |
| `free` | 자유 게시판 | **유지** |
```

신규 API만 있는 경우:

```markdown
### AS-IS

- 백엔드 VOC API 없음 → 프론트 localStorage 폴백

### TO-BE

- FAB 모달에서 접수·목록 조회가 서버 API로 동작
- (URL·스키마는 백엔드 확정·회신)
```

### 작성 전 확인

- [ ] 프론트 UI가 **어떤 데이터·동작**을 필요로 하는지 코드·화면에서 정리했는가
- [ ] 신규 API인가, 기존 운영 API인가 구분했는가 (후자만 path 인용)
- [ ] 동일 도메인 기존 요청서와 **업무 범위**가 겹치지 않는가
- [ ] 임시 폴백(localStorage 등)과 API 전환 조건을 명시했는가
- [ ] **AS-IS / TO-BE** 로 현재 vs 목표를 구분했는가

### 요청서에 넣을 내용 (체크리스트)

| 항목 | 설명 |
|------|------|
| 작성 목적 | 한 줄 — 무엇을 위해 API가 필요한지 |
| **AS-IS / TO-BE** | §0 또는 기능별 — 현재 상태 vs 목표 상태 (표 권장) |
| 프론트 조치 | 이미 구현된 UI·파일 경로 |
| 백엔드 필요 작업 | 번호 목록, **필수/권장** 구분 |
| UI → 필요 데이터 | 화면별 입력·표시·필터에 필요한 **정보**(필드명은 백엔드 확정) |
| UI 검증 의도 | 프론트 폼 규칙 (예: 내용 5자 이상) — 백엔드 validation 참고용 |
| **백엔드 확정·회신 요청** | URL·request·response·error **백엔드 설계 후 회신** 명시 |
| 완료 기준 | 백엔드 구현 + **스펙 회신** + 프론트 연동 QA |

path·request/response JSON은 **백엔드 회신 후** `apiPaths.js`·호출 코드에 반영한다.

### 문서 저장 위치·파일명

공통 규칙(SKILL.md **§1-2**): 파일명 **맨 앞** `{YYYYMMDD}_{HHmmss}_` (24시간, 0 패딩).

**백엔드 요청서** (`docs/backend-request/`):

```
docs/backend-request/{YYYYMMDD}_{HHmmss}_{기능명}_BACKEND_REQUEST.md
```

**프론트 가이드·검증·협업** (`docs/`):

```
docs/{YYYYMMDD}_{HHmmss}_{기능명}_FRONTEND_GUIDE.md
docs/{YYYYMMDD}_{HHmmss}_{기능명}_FRONTEND_VERIFICATION_REQUEST.md
```

| 규칙 | 설명 |
|------|------|
| **접두 날짜·시분초** | 작성 시점 `YYYYMMDD` + `_` + `HHmmss` |
| 기능명 | `{기능명}` — `UPPER_SNAKE_CASE` (예: `BUSINESS_ORDER_MANAGEMENT`, `VOC_CUSTOMER`) |
| 예시 (백엔드) | `docs/backend-request/20260614_143052_VOC_CUSTOMER_BACKEND_REQUEST.md` |
| 예시 (프론트) | `docs/20260621_212537_BUSINESS_ORDER_MANAGEMENT_FRONTEND_GUIDE.md` |
| 갱신 | 내용 변경 시 **새 타임스탬프 파일** 생성 권장 (이전 문서는 삭제하지 않고 `관련 문서`에 링크) |

관련 도메인 요청서가 있으면 **상단에 링크**. enum·업무 정의가 겹치면 source of truth 한쪽 명시.

### 문서 템플릿 (기능 API — 권장)

> **우선:** 하네스 `skills/culwonder/references/templates/backend-request.md`  
> 아래는 동일 규칙을 설명하는 예시 골격이다.

```markdown
# [기능명] — 백엔드 요청

> **작성 목적**: …
> **프론트 조치**: … (파일 경로)
> **백엔드 필요 작업**: 아래 (필수/권장)
> **관련 문서**: `docs/…` 또는 `docs/backend-request/…` (있으면)

---

## 0. 개요

### AS-IS (현재)

[지금 상태 — 미구현·폴백·운영 API·UI·enum]

### TO-BE (목표)

[요청 후 기대 — UI·업무 결과. URL·JSON은 백엔드 회신]

[임시 폴백, UI → 필요한 API 역할]

## 1. [기능] — 접수 (필수)

**프론트 UI:** … (`파일 경로`)

**보내야 하는 정보 (UI 관점):**
- …

**받아야 하는 정보 (UI 관점):**
- …

**UI 검증 (참고):**
- …

> URL·요청 body·응답 JSON·에러 코드 → **백엔드 확정 후 회신** (§N 참고)

## 2. [기능] — 목록 (필수)

(동일 형식)

## N. 백엔드 확정·회신 요청

아래 항목은 **백엔드에서 설계·확정**한 뒤 Swagger(또는 스펙 문서)와 함께 알려 주세요.

| # | API 역할 | 백엔드 회신 항목 |
|---|----------|------------------|
| 1 | 접수 | METHOD, path, request schema, response schema, error codes |
| 2 | 목록 | … |

프론트는 회신 스펙에 맞춰 `src/utils/apiPaths.js` 및 호출 코드를 연동합니다.

## N+1. 완료 기준

- [ ] 백엔드 API 구현
- [ ] **URL·요청·응답 스펙 회신** (Swagger 등)
- [ ] 프론트 `apiPaths.js` 연동 및 QA
```

### 짧은 템플릿 (필드 1~2개 추가 등 소규모)

```markdown
## 백엔드 요청사항

### [요청 제목]

**AS-IS:**
- …

**TO-BE:**
- …

**요청사항:**

1. **[엔티티/기능]** — UI에서 필요한 변경·데이터 설명
2. **URL·요청·응답** — 백엔드에서 설계·확정 후 회신 요청
```

### 금지 예시

| 상황 | ❌ 하지 말 것 | ✅ 할 것 |
|------|-------------|---------|
| 신규 API | `POST /api/voc` 등 path·JSON을 프론트가 **확정** | UI 필요 데이터 + **백엔드 회신 요청** |
| API 스키마 없음 | `api-docs.yaml` 직접 수정 | 요청서 + 백엔드 스펙 **회신 대기** |
| 백엔드 404 | Spring Controller 임의 작성 | `docs/backend-request/{YYYYMMDD}_{HHmmss}_*_BACKEND_REQUEST.md` |
| 스펙 불명확 | 「API 만들어 주세요」만 | AS-IS/TO-BE + 화면 요구 + 회신 요청 |
| 요청 형식 | Slack/口頭만 | 가이드 템플릿으로 `docs/backend-request/` 저장 |

### 레포 참고

| 문서 | 비고 |
|------|------|
| `docs/backend-request/{YYYYMMDD}_{HHmmss}_{기능명}_BACKEND_REQUEST.md` | 백엔드 요청서 — AS-IS/TO-BE·회신 요청·**§1-2 파일명** |
| `docs/{YYYYMMDD}_{HHmmss}_{기능명}_FRONTEND_GUIDE.md` 등 | 프론트 가이드·검증 — SKILL.md **§1-2** |

**신규·갱신 요청서**는 본 가이드(백엔드 URL·요청·응답 **회신**)를 따른다.

코드로 백엔드를 대체 구현하지 않는다.
