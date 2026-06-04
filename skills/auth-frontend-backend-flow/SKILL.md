---
name: auth-frontend-backend-flow
description: >-
  Maps Leeds Profile Next signup, login, session status, and logout to HTTP calls:
  same-origin Next routes vs NEXT_PUBLIC_BACKEND_URL (getBackendUri), apiPaths auth/sms/users,
  useAuth React Query keys, OAuth redirect env vars. Use when implementing or debugging
  authentication, OAuth callbacks, SMS verification signup, session-expired, logout,
  HttpOnly cookies, or mismatches between /api/auth/status and backend logout.
---

# Auth: frontend ↔ backend call flow (Leeds Profile Next)

## When to use this skill

- Adding or changing login, signup, logout, or “am I logged in?” behavior
- Tracing why `useAuth` shows logged in/out wrong, or cookies not clearing
- Explaining or implementing OAuth → token → profile sequence
- Porting auth behavior to another screen or app

## Non-negotiables (this repo)

- Call APIs through `src/utils/apis.js` (`api.get` / `api.post` with `withCredentials: true`).
- Paths come from `src/utils/apiPaths.js` (`auth`, `sms`, `users`, `getBackendUri()`). Do not hardcode backend URLs in feature code.

## Two request targets

| Target | Base | Example |
|--------|------|---------|
| **Same-origin (Next)** | `window.location.origin` | `GET /api/auth/status` → `src/app/api/auth/status/route.js` |
| **Backend API** | `getBackendUri()` → `NEXT_PUBLIC_BACKEND_URL` | `POST .../api-guest/auth/tokens` |

## Endpoints by flow (backend = `getBackendUri()`)

**Signup** (`src/app/(ui)/signup/page.js`)

1. OAuth redirect only (Kakao/Google) — no API until SMS
2. `POST` backend + `/api/sms/verification-codes` — send code (`sms.verificationCodes`)
3. `POST` backend + `/api/sms/verification-codes/validate` — verify (`sms.validate`)
4. `POST` backend + `/api-guest/users/accounts` — create account (`auth.accounts`), body `{ provider, code, phone }`

**Login** (`src/app/(ui)/login/page.js`)

1. OAuth redirect only
2. `POST` backend + `/api-guest/auth/tokens` (`auth.tokens`), body `{ provider, code }` — provider from OAuth `state` (`socialProviderFromOAuthState`)
3. Success → full navigation to `/`; failure with E2007 or signup-related message → signup modal path

**Admin login** (`src/app/(ui)/admin/page.js`): `POST` backend + `/api-guest/auth/admin-login` (`auth.adminLogin`).

**Session status** (`src/hooks/useAuth.js`)

1. `GET` **same-origin** + `/api/auth/status` (`auth.status`) — `isLoggedIn` from cookie **`accessTokenLeeds`** presence (Next route does not validate JWT)
2. If logged in: `GET` backend + `/api-logined/users/profile` (`users.profile`)

React Query: `['auth', 'status']` and `['auth', 'profile']`; status cached ~5 min, refetch on mount/focus off for status.

**Logout**

- `useAuth().logout` and `/session-expired` (non-OAuth-relogin): `POST` **backend** + `/api/auth/logout` (`auth.logout`), then remove auth queries and hard-navigate as implemented.
- Some flows (e.g. withdraw) call **same-origin** `POST /api/auth/logout` (Next `src/app/api/auth/logout/route.js`) — clears different cookie names than status checks (`accessTokenLeeds`). If logout seems “stuck”, verify which host clears which cookie.

**Utilities**: `src/utils/kakaoAuth.js`, `src/utils/googleAuth.js`; signup vs login uses different redirect env vars (`NEXT_PUBLIC_*_REDIRECT_URL` vs `*_SIGNUP`).

## Agent workflow

1. Identify whether the change needs **Next same-origin** or **backend** base URL — wrong base is a common bug.
2. Open the file from the “Implementation map” below; confirm `path:` matches `apiPaths.js`.
3. After auth changes, check logout + status cookie story if UX depends on “logged out” immediately.

## Implementation map

| Concern | File |
|---------|------|
| Paths | `src/utils/apiPaths.js` |
| HTTP client | `src/utils/apis.js` |
| Status + logout + profile fetch | `src/hooks/useAuth.js` |
| Login callback | `src/app/(ui)/login/page.js` |
| Signup | `src/app/(ui)/signup/page.js` |
| Status route | `src/app/api/auth/status/route.js` |
| Next logout route | `src/app/api/auth/logout/route.js` |
| Session expired | `src/app/(ui)/session-expired/page.js` |

## Full reference

Tables, mermaid sequence diagram, and extended notes: [doc/auth-frontend-backend-flow.md](../../../doc/auth-frontend-backend-flow.md)

Related: [doc/login-status-detection-porting-guide.md](../../../doc/login-status-detection-porting-guide.md), [doc/auth-cross-platform-integration-guide.md](../../../doc/auth-cross-platform-integration-guide.md) (non-Next clients), [doc/auth-kakao-token-flow.md](../../../doc/auth-kakao-token-flow.md).
