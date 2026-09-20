---
name: culwonder-leeds-profile-frontend
description: >-
  CulWonder(컬원더) Leeds Profile Next.js 16 App Router 프론트엔드 규칙.
  apis.js·apiPaths 중앙화, Atomic Design, 컴포넌트 네이밍 {Scope?}{Role}{Variant?},
  Redux Toolkit, React Query, 이미지 보호, 모바일 카피 줄바꿈(텍스트 UX),
  컴포넌트 카탈로그, 자사 백엔드 미작성 범위, docs/backend-request 백엔드 요청서,
  DESIGN.md(디자인.md)는 google-labs-code/design.md 스펙 참조,
  Git 커밋 메시지 한국어 작성, 작업 완료 후 사용자 확인 가이드.
  leeds_profile_next, 리즈프로필, CulWonder 프론트 UI·API·컴포넌트 작업 시 사용.
  Use whenever the user asks to build, change, review, or debug Leeds Profile /
  CulWonder Next.js pages, components, Redux, React Query, apis.js/apiPaths,
  backend-request docs, DESIGN.md, Korean commit messages, or mobile copy —
  even if they do not name this skill.
---

# CulWonder Leeds Profile — 프론트엔드 에이전트 스킬

CulWonder `leeds_profile_next` 레포에서 UI·상태·API 연동을 할 때 이 스킬을 따른다.
인증 상세 흐름은 `auth-frontend-backend-flow` 스킬을 함께 본다.

> **읽는 순서:** 비협상 규칙 → 워크플로 → 필요 시 `references/`만 연다. SKILL 본문은 요약·강제 규칙, 상세·예시·템플릿은 references.

## 언제 사용하는가

