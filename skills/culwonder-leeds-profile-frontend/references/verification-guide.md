# 작업 완료 후 사용자 확인 가이드

에이전트는 **프론트 작업을 마칠 때마다** 아래 형식으로 사용자에게 확인 방법을 전달한다. 코드만 수정하고 확인 절차를 생략하지 않는다.

---

## 에이전트가 답변에 포함할 것 (필수 템플릿)

```markdown
## 변경 요약
- (1~3줄: 무엇이 바뀌었는지)

## 백엔드 요청서 (해당 시)
- 경로: `docs/backend-request/{YYYYMMDD}_{HHmmss}_…_BACKEND_REQUEST.md`
- 백엔드 회신 대기: (path·request·response 요약)
- 해당 없으면 「해당 없음」

## 로컬에서 확인
1. …
2. …

## 테스트/운영에서 확인 (해당 시)
1. …

## 정상 기준
- …

## 문제가 있을 때
- …
```

- **로컬만** 영향 있으면 테스트/운영 절은 「해당 없음」 또는 생략
- **배포·env** 변경이면 테스트(`test.leedsprofile.com`) 확인 절 **필수**
- 확인 URL은 **전체 경로**로 적는다 (예: `https://test.leedsprofile.com/shoot/detail/tanning/999976666`)

---

## 환경별 실행·URL

| 환경 | 빌드/실행 | 로드되는 env | 대표 URL |
|------|-----------|--------------|----------|
| 로컬 개발 | `npm run dev` | `.env.development` + `.env.local` | `http://localhost:3000` |
| 로컬 테스트 모드 | `npm run build:test` → `npm run start:test` | `.env.test` | `http://localhost:3000` |
| Cloudtype 테스트 | `build:test` + `start:test` (`.cloudtype/app-test.yaml`) | `.env.test` | `https://test.leedsprofile.com` |
| Cloudtype 운영 | `npm run build` + `npm start` | `.env.production` | `https://www.leedsprofile.com` |

**주의**

- `npm run build:test` 후 **`npm start`만** 실행하면 `.env.production`이 적용된다. 테스트 설정 검증 시 **`npm run start:test`** 를 쓴다.
- Cloudtype 테스트 서비스는 대시보드에서 **빌드=`npm run build:test`**, **시작=`npm run start:test`** 인지 확인한다 (`preset: next.js`가 기본 `next start`로 덮어쓰지 않는지).
- `.env.production.local` 은 사용하지 않는다. 테스트 값은 `.env.test` + `build:test` / `start:test`.

---

## 작업 유형별 확인 체크리스트

### 1. UI·페이지 변경

| 확인 항목 | 방법 |
|-----------|------|
| 변경 화면 | 브라우저에서 **직접 URL** 접속 (에이전트가 수정한 `page.js` 경로 기준) |
| 반응형 | DevTools 375px(모바일) + 1280px(데스크톱) |
| 로딩·빈 상태 | 네트워크 Slow 3G 또는 빈 목록 조건 |
| 로그인 필요 화면 | 비로그인 → 가드/리다이렉트, 로그인 후 재접속 |
| 탭·앵커 | URL hash·탭 전환 시 스크롤·콘텐츠 일치 |

**Shoot 상세 예시**

- 태닝: `/shoot/detail/tanning/{storeId}` — 포트폴리오 탭 없음, **매장 사진** 섹션만
- 헤메/스튜디오 등: `/shoot/detail/{type}/{storeId}` (`hair-makeup`, `photo-studio`, `shoot-place`, `waxing`)

**프로필·입점 수정**

- `/shoot/profile/business/business-list/detail/tanning` 등 business/basic 경로 각각 확인

### 2. API·React Query 연동

| 확인 항목 | 방법 |
|-----------|------|
| 요청 URL | DevTools **Network** — `getBackendUri()` + `apiPaths` path 일치 |
| 쿠키 | `withCredentials` — 테스트: `accessTokenLeedsTest` 등 (`.env.test`) |
| 성공/실패 UI | 토스트·빈 상태·에러 문구 |
| 캐시 | 동일 액션 후 목록/상세 갱신 (`invalidateQueries` 적용 여부) |

