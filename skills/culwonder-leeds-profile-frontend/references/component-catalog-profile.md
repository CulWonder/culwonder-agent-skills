# 컴포넌트 카탈로그 — Profile · Props · Admin

전역 컴포넌트 → [component-catalog-core.md](component-catalog-core.md)

---

## Profile 사이드 메뉴 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| ProfileSideMenu | `@/components/organism/ProfileSideMenu` | basic 프로필 사이드 |
| ProfileSideMenuBusiness | `@/components/organism/ProfileSideMenuBusiness` | business 프로필 사이드 |
| ProfileSideMenuFood | `@/components/organism/ProfileSideMenuFood` | food basic 메뉴 변형 |
| ProfileSideMenuFoodBusiness | `@/components/organism/ProfileSideMenuFoodBusiness` | food business 메뉴 변형 |

**레이아웃**
- `(ui)/[domain]/profile/basic/layout.js` → `ProfileSideMenu` + `ProfileSideMenuFood`
- `(ui)/[domain]/profile/business/layout.js` → `ProfileSideMenuBusiness`

---

## 주문 · 반품 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| ProfileOrderListItemTypeItemCard | `@/components/organism/ProfileOrderListItemTypeItemCard` | 주문 목록 아이템 카드 |
| ReturnRequestModal | `@/components/organism/ReturnRequestModal` | 반품 요청 모달 |

---

## Props / 소품 (Molecule + Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| PropsProductCard | `@/components/molecule/PropsProductCard` | 소품 상품 카드 |
| PropsCardSwipeList | `@/components/molecule/PropsCardSwipeList` | 가로 스와이프 목록 |
| PropsProductManagementCard | `@/components/molecule/PropsProductManagementCard` | 사업자 소품 관리 |
| SearchContentProps | `@/components/organism/SearchContentProps` | `/props/search` 결과 |
| SearchSideBarFilterProps | `@/components/organism/SearchSideBarFilterProps` | props 검색 필터 |

---

## Item / 입점 폼 (Molecule)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| ItemOptionChipField | `@/components/molecule/ItemOptionChipField` | 옵션 칩 입력 |
| ItemOptionFields | `@/components/molecule/ItemOptionFields` | 옵션 필드 세트 |
| ItemCostumeVariantFields | `@/components/molecule/ItemCostumeVariantFields` | 코스튬 variant 필드 |
| ItemOptionPurchaseSelector | `@/components/molecule/ItemOptionPurchaseSelector` | 구매 옵션 선택 |

사용처: `store/create-request`, business item-distribution, food join 등.

---

## Admin (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| AdminShellLayout | `@/components/organism/admin/AdminShellLayout` | 어드민 레이아웃 shell |
| AdminEcommerceJoinAuditSectionPage | `@/components/organism/admin/AdminEcommerceJoinAuditSectionPage` | 이커머스 입점 심사 |
| StoreJoinAuditParts | `@/components/organism/admin/StoreJoinAuditParts` | 스토어 입점 심사 파트 |
| AdminPopupForm | `@/components/organism/admin/AdminPopupForm` | 팝업 등록/수정 |
| AdminPopupListTable | `@/components/organism/admin/AdminPopupListTable` | 팝업 목록 테이블 |

---

## 홈 팝업 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HomePopupContainer | `@/components/organism/HomePopupContainer` | 메인 팝업 컨테이너 |
| HomePopupModal | `@/components/organism/HomePopupModal` | 메인 팝업 모달 |

---

## 관련 페이지

- `(ui)/[domain]/profile/basic/*`, `profile/business/*`
- `(ui)/[domain]/order/[orderId]`
- `(ui)/props/`, `store/create-request/*`
- `(ui)/admin/`
