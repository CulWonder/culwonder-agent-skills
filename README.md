# CulWonder Agent Skills

CulWonder 프로젝트용 [Cursor Agent Skills](https://skills.sh/) 모음입니다.

## Skills

| Skill | 설명 |
|-------|------|
| `culwonder-springboot-backend` | Leeds Profile Spring Boot 3.5 백엔드 규칙 (Allman, DDD, QueryDSL, CommonResponse, API 접두사) |
| `culwonder-leeds-profile-frontend` | Leeds Profile Next.js 16 App Router 프론트엔드 규칙 (apis.js, Atomic Design, Redux, React Query) |
| `auth-frontend-backend-flow` | 인증·OAuth·세션·로그아웃 HTTP 흐름 (same-origin vs backend) |

## 설치

```bash
# 백엔드 (Spring Boot)
npx skills add CulWonder/culwonder-agent-skills@culwonder-springboot-backend

# 프론트엔드
npx skills add CulWonder/culwonder-agent-skills@culwonder-leeds-profile-frontend

# 인증 흐름 (프론트 스킬과 함께 사용)
npx skills add CulWonder/culwonder-agent-skills@auth-frontend-backend-flow
```

전역 설치:

```bash
npx skills add CulWonder/culwonder-agent-skills@culwonder-leeds-profile-frontend -g -y
```

## 레포 구조

```
skills/
├── culwonder-springboot-backend/
│   ├── SKILL.md
│   └── references/
├── culwonder-leeds-profile-frontend/
│   ├── SKILL.md
│   └── references/
└── auth-frontend-backend-flow/
    └── SKILL.md
```

## 동기화

| 스킬 | 원본 레포 |
|------|-----------|
| `culwonder-springboot-backend` | [leeds_profile_springboot_core](https://github.com/CulWonder/leeds_profile_springboot_core) `.agents/skills/`, `.cursor/rules/` |
| `culwonder-leeds-profile-frontend`, `auth-frontend-backend-flow` | [leeds_profile_next](https://github.com/CulWonder/leeds_profile_next) `.agents/skills/`, `.cursor/rules/` |

규칙 변경 시 원본 레포 수정 후 이 레포도 함께 업데이트하세요.

## License

MIT — see [LICENSE](LICENSE).
