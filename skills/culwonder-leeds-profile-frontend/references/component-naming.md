# 컴포넌트 네이밍 — `{Scope?}{Role}{Variant?}`

새 컴포넌트·리네이밍 시 이 공식을 따른다. **Role은 필수**, Scope·Variant는 필요할 때만.

---

## 공식

```
{Scope?}{Role}{Variant?}
```

| 부분 | 필수 | 의미 | 예 |
|------|------|------|-----|
| **Scope** | 선택 | 도메인·기능 범위 | `Food`, `Shoot`, `Helper`, `Profile`, `MealCalculator` |
| **Role** | **필수** | UI·행동 역할 | `Card`, `Filter`, `Content`, `Modal`, `Section`, `List`, `Banner` |
| **Variant** | 선택 | 형태·단계·용도 차이 | `Swipe`, `Compact`, `Floating`, `Step3`, `SideBar` |

**읽는 법**

- Scope 없음 → **전 도메인 공통** (Core)
- Variant 없음 → 해당 Role의 **기본형**

---

## 3계층

| 계층 | 이름 패턴 | 배치 | 예 |
|------|-----------|------|-----|
| **Core** | `{Role}{Variant?}` | `atom/`, `molecule/` | `Modal`, `ListingCard`, `SearchContent` |
| **Domain** | `{Scope}{Role}{Variant?}` | `{layer}/{domain}/` 또는 flat | `FoodListingCard`, `ShootSearchContent` |
| **Feature** | `{Feature}{Variant?}{Role}` | `organism/{domain}/` | `MealCalculatorStep3Modal`, `HelperStyleQuizModal` |

**Scope를 붙이는 기준** — API·Redux·UX 중 **하나라도 도메인별로 30% 이상 다르면** Scope 사용.  
그렇지 않으면 Core + `domain` prop 또는 `page/{domain}/*.js` config.

**Feature Scope** — 한 기능 플로우에만 묶인 UI (`MealCalculator`, `HelperPose` 등). 다른 도메인 재사용 불가.

---

## Role 목록 (자주 쓰는 것)

| Role | 용도 |
|------|------|
| `Card` | 목록·그리드 아이템 |
| `List` | 카드·행 나열 (스와이프 등 Variant와 조합) |
| `Content` | 페이지 본문·검색 결과 영역 |
| `Filter` | 필터 UI (Variant: `SideBar`, `Checkbox` 등) |
| `Modal` | 오버레이 다이얼로그 |
| `Section` | 페이지 내 섹션 블록 |
| `Banner` | 프로모·안내 배너 |
| `Header` / `Footer` | 레이아웃 상·하단 |
| `Form` | 입력·제출 폼 |
| `Skeleton` | 로딩 placeholder |

Role이 위 표에 없으면 **가장 가까운 단어 하나**를 고른다. 복합어(`SearchContentArea`) 대신 Role 하나(`SearchContent` 또는 `Content`).

---

## 결정 트리 (새 컴포넌트)

```
2개 이상 도메인에서 재사용 가능한가?
├─ 예 → {Role}{Variant?}
│        예: ListingCard, SearchContent, SideBarFilter
└─ 아니오 → 한 기능 플로우 전용인가?
     ├─ 예 → {Feature}{Variant?}{Role}
     │        예: MealCalculatorStep3Modal, HelperStyleQuizFloatingWidget
     └─ 아니오 → {Scope}{Role}{Variant?}
                  예: FoodListingCard, ShootSideBarFilter
```

---

## 레포 예시

| 현재 이름 | 공식 분해 | 비고 |
|-----------|-----------|------|
| `Modal` | `{Role}` | Core |
| `CustomPagination` | `{Variant}{Role}` | Core |
| `ProtectedRoute` | `{Variant?}{Role}` | Core — `auth/` |
| `FoodCard` | `Food` + `Card` | Domain |
| `StudioCard` | (Scope 암시: Shoot) + `Card` | Domain — Scope 명시 권장 |
| `SearchContentFood` | `Food` + `Search` + `Content` | Domain — 통합 시 `SearchContent` + config |
| `SearchSideBarFilterShoot` | `Shoot` + `SideBar`(Variant) + `Filter` | Domain |
| `MealCalculatorStep3Modal` | `MealCalculator` + `Step3` + `Modal` | Feature |
| `HelperPlanBudgetSection` | `Helper` + `PlanBudget` + `Section` | Domain/Feature |

---

## 폴더와 Scope

파일명에 Scope가 길어지면 **폴더로 Scope, 이름은 Role 중심**도 허용:

```
molecule/
├── ListingCard.jsx           # Core
├── food/FoodListingCard.jsx  # Domain adapter
organism/
├── search/SearchContent.jsx
page/
├── food/searchConfig.js      # 도메인별 query·filter 설정
├── helper/guideOnboarding.js
```

---

## 금지·주의

- ❌ Role 생략 — `SearchFood`, `FoodArea` (→ `FoodSearchContent` 또는 `SearchContent`)
- ❌ Scope 중복 — `HelperHelperModal`
- ❌ 동일 UI를 `FoodXxx` / `ShootXxx`로 복제 — Core + config/prop 우선
- ❌ Core 이름(`Card.jsx`) 하나에 모든 도메인 분기 — Domain adapter 또는 config 분리
- ✅ 기존 이름 유지 + 신규만 공식 적용 (점진적 마이그레이션)
- ✅ 리네이밍 시 re-export alias로 호환 유지 가능

---

## 워크플로우 체크리스트

1. [component-catalog-core.md](component-catalog-core.md) — Core로 대체 가능한지
2. 도메인 카탈로그 — 동일 Role 존재하는지
3. `{Scope?}{Role}{Variant?}` 로 이름 확정
4. Atomic Design 계층·폴더 배치 (`SKILL.md` 참고)
5. 도메인 차이는 `components/page/{domain}/` config로 분리 검토