- 새 페이지·컴포넌트·훅·Redux slice 추가/수정
- 백엔드 API 연동, React Query 쿼리 작성
- food / shoot 멀티 도메인, 프로필·주문·어드민 화면
- 로딩·빈 상태·페이지네이션 UI
- **DESIGN.md / 디자인.md** 작성·갱신 — [google-labs-code/design.md](https://github.com/google-labs-code/design.md) 스펙 참조 (§1-5)
- skills.sh 설치: `npx skills add <owner/repo>@culwonder-leeds-profile-frontend`

## 비협상 규칙 (반드시 지킬 것)

### 1. 작업 범위

- **자사(인하우스) 백엔드 코드는 작성·수정하지 않는다.** (서버, DB 마이그레이션, Spring 구현 등)
- 프론트에서 **기존 API를 호출**하는 코드(`apis.js`, `apiPaths.js`, 페이지/훅)는 작성 가능
- 백엔드 스키마·엔드포인트 변경이 필요하면 **코드 수정 없이** 요청사항만 정리해 전달 (업무 요구·UI 필요 데이터·현재 상황 — **URL·요청·응답 JSON은 백엔드가 정의 후 회신**)

### 1-1. 백엔드 요청 — 가이드만 따를 것

백엔드에 추가·수정을 요청할 때는 **아래 가이드만** 따른다. 가이드 밖 형식·임의 스펙·口頭/채팅만 전달은 하지 않는다.

| 해야 할 것 | 금지 |
|-----------|------|
| **작성 양식**은 하네스 템플릿(`backend-request.md`) + [references/api.md](references/api.md) **「백엔드 요청 가이드」** 세부 | 가이드·하네스와 다른 형식으로 요청서 작성 |
| `docs/backend-request/{YYYYMMDD}_{HHmmss}_{기능명}_BACKEND_REQUEST.md`에 요청서 저장 (§1-2·가이드 §문서 템플릿) | `api-docs.yaml`, Spring/Java, DB 마이그레이션 직접 수정 |
| **업무·UI 관점** 요구(기능, 화면, 필요 데이터, 검증 의도, 현재 상황) 명시 | **URL(path)·요청값·응답값을 프론트가 확정**해 백엔드에 지시 |
| 변경·추가 요청은 **AS-IS / TO-BE** 로 현재 vs 목표를 구분해 표현 | 변경점을 줄글·목록만 나열 (전후 구분 없음) |
| 요청서에 **「백엔드 확정·회신 요청」** 절 포함 — path·request·response는 백엔드 설계 후 알려 달라고 명시 | 백엔드 미구현 API를 프론트에서 mock·우회로 **영구 대체** |
| 기존 **이미 운영 중인** API는 `apiPaths.js`·실제 응답 기준으로 기술 | CulWonder 관례와 다른 path를 프론트가 임의 제안·고정 |

→ **양식 SSOT:** 하네스 `docs/FE_BE_DOC_HANDOFF.md` · `templates/backend-request.md`  
→ **제품 세부:** **[references/api.md](references/api.md) §백엔드 요청 가이드**

### 1-2. 문서 작성 — 파일명 규칙 (필수)

프론트 가이드, 백엔드 요청서, 검증·협업 Markdown 등 **레포에 새로 저장하는 문서**는 파일명 **맨 앞**에 작성 시점 **날짜·시분초**를 붙인다.

```
{YYYYMMDD}_{HHmmss}_{기능명}_{문서유형}.md
```

| 부분 | 형식 | 예 |
|------|------|-----|
| 날짜 | `YYYYMMDD` | `20260621` |
| 시분초 | `_` + `HHmmss` (24시간, 0 패딩) | `_212537` |
| 기능명 | `UPPER_SNAKE_CASE`, 도메인·기능 식별 | `BUSINESS_ORDER_MANAGEMENT` |
| 문서유형 | 용도 접미 | `FRONTEND_GUIDE`, `BACKEND_REQUEST`, `FRONTEND_VERIFICATION_REQUEST` |

**저장 위치**

| 용도 | 폴더 | 예시 |
|------|------|------|
| 백엔드 API 요청 | `docs/backend-request/` | `docs/backend-request/20260614_143052_VOC_CUSTOMER_BACKEND_REQUEST.md` |
| 프론트 가이드·검증·협업 | `docs/` | `docs/20260621_212537_BUSINESS_ORDER_MANAGEMENT_FRONTEND_GUIDE.md` |

**규칙**

- 타임스탬프는 **문서를 처음 작성하는 시점** 기준 (생성 직전 `date +%Y%m%d_%H%M%S` 등으로 확인)
- 내용 **갱신** 시 덮어쓰기보다 **새 타임스탬프 파일** 생성 권장 — 이전 문은 본문 `관련 문서`에 링크
- ❌ `business-order-management-frontend-guide.md` 처럼 **날짜·시분초 없는** 신규 파일명
- **`docs/backend-request/` 보관**: `*_BACKEND_REQUEST.md`는 **최신 10개만** 유지 (`README.md` 제외). 새 요청서 저장 직후 파일명 `{YYYYMMDD}_{HHmmss}` 기준 내림차순 정렬 → 11번째부터 삭제

→ 작성 양식: 하네스 `templates/backend-request.md` · 세부·AS-IS/TO-BE: **[references/api.md](references/api.md) §백엔드 요청 가이드**

### 1-3. 프론트 요구사항 처리 중 — 백엔드 요청서 작성 (필수)

프론트 UI·연동 작업을 **진행·완료하는 동안**, 아래에 해당하면 **같은 작업 흐름 안에서** `docs/backend-request/`에 요청서를 **반드시** 만든다. 채팅으로만 전달하거나 나중에 미루지 않는다.

**요청서가 필요한 경우 (하나라도 해당)**

| 상황 | 예 |
|------|-----|
| **신규 API** | `apiPaths.js`에 없는 엔드포인트, 404·미구현 |
| **기존 API 필드 부족** | UI에 필요한 값이 응답에 없음 |
| **요청 body·query 변경** | 필터·정렬·페이지네이션·상태 변경 등 백엔드 저장 필요 |
| **스키마·enum·검증** | 새 status, 타입, 사업자/주문 상태 등 서버 정의 필요 |
| **업무 규칙** | 정산·환불·승인 등 **서버만** 처리 가능한 로직 |

**요청서 없이 프론트만 처리 가능한 경우**

- copy·레이아웃·스타일만 변경
- **이미 운영 중인 API**로 UI 요구를 전부 충족
- Next API Route만 추가하고 Spring 변경이 없는 경우 (해당 시 요청서 불필요 — Spring 연동이 필요해지면 §1-3 적용)

**에이전트 동작 (순서)**

1. 요구사항 분석 → 백엔드 필요 여부 판단
2. 필요 시 **프론트 코드(mock·TODO)보다 먼저 또는 병행**하여 `docs/backend-request/{YYYYMMDD}_{HHmmss}_{기능명}_BACKEND_REQUEST.md` 작성 ([api.md](references/api.md) 템플릿·AS-IS/TO-BE·§백엔드 확정·회신 요청)
3. 프론트는 **회신 대기** 또는 **임시 UI만** — mock·localStorage **영구 대체 금지** (§1-1)
4. 작업 완료 답변에 **요청서 경로** 명시 (예: `docs/backend-request/20260627_153045_ORDER_FILTER_BACKEND_REQUEST.md`)

→ 폴더 안내: 제품 레포(`leeds_profile_next`)의 `docs/backend-request/README.md` (agent-skills 레포에는 해당 경로 없음)

### 1-4. Git 커밋 메시지 — 한국어 (필수)

사용자가 커밋을 요청하면 **제목·본문 모두 한국어**로 작성한다. 영문 전용 subject/body는 쓰지 않는다.

**형식**

```
{type}({scope}): {한국어 제목}

{한국어 본문 — why 중심, 1~2문장}
```

| 부분 | 언어 | 규칙 |
|------|------|------|
| `type` | 영어 | Conventional Commits — `feat` / `fix` / `chore` / `refactor` / `docs` / `style` / `test` 등 |
| `scope` | 영어 | 선택. 도메인·영역 — `writer`, `helper`, `ui`, `menu`, `seo` 등. 복수면 `ui,seo` |
| 제목 | **한국어** | 한 줄. **why** 중심. 마침표 없음. 변경 나열이 아니라 의도·효과 |
| 본문 | **한국어** | 필요 시. 무엇을 바꿨는지보다 **왜** 바꿨는지. HEREDOC으로 전달 |

**규칙**

- ❌ `Fix writer location display` 처럼 **제목·본문을 영어만** 작성
- ❌ `update files`, `fix bug` 처럼 모호한 영문·한글 혼용 제목
- ✅ `type(scope):` 접두만 영어, **설명은 한국어**
- 사용자 커밋 규칙(HEREDOC, secrets 제외, amend/push 조건)과 함께 적용

**예시**

```
fix(ui): 작가 상세 위치 표시와 가이드 배너 이미지 레이아웃 보완

소품이 없어도 위치가 보이도록 하고, DomainGuideBanner·StudioPlaceCard 이미지/링크 레이아웃을 안정화한다.
```

```
feat(writer,menu): 시그니처 메뉴·작가 상세 UX와 검색 필터 연동

사업자 메뉴 시그니처 토글/소비자 표시를 추가하고, 작가 상세 탭·갤러리·스크롤을 정리하며 대표 메뉴 필터를 반영한다.
```

```
chore(helper): 운동 가이드 일러스트 원본 이미지 8종 추가

CDN 업로드용 운동 자세 일러스트 소스 파일을 레포에 보관한다.
```

### 1-5. DESIGN.md (디자인.md) — Google Labs 스펙 참조 (필수)

시각 정체성·디자인 시스템을 에이전트용으로 문서화할 때 **`DESIGN.md` / 디자인.md**를 작성한다. **임의 형식·자유 산문만** 쓰지 말고, 아래 스펙을 **반드시 참조**한다.

| 해야 할 것 | 금지 |
|-----------|------|
| **[google-labs-code/design.md](https://github.com/google-labs-code/design.md)** 포맷·스펙을 읽고 준수 | 스펙과 무관한 자유 형식만으로 디자인 문서 작성 |
| YAML front matter(토큰) + Markdown body(근거·적용 방식) 구조 | 토큰 없이 prose만, 또는 prose 없이 토큰만 (스펙 의도 무시) |
| 섹션 순서: Overview → Colors → Typography → Layout → Elevation → Shapes → Components → Do's and Don'ts | 임의 섹션 순서·스펙에 없는 핵심 구조로 대체 |
| 기존 CulWonder UI(색·타이포·라운드·간격)와 **충돌하지 않게** 토큰 반영 | 레포 디자인 시스템과 무관한 새 팔레트·폰트를 스펙 없이 도입 |

**참조 (작성 전 확인)**

1. 저장소·개요: https://github.com/google-labs-code/design.md  
2. 스펙 전문: 저장소 `docs/spec.md` (또는 `npx @google/design.md spec`)  
3. 작성 후 검증(선택): `npx @google/design.md lint DESIGN.md`

**파일명·위치**

- 표준 파일명: **`DESIGN.md`** (Google Labs 관례). 한국어로 부를 때는 「디자인.md」와 동일 문서를 가리킨다.
- 레포 루트 또는 해당 도메인/기능 범위의 디자인 시스템 문서 경로에 둔다.
- §1-2 날짜 접두 문서(`docs/{YYYYMMDD}_…`)와 **별개** — DESIGN.md는 에이전트용 **지속(persistent) 디자인 시스템** 문서이므로 날짜 접두를 붙이지 않는다. 스냅샷·협업용이면 §1-2로 `docs/`에 별도 복사본을 둘 수 있다.

**에이전트 동작**

1. DESIGN.md / 디자인.md **작성·갱신 요청**이면 먼저 https://github.com/google-labs-code/design.md 를 참조
2. YAML 토큰 + `##` 섹션(스펙 순서)으로 초안 작성 — CulWonder 기존 토큰·컴포넌트와 맞춤
3. 가능하면 `npx @google/design.md lint`로 구조·참조·대비 검증 후 저장

### 2. API 통신

| 허용 | 금지 |
|------|------|
| `import api from '@/utils/apis'` | `axios` / `fetch` 직접 사용 |
| `api.get/post/patch/delete({ uri, path, ... })` | URL·path 하드코딩 |
| `apiPaths.js`의 상수·함수 + `getBackendUri()` | `storeApi.js` 등 별도 HTTP 유틸 신규 생성 |

- 모든 메서드 **첫 인자는 객체**: `{ uri, path, params?, data? }`
- `withCredentials: true` (쿠키 인증) — `apis.js` 기본값 유지
- 새 엔드포인트 → **먼저** `src/utils/apiPaths.js`에 추가 후 호출

```javascript
import api from '@/utils/apis';
import { users, getBackendUri } from '@/utils/apiPaths';

const result = await api.get({
  uri: getBackendUri(),
  path: users.profile,
  params: {},
});
```

- 401 + `errorCode === 'E2003'`: `apis.js`가 storage 정리 + backend `auth.logout` 후 **`/session-expired?redirect=...`** (`buildSessionExpiredUrl`; 이미 `/login` 또는 `/session-expired`면 무한 리다이렉트 금지). 실코드 SSOT — `auth-frontend-backend-flow` 스킬 참고

### 3. import 경로

- **`@/` 절대경로만** 사용 (`jsconfig.json`: `@/*` → `./src/*`)
- ❌ `./`, `../` 상대경로 import

### 4. React import

- `from 'react'` named import는 **심볼당 한 줄**, 같은 파일 내 **알파벳 순**

```javascript
import { useEffect } from 'react';
import { useState } from 'react';
```

### 5. 이미지 보호

- 노출 이미지: 드래그·우클릭 저장 방지 (전역 `globals.css` + `ImageProtection`)
- **우선** `@/components/atom/ProtectedImage` 사용
- 업로드/편집 UI만 `protect={false}` 예외

### 6. 기술 스택 전제

- **JavaScript only** (TypeScript 미사용)
- Next.js 16 App Router, React 19, Tailwind, shadcn/ui
- 서버 상태: React Query / 클라이언트 필터·UI 상태: Redux Toolkit + Persist

### 7. 화면 전용 콘텐츠 (copy·정적 데이터)

카피·탭·가이드 본문 등 **화면 전용 정적 데이터는 해당 라우트 `page.js` 파일 안에만** 둔다. `*Data.js`·`*Content.js`·형제 분리 파일 금지. 다른 라우트 공유는 원본 `page.js` named export. 여러 라우트 공유 config만 `components/page/{domain}/`.

상세·허용/금지 표·예시 코드 → **[references/page-content.md](references/page-content.md)**

---

## 에이전트 워크플로우

새 기능·프론트 요구사항 처리 시 순서:

1. **범위·백엔드 필요 여부** — §1-3 체크. 필요하면 **`docs/backend-request/`에 요청서 먼저(또는 병행) 작성** (§1-1·§1-2·[api.md](references/api.md)). URL·요청·응답은 **백엔드 확정·회신**. 프론트 가이드는 `docs/{YYYYMMDD}_{HHmmss}_{기능명}_FRONTEND_GUIDE.md`
2. **경로·API** — 기존 API면 `apiPaths.js` → `apis.js` 호출. **미구현 API는 요청서 작성 후** 회신 스펙 반영 (Same-origin Next route vs `getBackendUri()` 구분, auth 스킬 참고)
3. **파일 위치** — Atomic Design + `page.js`. **화면 copy·정적 데이터는 `page.js` 파일 내부에만** (§7, `*Data.js`·형제 분리 파일 금지)
4. **컴포넌트 이름** — `{Scope?}{Role}{Variant?}` ([component-naming.md](references/component-naming.md))
5. **상태** — 서버 데이터는 React Query, 필터/검색 UI 상태는 Redux
6. **컴포넌트 재사용** — [component-catalog-core.md](references/component-catalog-core.md) 확인 후 기존 컴포넌트 우선 사용
7. **UI** — 로딩·빈 상태·페이지네이션 규칙 적용, 이미지는 `ProtectedImage`, 목록은 `CustomPagination`
8. **텍스트 UX** — 긴 카피는 §7·[page-content.md](references/page-content.md)·[components-ui.md](references/components-ui.md) §텍스트 UX
9. **import** — `@/` + react import 스타일 검사
10. **사용자 확인 가이드** — 작업 완료 답변에 **반드시** 확인 절 포함 ([verification-guide.md](references/verification-guide.md) 템플릿·체크리스트)

---

## 작업 완료 후 — 사용자 확인 가이드 (필수)

프론트 작업을 **끝낼 때마다** 답변 마지막에 확인 방법을 적는다. 코드 diff만 전달하고 끝내지 않는다.

에이전트가 반드시 적을 절: **변경 요약** · **백엔드 요청서**(해당 시 경로, 없으면 「해당 없음」) · **로컬 확인**(명령·전체 URL·단계) · **테스트/운영**(해당 시) · **정상 기준** · **문제 시**.

| 목적 | 명령 | env |
|------|------|-----|
| 일반 로컬 개발 | `npm run dev` | `.env.development` |
| 테스트 설정 검증 | `npm run build:test` → `npm run start:test` | `.env.test` |
| 운영 | `npm run build` → `npm start` | `.env.production` |

- ❌ `build:test` 후 `npm start` 만 — `.env.production` 적용됨

템플릿·유형별 체크리스트·curl → **[references/verification-guide.md](references/verification-guide.md)**

---

## 폴더·파일 배치

### App Router (`src/app/`)

| 경로 | 용도 |
|------|------|
| `(ui)/[domain]/` | food, shoot 등 동적 도메인 |
| `(ui)/food/`, `(ui)/shoot/` | 도메인별 페이지 |
| `(ui)/login`, `signup`, `session-expired` | 인증 |
| `api/auth/`, `api/address/` 등 | Next API Routes (프록시·쿠키·OAuth 등) |
| `providers.js` | QueryClient → Redux → PersistGate → ImageProtection, Toast |

**멀티 도메인**: `[domain]/layout.js` → `DomainLayoutClient.jsx` (Topbar, Header, Footer, 도메인 Context)

**Shoot 상세**: `shoot/detail/{type}/[storeId]` — type: `hair-makeup`, `photo-studio`, `shoot-place`, `tanning`, `waxing`

### 컴포넌트 (Atomic Design)

논리 순서: `ui` → `atom` → `molecule` → `organism` → `page` → `auth` → `helper` → `etc`

| 폴더 | 역할 |
|------|------|
| `ui/` | shadcn/ui만 |
| `atom/` | 최소 단위 (Modal, ProtectedImage, ImageUploader) |
| `molecule/` | atom 조합 (ListingCard, CustomPagination) |
| `organism/` | 섹션·복합 UI (HomeHeader, SearchContent) |
| `page/` | 페이지 조립·**여러 라우트 공유** config (`page/helper/styleBySlug.js` 등) |
| `auth/` | ProtectedRoute, LinkGuard, 로그인 가드 |
| `helper/` | helper 도메인 Provider·동기화·레거시 마이그레이션 |

- 파일명: `PascalCase.jsx`, 스토리: `{Name}.stories.jsx`
- shadcn: `cn()` from `@/lib/utils.js`

### 컴포넌트 네이밍 — `{Scope?}{Role}{Variant?}`

| 부분 | 필수 | 의미 | 예 |
|------|------|------|-----|
| Scope | 선택 | 도메인·기능 | `Food`, `Shoot`, `Helper`, `MealCalculator` |
| Role | **필수** | UI·행동 | `Card`, `Filter`, `Content`, `Modal`, `Section` |
| Variant | 선택 | 형태·단계 | `Swipe`, `Floating`, `Step3`, `SideBar` |

- **Core** (Scope 없음): 2개 이상 도메인 재사용 — `Modal`, `ListingCard`, `SearchContent`
- **Domain** (`{Scope}{Role}`): 도메인 로직·API·UX가 실질적으로 다름 — `FoodListingCard`
- **Feature** (`{Feature}{Variant?}{Role}`): 단일 플로우 전용 — `MealCalculatorStep3Modal`

Scope 없이 이름 짓고, 도메인 차이는 `page/{domain}/*.js` config 또는 `{layer}/{domain}/` 폴더로 분리.

상세·결정 트리·금지 패턴 → [component-naming.md](references/component-naming.md)

### 기타 `src/`

| 경로 | 용도 |
|------|------|
| `hooks/useAuth.js` 등 | 커스텀 훅 |
| `redux/store.js`, `reducers/` | Redux |
| `utils/apis.js`, `apiPaths.js` | HTTP·경로 |
| `utils/format/` | 금액·전화·사업자번호 포맷 |

**파일 생성 체크리스트**

- 페이지: `src/app/(ui)/{route}/page.js` — **데이터·copy·컴포넌트를 같은 파일에**
- API Route: `src/app/api/{route}/route.js`
- Reducer: `{domain}Reducer.js` → `rootReducer.js` 등록, persist 필요 시 `commonReducerPersist` + `whitelist`
- 컴포넌트: 복잡도에 맞는 atomic 폴더
- ❌ 단일 화면용 `*Data.js`, `*Content.js`, 라우트 폴더 형제 데이터 파일 (§7)

---

## Redux 규칙

- `createSlice` only (`@reduxjs/toolkit`)
- 파일명: `{domain}Reducer.js`, slice `name`: camelCase
- 액션: `set*`, `toggle*`, `clear*`, `clearAllFilters`
- 영구 저장: `commonReducerPersist` + `store.js` `persistConfig.whitelist`
- 컴포넌트: `useSelector` / `useDispatch`, action은 reducer에서 export

---

## 인증·권한 (요약)

| 항목 | 위치 |
|------|------|
| 역할 | `USER_ROLE` in `ProtectedRoute.jsx` — GUEST, USER, BUSINESS, ADMIN |
| 페이지 가드 | `ProtectedRoute` |
| 링크/클릭 가드 | `LinkGuardLoginRequest`, `GuardLoginRequest`, `onClickGuardLoginRequest` |
| 로그인 상태 | `useAuth` — React Query `['auth','status']`, 프로필 `users.profile` |

상세 OAuth·쿠키·same-origin vs backend → **`auth-frontend-backend-flow`** 스킬.

---

## 컴포넌트 재사용 (새로 만들기 전에 확인)

| 문서 | 용도 |
|------|------|
| [component-catalog-core.md](references/component-catalog-core.md) | 전역 필수 — Modal, ImageUploader, CustomPagination, auth guard, layout shell |
| [component-catalog-food.md](references/component-catalog-food.md) | food·장바구니·식단계산기 |
| [component-catalog-shoot.md](references/component-catalog-shoot.md) | shoot·스튜디오·촬영장 |
| [component-catalog-helper.md](references/component-catalog-helper.md) | helper·플랜·포즈·스타일 퀴즈 |
| [component-catalog-profile.md](references/component-catalog-profile.md) | profile·props·admin·입점 폼 |

**비협상**
- ❌ `Modal`, `ImageUploader`, `CustomPagination`, `ProtectedRoute` 대체 구현
- ❌ 목록 페이지네이션을 페이지마다 새로 작성 — `CustomPagination` 사용
- Modal(비즈니스·업로드) vs shadcn `Dialog`(가벼운 UI) 구분 → core catalog 참고

---

## UI 패턴

### 로딩·빈 상태

- 아이콘 자리(60×60)에 **동일 레이아웃**으로 스피너
- 스피너: `w-[60px] h-[60px] border-4 border-[#FEC104] border-t-transparent rounded-full animate-spin`
- 빈 상태와 동일한 `py-[100px] flex flex-col items-center justify-center gap-y-[12px]`

### 페이지네이션 (전 목록 공통)

- 0-based index, 화면 표시는 +1
- `maxVisible = 5`, 생략은 `'ellipsis'`
- 앞(0~2): `1 2 3 4 5 ... last` / 중간: `1 ... cur±2 ... last` / 뒤: `1 ... last5`
- total 1페이지여도 `1` 표시, `total > 0`일 때만 노출
- 페이지 클릭 시 **스크롤 상단 이동 없음**

상세 알고리즘: [references/components-ui.md](references/components-ui.md)

### 스토어 PATCH 패턴

- 서비스/이미지 변경: `PATCH /api-logined/store/{storeId}`
- GET 현재 상태 → 배열 수정 → PATCH 전체 교체 (별도 storeApi.js 금지)

### Context

- `FoodFavoriteOverridesContext`: 동일 상품 다중 노출 시 하트 상태 동기화 (`productId` / `foodId` key)

### CKEditor

- `dynamic(..., { ssr: false })` — 클라이언트 전용

### 텍스트 UX (모바일 카피 줄바꿈)

긴 카피는 모바일에서 **의미 단위**로 끊는다. `<br className="sm:hidden" />` 만 사용, 카피 상수는 **`page.js` 내부**(§7). 고정 높이·문구 이중 작성·중간 단어 끊기 금지.

상세·도메인별 기준·예시 → [references/components-ui.md](references/components-ui.md) §텍스트 UX

---


---

## Provider 순서

```
QueryClientProvider → Redux Provider → PersistGate → ImageProtection → ToastContainer
```

---

## 상세 참고 문서

레포 내 Cursor rules와 동기화된 참고:

| 주제 | 파일 |
|------|------|
| API·React Query·**백엔드 요청 가이드 (필수)** | [references/api.md](references/api.md) §백엔드 요청 가이드 |
| 로딩·페이지네이션·**텍스트 UX**·UI 패턴 | [references/components-ui.md](references/components-ui.md) |
| 컴포넌트 카탈로그 (core) | [references/component-catalog-core.md](references/component-catalog-core.md) |
| 컴포넌트 카탈로그 (food/shoot/helper/profile) | `references/component-catalog-*.md` |
| 컴포넌트 네이밍 | [references/component-naming.md](references/component-naming.md) |
| 폴더 트리·아키텍처·체크리스트 | [references/architecture.md](references/architecture.md) |
| **작업 완료 후 사용자 확인 가이드** | [references/verification-guide.md](references/verification-guide.md) |
| **문서 파일명 (날짜·시분초)** | SKILL.md **§1-2** — `docs/backend-request/`·`docs/` |
| **프론트 작업 중 백엔드 요청** | SKILL.md **§1-3** — `docs/backend-request/` 요청서 필수 |
| **Git 커밋 메시지 (한국어)** | SKILL.md **§1-4** — `type(scope):` + 한국어 제목·본문 |
| **DESIGN.md / 디자인.md** | SKILL.md **§1-5** — **[google-labs-code/design.md](https://github.com/google-labs-code/design.md)** 스펙 필수 참조 |
| 화면 전용 copy·정적 데이터 배치 | [references/page-content.md](references/page-content.md) (SKILL §7 요약) |

원본 전체 규칙: 레포 `.cursor/rules/` (`api.md`, `components.md`, `folder-structure.md`, `page-content.md` 등)

---


## 완료 전 검증 체크리스트

작업 종료 전에 해당 항목을 확인한다.

```
- [ ] 백엔드 Java/DB/마이그레이션을 이 레포에서 수정하지 않았다
- [ ] 신규/변경 API면 docs/backend-request/{YYYYMMDD}_{HHmmss}_…_BACKEND_REQUEST.md 작성·경로 답변 포함
- [ ] HTTP는 apis.js + apiPaths.js만 (axios/fetch·URL 하드코딩 없음)
- [ ] 401 E2003 처리는 `/session-expired?redirect=...` (buildSessionExpiredUrl; login 직행 금지)
- [ ] import는 @/ 절대경로, react named import는 심볼당 한 줄·알파벳 순
- [ ] 화면 copy·정적 데이터는 page.js 내부 (page-content.md)
- [ ] 목록은 CustomPagination, 이미지는 ProtectedImage 우선
- [ ] 컴포넌트 카탈로그에서 기존 재사용 후보를 확인했다
- [ ] 답변에 사용자 확인 가이드(verification-guide.md)를 포함했다
- [ ] 커밋 요청 시 type(scope): + 한국어 제목·본문
```

---

## skills.sh 배포 구조

공개 저장소에 올릴 때 권장 레이아웃:

```
culwonder-agent-skills/
└── skills/
    └── culwonder-leeds-profile-frontend/
        ├── SKILL.md
        ├── evals/
        │   └── evals.json
        └── references/
```

설치: `npx skills add CulWonder/culwonder-agent-skills@culwonder-leeds-profile-frontend`
