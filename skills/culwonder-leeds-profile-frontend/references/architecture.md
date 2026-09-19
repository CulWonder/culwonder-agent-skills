# 아키텍처·폴더 참고

## 핵심 디렉토리

```
src/
├── app/(ui)/[domain]/   # food, shoot 프로필·주문
├── app/(ui)/food|shoot/ # 도메인 페이지
├── app/api/             # Next API Routes
├── components/{ui,atom,molecule,organism,page,auth,helper,etc}
├── hooks/
├── redux/
└── utils/               # apis, apiPaths, format, auth helpers
```

## DomainLayoutClient

```jsx
export default function DomainLayoutClient({ children }) {
  return (
    <>
      <Topbar />
      <Header />
      <FoodFavoriteOverridesProvider>{children}</FoodFavoriteOverridesProvider>
      <Footer />
    </>
  );
}
```

## 컴포넌트 네이밍

이름 공식: **`{Scope?}{Role}{Variant?}`** — Role 필수, Scope·Variant는 필요 시.

- Core: Scope 없음 (`Modal`, `SearchContent`)
- Domain: `Food` + `Card`, `Shoot` + `SideBarFilter`
- Feature: `MealCalculator` + `Step3` + `Modal`

상세 → [component-naming.md](component-naming.md)

## 유틸·콘텐츠 분리

| 유틸 | 용도 |
|------|------|
| `utils/format/*` | 금액, 전화, 사업자번호 |
| `utils/portfolioConcept.js` | API 응답 필드명 차이 흡수 |
| `utils/contactTypes.js` | 연락처 타입·링크 |

### 화면 전용 copy·정적 데이터

- 탭·섹션·가이드 본문 → **`src/app/(ui)/{route}/page.js` 파일 안** (`const` / `function`, default export 아래·위 모두 가능)
- ❌ `*Content.js`, `*Data.js`, `page.js` 옆 형제 분리 파일
- ❌ organism이 데이터 파일 직접 import — `page.js`에서 props 전달
- 다른 라우트 공유 → 원본 `page.js`의 **named export** (데이터는 여전히 그 파일 안)
- ✅ 여러 라우트 공유 config만 `components/page/{domain}/` (예: `styleBySlug.js`)

상세 → [page-content.md](page-content.md) (SKILL §7 요약)

## SEO (요약)

- `layout.js`: JSON-LD Organization, WebSite 등
- 개인 페이지: `robots: { index: false, follow: true }`

## 다른 프로젝트에 이식할 때

- [ ] `[domain]` + 레이아웃 Client 패턴
- [ ] ProtectedRoute / LinkGuard / useAuth
- [ ] apis.js + apiPaths.js
- [ ] 401 E2003 → apis.js → `/session-expired?redirect=...` (`buildSessionExpiredUrl`)
- [ ] Provider 순서
- [ ] ProtectedImage + ImageProtection

## 문서

**파일명 (신규·갱신)**: SKILL.md **§1-2** — `{YYYYMMDD}_{HHmmss}_{기능명}_{문서유형}.md`

| 폴더 | 용도 | 예 |
|------|------|-----|
| `docs/backend-request/` | 백엔드 API 요청 | `20260614_143052_VOC_CUSTOMER_BACKEND_REQUEST.md` |
| `docs/` | 프론트 가이드·검증·협업 | `20260621_212537_BUSINESS_ORDER_MANAGEMENT_FRONTEND_GUIDE.md` |
| `docs/signup-flow/` | 회원가입 UI 캡처 등 | — |

→ 전체 안내: **`docs/README.md`**
