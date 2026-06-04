# CulWonder Agent Skills

CulWonder 프로젝트용 [Cursor Agent Skills](https://skills.sh/) 모음입니다.

## Skills

| Skill | 설명 |
|-------|------|
| `culwonder-leeds-profile-frontend` | Leeds Profile Next.js 16 App Router 프론트엔드 규칙 (apis.js, Atomic Design, Redux, React Query) |
| `auth-frontend-backend-flow` | 인증·OAuth·세션·로그아웃 HTTP 흐름 (same-origin vs backend) |

## 설치

```bash
# 프론트엔드 메인 스킬
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
├── culwonder-leeds-profile-frontend/
│   ├── SKILL.md
│   └── references/
└── auth-frontend-backend-flow/
    └── SKILL.md
```

## 동기화

스킬 원본은 [leeds_profile_next](https://github.com/CulWonder/leeds_profile_next) 레포 `.agents/skills/`와 `.cursor/rules/`를 기준으로 유지합니다. 규칙 변경 시 이 레포도 함께 업데이트하세요.

## License

MIT — see [LICENSE](LICENSE).
