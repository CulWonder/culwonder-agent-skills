---
name: culwonder-leeds-profile-frontend
description: >-
  CulWonder(컬원더) Leeds Profile Next.js 16 App Router 프론트엔드 규칙.
  apis.js·apiPaths 중앙화, Atomic Design, 컴포넌트 네이밍 {Scope?}{Role}{Variant?},
  Redux Toolkit, React Query, 이미지 보호, 컴포넌트 카탈로그, 자사 백엔드 미작성 범위.
  leeds_profile_next, 리즈프로필, CulWonder 프론트 UI·API·컴포넌트 작업 시 사용.
---

# CulWonder Leeds Profile — 프론트엔드 에이전트 스킬

CulWonder `leeds_profile_next` 레포에서 UI·상태·API 연동을 할 때 이 스킬을 따른다.
인증 상세 흐름은 `auth-frontend-backend-flow` 스킬을 함께 본다.

## 언제 사용하는가

- 새 페이지·컴포넌트·훅·Redux slice 추가/수정
- 백엔드 API 연동, React Query 쿼리 작성
- food / shoot 멀티 도메인, 프로필·주문·어드민 화면
- 로딩·빈 상태·페이지네이션 UI
- skills.sh 설치: `npx skills add <owner/repo>@culwonder-leeds-profile-frontend`

## 비협상 규칙 (반드시 지킬 것)

### 1. 작업 범위

- **자사(인하우스) 백엔드 코드는 작성·수정하지 않는다.** (서버, DB 마이그레이션, Spring 구현 등)
- 프론트에서 **기존 API를 호출**하는 코드(`apis.js`, `apiPaths.js`, 페이지/훅)는 작성 가능
- 백엔드 스키마·엔드포인트 변경이 필요하면 **코드 수정 없이** 요청사항만 정리해 전달 (필드명, 타입, path, 요청/응답 예시)

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

- 401 + `errorCode === 'E2003'`: `apis.js`가 storage 정리 후 `/session-expired?redirect=...` (이미 해당 경로면 무한 리다이렉트 금지)

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

---

## 에이전트 워크플로우

새 기능 구현 시 순서:

1. **범위 확인** — 백엔드 구현이 필요한지? 필요하면 프론트만 스펙 문서화
2. **경로·API** — `apiPaths.js` → `apis.js` 호출 (Same-origin Next route vs `getBackendUri()` 구분, auth 스킬 참고)
3. **파일 위치** — Atomic Design 폴더 + `src/app/(ui)/.../page.js` 규칙
4. **컴포넌트 이름** — `{Scope?}{Role}{Variant?}` ([component-naming.md](references/component-naming.md))
5. **상태** — 서버 데이터는 React Query, 필터/검색 UI 상태는 Redux
6. **컴포넌트 재사용** — [component-catalog-core.md](references/component-catalog-core.md) 확인 후 기존 컴포넌트 우선 사용
7. **UI** — 로딩·빈 상태·페이지네이션 규칙 적용, 이미지는 `ProtectedImage`, 목록은 `CustomPagination`
8. **import** — `@/` + react import 스타일 검사

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
| `page/` | 페이지 조립·도메인 config (`page/helper/`, `page/food/` 등) |
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

- 페이지: `src/app/(ui)/{route}/page.js`
- API Route: `src/app/api/{route}/route.js`
- Reducer: `{domain}Reducer.js` → `rootReducer.js` 등록, persist 필요 시 `commonReducerPersist` + `whitelist`
- 컴포넌트: 복잡도에 맞는 atomic 폴더

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
| API·React Query·백엔드 요청 형식 | [references/api.md](references/api.md) |
| 로딩·페이지네이션·UI 패턴 | [references/components-ui.md](references/components-ui.md) |
| 컴포넌트 카탈로그 (core) | [references/component-catalog-core.md](references/component-catalog-core.md) |
| 컴포넌트 카탈로그 (food/shoot/helper/profile) | `references/component-catalog-*.md` |
| 컴포넌트 네이밍 | [references/component-naming.md](references/component-naming.md) |
| 폴더 트리·아키텍처·체크리스트 | [references/architecture.md](references/architecture.md) |

원본 전체 규칙: 레포 `.cursor/rules/` (`api.md`, `components.md`, `folder-structure.md` 등)

---

## skills.sh 배포 구조

공개 저장소에 올릴 때 권장 레이아웃:

```
culwonder-agent-skills/
└── skills/
    └── culwonder-leeds-profile-frontend/
        ├── SKILL.md
        └── references/
```

설치: `npx skills add CulWonder/culwonder-agent-skills@culwonder-leeds-profile-frontend`
