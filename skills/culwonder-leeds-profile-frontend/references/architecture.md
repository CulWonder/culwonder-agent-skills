# 아키텍처·폴더 참고

## 핵심 디렉토리

```
src/
├── app/(ui)/[domain]/   # food, shoot 프로필·주문
├── app/(ui)/food|shoot/ # 도메인 페이지
├── app/api/             # Next API Routes
├── components/{ui,atom,molecule,organism,helper,page,etc}
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

## 유틸 분리

| 유틸 | 용도 |
|------|------|
| `utils/format/*` | 금액, 전화, 사업자번호 |
| `utils/portfolioConcept.js` | API 응답 필드명 차이 흡수 |
| `utils/contactTypes.js` | 연락처 타입·링크 |

## SEO (요약)

- `layout.js`: JSON-LD Organization, WebSite 등
- 개인 페이지: `robots: { index: false, follow: true }`

## 다른 프로젝트에 이식할 때

- [ ] `[domain]` + 레이아웃 Client 패턴
- [ ] ProtectedRoute / LinkGuard / useAuth
- [ ] apis.js + apiPaths.js
- [ ] 401 E2003 → session-expired
- [ ] Provider 순서
- [ ] ProtectedImage + ImageProtection

## 문서

- `doc/LEEDS_PROFILE_PRD.md`, `doc/api-docs.yaml`
- `doc/MODAL_COMPONENTS.md`, `doc/NON_REACT_QUERY_FETCHES.md`
