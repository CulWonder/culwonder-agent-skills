# 컴포넌트 카탈로그 — Core (전역)

새 UI를 만들기 **전에** 이 목록을 확인한다. 아래 컴포넌트의 **대체 구현·복제 금지**.

도메인별 목록:
- [component-catalog-food.md](component-catalog-food.md)
- [component-catalog-shoot.md](component-catalog-shoot.md)
- [component-catalog-helper.md](component-catalog-helper.md)
- [component-catalog-profile.md](component-catalog-profile.md)

---

## Modal vs Dialog 선택

| 상황 | 사용 |
|------|------|
| 관리자·사업자·업로드·복잡한 footer·커스텀 width | `@/components/atom/Modal` |
| 확인/취소만 필요 | `@/components/atom/ConfirmModal` |
| shadcn 스타일의 가벼운 UI | `@/components/ui/dialog` 또는 `alert-dialog` |

---

## Atom

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| Modal | `@/components/atom/Modal` | 범용 모달 (`isOpen`, `onClose`, `title`, `size`, `footer`) |
| ConfirmModal | `@/components/atom/ConfirmModal` | 확인/취소 다이얼로그 |
| ImageUploader | `@/components/atom/ImageUploader` | 다중 이미지 업로드·드래그 정렬·`apiPaths.images` 연동 |
| SimpleImageUploader | `@/components/atom/SimpleImageUploader` | 단일 이미지 (간단 케이스; 보통 ImageUploader 우선) |
| SwitchToggle | `@/components/atom/SwitchToggle` | ON/OFF 토글 |
| ProtectedImage | `@/components/atom/ProtectedImage` | 노출 이미지 (드래그·저장 방지). 업로드 UI만 `protect={false}` |
| SkipLink | `@/components/atom/SkipLink` | 접근성 스킵 링크 |

---

## Auth / Guard

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| ProtectedRoute | `@/components/auth/ProtectedRoute` | 페이지 권한 가드 (`USER_ROLE`, `requiredRole`) |
| onClickGuardLoginRequest | `@/components/auth/onClickGuardLoginRequest` | 버튼/클릭 — 비로그인 시 로그인 유도 |
| LinkGuardLoginRequest | `@/components/auth/LinkGuardLoginRequest` | Link 클릭 — 비로그인 시 로그인 유도 |
| GuardLoginRequest | `@/components/auth/GuardLoginRequest` | children 래핑 클릭 가드 |
| LinkGuard | `@/components/auth/LinkGuard` | Link + 커스텀 onClick (로그인 가드 아님) |

**선택 기준**
- 전체 페이지 → `ProtectedRoute`
- 특정 버튼/링크만 → `onClickGuardLoginRequest` / `LinkGuardLoginRequest` / `GuardLoginRequest`

`USER_ROLE`: `GUEST`, `USER`, `BUSINESS`, `ADMIN` — `ProtectedRoute.jsx` export.

---

## Molecule (공통)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| CustomPagination | `@/components/molecule/CustomPagination` | **목록 페이지네이션 표준**. `getPageNumbers` 내장, 0-based |
| CheckboxFilter | `@/components/molecule/CheckboxFilter` | 검색·목록 체크박스 필터 |
| ImageLightbox | `@/components/molecule/ImageLightbox` | 이미지 확대 뷰 |
| ProfileImageEditor | `@/components/molecule/ProfileImageEditor` | 프로필 이미지 편집 (ImageUploader 래핑) |

페이지네이션 규칙·로딩 UI → [components-ui.md](components-ui.md)

---

## Layout Shell (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| Topbar | `@/components/organism/Topbar` | 최상단 바 |
| HomeHeader | `@/components/organism/HomeHeader` | GNB·검색·식단계산기 진입 |
| HomeFooter | `@/components/organism/HomeFooter` | 푸터 |
| GlobalHeader | `@/components/organism/GlobalHeader` | food/shoot 외 단독 레이아웃 헤더 |
| MobileTabBar | `@/components/organism/MobileTabBar` | 모바일 하단 탭 |
| PageTitle | `@/components/organism/PageTitle` | 검색·목록 페이지 타이틀 |
| GoToTopButton | `@/components/organism/GoToTopButton` | 상단 이동 버튼 |

**멀티 도메인 레이아웃** (`DomainLayoutClient.jsx`): `Topbar` → `HomeHeader` → `{children}` → `HomeFooter`

---

## 검색 (Organism — 도메인별 Content/Filter는 domain catalog 참고)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| SearchHeader | `@/components/organism/SearchHeader` | 검색 페이지 헤더 |
| SearchResultsSkeleton | `@/components/organism/SearchResultsSkeleton` | 검색 결과 로딩 스켈레톤 |

---

## shadcn/ui

`@/components/ui/`에서 import. 전체 목록 복붙 금지 — 필요한 것만 가져온다.

| 자주 씀 | import | 비고 |
|--------|--------|------|
| Button | `@/components/ui/button` | 기본 버튼 |
| brand-button | `@/components/ui/brand-button` | 리즈 브랜드 CTA |
| Input / Label | `@/components/ui/input`, `label` | 폼 |
| Dialog / AlertDialog | `@/components/ui/dialog`, `alert-dialog` | 가벼운 모달 |
| Tooltip | `@/components/ui/tooltip` | 툴팁 |

클래스 병합: `cn()` from `@/lib/utils.js`

---

## Helper / Provider (전역 부트)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| ImageProtection | `@/components/atom/ImageProtection` | `providers.js` — 전역 이미지 보호 |
| PersistLegacyMigration | `@/components/helper/PersistLegacyMigration` | localStorage 레거시 마이그레이션 |

---

## 금지 · 주의

- ❌ `Modal`, `ImageUploader`, `CustomPagination`, `ProtectedRoute` 대체 구현
- ❌ 목록마다 `getPageNumbers` 재작성 — `CustomPagination` 사용
- ❌ 노출 `<img>` / `<Image>` 직접 사용 — `ProtectedImage` (업로드 UI 예외)
- ❌ shadcn `ui/*` 소스 복사 — `@/components/ui/` import
