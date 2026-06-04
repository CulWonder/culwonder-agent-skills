# 프로젝트·도메인 셋업

## Leeds Profile Core에서 부트스트랩

1. `.cursor/rules/`, `.cursorrules`를 새 레포에 복사 (Cursor 항상 적용 규칙).
2. `api/common/` 복사 후 패키지명 수정.
3. `build.gradle` 의존성 적용 (QueryDSL, Lombok, SpringDoc, MySQL driver).
4. `config/local/application-dev.properties` 설정 — canonical properties는 커밋하지 않음.
5. `SwaggerConfig` OpenAPI title/servers, `WebConfig` CORS origins 수정.
6. 프로젝트별 `ErrorCode` 확장.
7. SKILL.md의 새 도메인 체크리스트대로 첫 도메인 추가.

## 도메인 추가 (최소 단계)

1. `api/{domain}/` 패키지 트리: `entity`, `repository`, `service`, `request`, `response`, `code`.
2. Aggregate Root 엔티티 + 테이블 `{domain}_module` 정의.
3. Repository + Custom + Impl (QueryDSL) 추가.
4. Service / CallService / ProviderService 구현.
5. API 표면(guest, logined, business, admin)별 Controller 추가.
6. 숫자 `displayName`으로 Swagger 그룹 등록.
7. 주요 플로우 integration·service 테스트 추가.

## Cursor rules vs 이 스킬

| 메커니즘 | 역할 |
|----------|------|
| `.cursor/rules/*.mdc` | Cursor에서 파일 패턴 매칭 시 자동 적용 |
| 이 스킬 | `npx skills add`로 설치; 작업 매칭 또는 명시 호출 시 로드 |
| `.cursorrules` | 레포 루트 필수 규칙 요약 |

규칙 변경 시 `.cursor/rules`를 먼저 수정(레포 내 source of truth)한 뒤, skills.sh 사용자를 위해 이 스킬 `references/`를 동기화.

## Leeds Profile 레포의 상세 규칙

전체 규칙(7000줄+) 위치:

```
.cursor/rules/core/
.cursor/rules/api/
.cursor/rules/business/
```

상세 예시는 위 파일 참고; 이 스킬은 SKILL.md ~500줄 이하 + progressive disclosure를 의도적으로 유지.
