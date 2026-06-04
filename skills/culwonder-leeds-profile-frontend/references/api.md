# API 통신 참고 (Leeds Profile Next)

## 단일 진입점

- `src/utils/apis.js` — `api.get | post | patch | delete`
- `src/utils/apiPaths.js` — path 상수·함수, `getBackendUri()` → `NEXT_PUBLIC_BACKEND_URL`

## 호출 형태

```javascript
// GET
await api.get({ uri: getBackendUri(), path: users.profile, params: {} });

// POST
await api.post({ uri: getBackendUri(), path: auth.tokens, data: { provider, code } });

// PATCH / DELETE — 동일하게 객체 첫 인자
```

두 번째 인자(선택): `{ timeout, headers, ... }`

## React Query

```javascript
useQuery({
  queryKey: ['users', 'profile'],
  queryFn: () => api.get({ uri: getBackendUri(), path: users.profile, params: {} }),
});
```

- queryKey는 도메인별로 일관되게 (`useAuth`는 `['auth','status']` 등 기존 패턴 따름)

## apiPaths 도메인 예

`auth`, `sms`, `users`, `store`, `storeLogined`, `ecommerce`, `ecommerceLogined`, `admin`, `business` — 동적 path는 함수: `storeLogined.one(storeId)`

## Same-origin vs Backend

| 대상 | base | 예 |
|------|------|-----|
| Next API Route | `window.location.origin` 또는 path만 | `/api/auth/status` |
| Spring API | `getBackendUri()` | `/api-guest/auth/tokens` |

→ 인증·로그아웃 시 어느 쪽인지 먼저 확인 (`auth-frontend-backend-flow`).

## 에러 처리

- 실패 시 `Promise.reject`, `error.message` 활용
- try/catch 또는 React Query `onError`에서 UI 처리

## 백엔드 요청사항 정리 템플릿

프론트에서 해결 불가할 때:

```markdown
## 백엔드 요청사항

### [제목]

**현재 상황:** …

**요청사항:**
1. [엔티티]에 [필드] 추가 (타입, maxLength 등)
2. `METHOD /api/...` — 요청/응답 JSON 예시
```

코드로 백엔드를 대체 구현하지 않는다.
