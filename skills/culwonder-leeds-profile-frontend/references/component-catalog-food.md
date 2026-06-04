# 컴포넌트 카탈로그 — Food

전역 컴포넌트 → [component-catalog-core.md](component-catalog-core.md)

---

## 카드 · 목록 (Molecule)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| FoodCard | `@/components/molecule/FoodCard` | food 상품 목록 카드 |
| FoodCardSwipeList | `@/components/molecule/FoodCardSwipeList` | 가로 스와이프 food 목록 |
| FoodProductManagementCard | `@/components/molecule/FoodProductManagementCard` | 사업자 food 상품 관리 카드 |
| CartItemInfoCard | `@/components/molecule/CartItemInfoCard` | 장바구니 라인 아이템 |
| CartMacroDisclaimer | `@/components/molecule/CartMacroDisclaimer` | 영양·매크로 안내 문구 |
| PurchaseLineList | `@/components/molecule/PurchaseLineList` | 구매 내역 라인 목록 |
| ReviewableProductThumbnail | `@/components/molecule/ReviewableProductThumbnail` | 리뷰 가능 상품 썸네일 |

---

## 식단 계산기 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| MealCalculatorFloatingShell | `@/components/organism/MealCalculatorFloatingShell` | 플로팅 UI shell |
| MealCalculatorFloatingButton | `@/components/organism/MealCalculatorFloatingButton` | food 홈 등 플로팅 진입 |
| CartMealCalculatorFloatingButton | `@/components/organism/CartMealCalculatorFloatingButton` | 장바구니 플로팅 |
| CompletedMealCalculatorFloatingButton | `@/components/organism/CompletedMealCalculatorFloatingButton` | 완료 후 플로팅 |
| MealCalculatorStep1Modal ~ Step4Modal | `@/components/organism/MealCalculatorStep{N}Modal` | 단계별 모달 |
| MealCalculatorStep1Shared | `@/components/molecule/MealCalculatorStep1Shared` | Step1 공유 UI |

Redux: `commonReducerPersist` — meal calculator 상태 persist.

---

## 리뷰 · 모달 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| FoodReviewModal | `@/components/organism/FoodReviewModal` | food 리뷰 작성 |

---

## 검색 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| SearchContentFood | `@/components/organism/SearchContentFood` | `/food/search` 결과 영역 |
| SearchSideBarFilterFood | `@/components/organism/SearchSideBarFilterFood` | food 검색 사이드 필터 |

---

## Context

| 이름 | 용도 |
|------|------|
| FoodFavoriteOverridesContext | 동일 상품 다중 노출 시 하트 상태 동기화 (`productId` / `foodId` key) |

---

## 관련 페이지

- `(ui)/food/home`, `search`, `cart`, `detail/[foodId]`, `payment`
- 프로필: `favorites-ecommerce`, `orderList`, business food 관리