401 + `E2003` → `apis.js`: storage 정리 + backend logout → `/session-expired?redirect=...` (`buildSessionExpiredUrl`; auth 스킬 참고)

### 3. 인증·회원가입

| 확인 항목 | 방법 |
|-----------|------|
| 카카오/구글 OAuth | 리다이렉트 URL이 env의 `NEXT_PUBLIC_*_REDIRECT_URL*` 와 일치 |
| 회원가입 UI | `/signup` — SMS 인증 플로우 (KCP 아님) |
| 시나리오 캡처 | `/test/signup-scenario?screen=phone-code` 등 (로컬·테스트) |
| 시나리오 PNG | `node scripts/capture-signup-scenario.mjs` (서버 기동 후) |

상세 HTTP 흐름 → **`auth-frontend-backend-flow`** 스킬

### 4. env·배포·멀티 도메인

배포 후 **HTML에 박힌 공개 env** 확인:

```bash
# 테스트 사이트가 test URL을 쓰는지 (www가 섞이면 prod 빌드 의심)
curl -sS 'https://test.leedsprofile.com/' | grep -o 'test[^"'\'' ]*leedsprofile[^"'\'' ]*' | sort -u | head

# 특정 페이지 200 여부
curl -sS -o /dev/null -w '%{http_code}\n' 'https://test.leedsprofile.com/test/kcp-cert'
```

| 정상 (테스트) | 비정상 |
|---------------|--------|
| `test.leedsprofile.com`, `test-api.leedsprofile.com` 등 | HTML 전역에 `www.leedsprofile.com` 다수 |
| `/test/*` 라우트 200 | `/test/kcp-cert` 404 |

로컬 테스트 모드:

```bash
npm run build:test && npm run start:test
# 다른 터미널
curl -sS -o /dev/null -w '%{http_code}\n' 'http://localhost:3000/test/kcp-cert'
```

### 5. KCP 본인확인 (`/test/kcp-cert`)

| 확인 항목 | 방법 |
|-----------|------|
| 페이지 로드 | `/test/kcp-cert` — 등록 폼·안내 문구 |
| register API | Network에서 `POST /api/test/kcp-cert/register` 200 |
| enc_data | KCP 팝업/리다이렉트 (샌드박스 `AO7F3` / 테스트 `ALUE0` — `kcpCertServer.js`·env) |
| callback | `/api/test/kcp-cert/callback` 후 `/test/kcp-cert` 복귀 |

운영 경로: `/api/kcp-cert/*` (회원가입 `/signup` 과 분리)

### 6. 컴포넌트·Storybook

| 확인 항목 | 방법 |
|-----------|------|
| 스토리 있음 | `npm run storybook` → 해당 `{Name}.stories.jsx` |
| 스토리 없음 | 실제 사용 페이지에서 props·상태별 확인 |

### 7. 코드 품질 (에이전트가 실행·결과 공유)

```bash
npm run lint
npm test
```

실패 시 사용자에게 **실패 로그 요약** + 수정 여부를 함께 전달.

---

## Cloudtype 배포 확인

1. GitHub `main` 푸시 후 Cloudtype 빌드 로그에서 `build:test` / `start:test` 실행 여부
2. 배포 완료 후 위 **curl·브라우저** 체크
3. 자동 배포 실패( deploy key 등) 시 대시보드 **수동 재배포** 안내

---

## 사용자에게 주지 않을 것

- `.env.local` / 시크릿 값 전체 붙여넣기
- 「확인해 보세요」만 있고 URL·단계 없는 답변
- 백엔드 미구현 API를 프론트만으로 「완료」 처리한 경우 (백엔드 요청서·대기 상태 명시)

---

## 빠른 참조 — 테스트 전용 라우트

| 경로 | 용도 |
|------|------|
| `/test/kcp-cert` | KCP 본인확인 |
| `/test/signup-scenario` | 회원가입 UI 시나리오 (쿼리 `?screen=`) |
| `/test/toss-cert` | 토스 본인확인 테스트 |
| `/test/toss-payment-widget` | 토스 결제 위젯 테스트 |

로컬·`test.leedsprofile.com` 에서 사용. 운영(`www`)에서는 해당 없을 수 있음.
