# 코어 표준

## Allman 스타일 (필수)

여는 `{`는 독립된 줄에, 키워드와 같은 열에 정렬 (레벨당 4칸):

```java
public class UserService
{
    public User findUser(String id)
    {
        if (id == null)
        {
            throw new CustomException(ErrorCode.INVALID_INPUT);
        }
        return userRepository.findById(id).orElseThrow(...);
    }
}
```

## Import

- 코드 본문에 FQCN 사용 금지
- 순서: `java.*` → 서드파티 → 프로젝트 패키지
- wildcard import 금지
- 커밋 전 미사용 import 제거

## 네이밍 요약

| 계층 | 패턴 |
|------|------|
| Controller | `{Domain}Controller`, `{Domain}AdminController`, … |
| Service | `{Domain}Service`, `{Domain}CallService`, `{Domain}ProviderService` |
| Repository | `{Domain}Repository`, `{Domain}RepositoryCustom`, `{Domain}RepositoryImpl` |
| Request/Response | `{Domain}{Action}Request`, `{Domain}{Type}Response` |
| Entity | `{Domain}` 또는 설명적 엔티티명 |
| 에러 코드 | `common/code/ErrorCode.java` enum |

## 스택 기본값 (Leeds Profile Core)

- Java 17, Spring Boot 3.5.x
- MySQL, JPA, QueryDSL, Lombok, SpringDoc OpenAPI
- 포트 8082 (로컬 기본값, architecture 문서 기준)

## 설정 파일 불변 (AI)

AI 에이전트는 다음 파일 **생성·수정 금지**:

- `src/main/resources/application.properties`
- `application-dev.properties`, `application-prod.properties`, `application-test.properties`

`config/local/application-{profile}.properties` (gitignored) 사용하거나, 사람이 적용할 키만 문서화.

## 환경 변수

- 값이 필요한 컴포넌트는 `@Value("${app.feature-seconds}")`로 주입
- `AppProperties`는 IDE 메타데이터·검증용 — 비즈니스 코드 런타임 주입용 아님
- 시간 단위 키는 접미사 명시: `-seconds`, `-minutes`

## common 패키지 (신규 프로젝트 필수)

Leeds Profile `api/common/`에서 복사:

- `CommonResponse`, `PageResponse`, `PageResponseUtil`
- `CustomException`, `GlobalExceptionHandler`, `ErrorCode`
- `WebConfig`, `SwaggerConfig`, `JpaConfig` (`@EnableJpaAuditing`)
- `AuthInterceptor`

패키지명: `com.culwonder.leeds_profile_springboot_core` → 대상 프로젝트 base package로 변경.
