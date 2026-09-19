# 컴포넌트 카탈로그 — Shoot

전역 컴포넌트 → [component-catalog-core.md](component-catalog-core.md)

---

## 카드 · 목록 (Molecule)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| StudioCard | `@/components/molecule/StudioCard` | shoot 스튜디오 카드 |
| StudioPlaceCard | `@/components/molecule/StudioPlaceCard` | 촬영장 카드 |
| StoreCard | `@/components/molecule/StoreCard` | 스토어 공통 카드 |
| ShootPlaceSwipeList | `@/components/molecule/ShootPlaceSwipeList` | 촬영장 가로 스와이프 목록 |
| StorePartnerHairMakeupSection | `@/components/molecule/StorePartnerHairMakeupSection` | 스튜디오·HMU 제휴 목록 (`storePartnerList` · note + 인스타 링크) |

---

## 사업자 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| BusinessPartnerHairMakeupSection | `@/components/organism/BusinessPartnerHairMakeupSection` | 제휴 등록 note 1필드 (`partnerKind` · POST/PATCH/DELETE) |

---

## 리뷰 · 모달 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| ShootReviewModal | `@/components/organism/ShootReviewModal` | shoot 스토어 리뷰 작성 |

---

## 검색 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| SearchContentShoot | `@/components/organism/SearchContentShoot` | `/shoot/search` 결과 영역 |
| SearchSideBarFilterShoot | `@/components/organism/SearchSideBarFilterShoot` | shoot 검색 사이드 필터 |

---

## Shoot 상세 타입

`shoot/detail/{type}/[storeId]` — type:

- `hair-makeup`
- `photo-studio`
- `shoot-place`
- `tanning`
- `waxing`

상세 페이지는 타입별 `page.js` + 공통 store 상세 패턴 재사용.

---

## 관련 페이지

- `(ui)/shoot/`, `shoot/search`, `shoot/detail/*`
- 프로필 business: store 타입별 관리 (`hair-makeup`, `photo-studio` 등)
