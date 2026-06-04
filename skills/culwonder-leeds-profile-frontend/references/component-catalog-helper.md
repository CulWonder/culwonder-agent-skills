# 컴포넌트 카탈로그 — Helper

전역 컴포넌트 → [component-catalog-core.md](component-catalog-core.md)

---

## Layout · Provider

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HelperProviders | `@/components/helper/HelperProviders` | helper `layout.js` — children 래핑 |
| HelperUiShell | `@/components/helper/HelperUiShell` | 퀴즈·온보딩·토스트 전역 shell |
| ActivePlanSync | `@/components/helper/ActivePlanSync` | Redux active plan 동기화 |
| HelperGuideSessionMark | `@/components/helper/HelperGuideSessionMark` | 가이드 세션 마킹 |

**HelperUiShell**이 마운트하는 organism: `HelperStyleQuizModal`, `HelperStyleQuizFloatingWidget`, `HelperGuideOnboardingModal`, `HelperPosePickToast`

---

## 헤더 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HelperHeader | `@/components/organism/HelperHeader` | helper 메인 헤더 |
| HelperSubpageHeader | `@/components/organism/HelperSubpageHeader` | helper 서브페이지 헤더 |

---

## 플랜 · 예산 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HelperPlanBudgetSection | `@/components/organism/HelperPlanBudgetSection` | 플랜 예산 섹션 |
| HelperPlanSectionToolbar | `@/components/organism/HelperPlanSectionToolbar` | 플랜 섹션 툴바 |
| HelperPlanStepper | `@/components/organism/HelperPlanStepper` | 플랜 단계 스텝per |
| HelperVendorNeedsChecklist | `@/components/organism/HelperVendorNeedsChecklist` | 벤더 니즈 체크리스트 |

---

## 스타일 · 퀴즈 (Organism + Molecule)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HelperStyleQuizModal | `@/components/organism/HelperStyleQuizModal` | 스타일 퀴즈 모달 |
| HelperStyleQuizFloatingWidget | `@/components/organism/HelperStyleQuizFloatingWidget` | 플로팅 퀴즈 위젯 |
| HelperStyleDetailContentSections | `@/components/organism/HelperStyleDetailContentSections` | 스타일 상세 섹션 |
| HelperStyleCard | `@/components/molecule/HelperStyleCard` | 스타일 카드 |
| HelperMaleStyleCard | `@/components/molecule/HelperMaleStyleCard` | 남성 스타일 카드 |
| HelperStyleSuggestionsCarousel | `@/components/molecule/HelperStyleSuggestionsCarousel` | 스타일 추천 캐러셀 |
| HelperStyleDetailPlanBar | `@/components/molecule/HelperStyleDetailPlanBar` | 스타일 상세 플랜 바 |
| HelperStyleDetailPlanAction | `@/components/molecule/HelperStyleDetailPlanAction` | 플랜 액션 버튼 |
| HelperPlanHeaderLink | `@/components/molecule/HelperPlanHeaderLink` | 플랜 헤더 링크 |
| HelperGuideProgressBar | `@/components/molecule/HelperGuideProgressBar` | 가이드 진행 바 |

훅: `useHelperQuiz`, `useHelperGuideOnboarding`

---

## 포즈 가이드 (Organism + Molecule)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HelperPoseGuideStickyBar | `@/components/organism/HelperPoseGuideStickyBar` | 포즈 가이드 sticky 바 |
| HelperPoseGuideCategoryGallery | `@/components/organism/HelperPoseGuideCategoryGallery` | 카테고리 갤러리 |
| HelperPoseFavoritesPanel | `@/components/organism/HelperPoseFavoritesPanel` | 포즈 즐겨찾기 패널 |
| HelperPosePromptBanner | `@/components/organism/HelperPosePromptBanner` | 포즈 안내 배너 |
| HelperGuidePoseBanner | `@/components/organism/HelperGuidePoseBanner` | 가이드 포즈 배너 |
| HelperPosePickToast | `@/components/organism/HelperPosePickToast` | 포즈 선택 토스트 |
| HelperPoseCategoryCard | `@/components/molecule/HelperPoseCategoryCard` | 포즈 카테고리 카드 |
| HelperPoseFavoriteButton | `@/components/molecule/HelperPoseFavoriteButton` | 포즈 즐겨찾기 버튼 |

---

## 온보딩 · 문의 (Organism)

| 컴포넌트 | import | 용도 |
|---------|--------|------|
| HelperGuideOnboardingModal | `@/components/organism/HelperGuideOnboardingModal` | 가이드 온보딩 모달 |
| HelperShootingInquiryForm | `@/components/organism/HelperShootingInquiryForm` | 촬영 문의 폼 |

데이터/상수 (`components/page/helper/`): `styleDetails.js`, `poseGuideContent.js`, `inquiryStorage.js` — 컴포넌트가 아닌 콘텐츠 참고용.

---

## 관련 페이지

- `(ui)/helper/`, `helper/my-plans`, `helper/pose-guide`, `helper/styles/[slug]`, `helper/inquiries`
