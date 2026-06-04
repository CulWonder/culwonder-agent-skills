# 비즈니스 계층 — Entity, Service, Repository

## Entity (DDD)

### 금지

- 정적 팩토리 메서드 (`fromXxx`, `createYyy`)
- 하위 엔티티 전용 Repository로 저장
- aggregate 컬렉션에 `cascade = CascadeType.ALL, orphanRemoval = true` 없이 `@OneToMany`

### 필수

- 호출 지점에서 Builder 사용 (service 또는 aggregate 메서드 본문)
- Aggregate Root가 자식 관리: `auth.addLoginHistory()`, `historyRepository.save()` 금지
- 모든 테이블에 Audit 필드:

```java
@Column(length = 50)
private String createdId;

@CreatedDate
@Column(nullable = false, updatable = false)
private LocalDateTime createdAt;

@Column(length = 50)
private String updatedId;

@LastModifiedDate
@Column(nullable = false)
private LocalDateTime updatedAt;
```

- 엔티티에 `@EntityListeners(AuditingEntityListener.class)`

### 테이블 네이밍

| 역할 | 패턴 | 예시 |
|------|------|------|
| Aggregate root | `{domain}_module` | `user_module` |
| 하위 엔티티 | `{domain}_module_{child}` | `user_module_account` |

## Service 계층

```
{Domain}Service          — 핵심 비즈니스 로직
{Domain}CallService      — 타 모듈 호출
{Domain}ProviderService  — 타 모듈에 노출 (Service 호출)
```

호출 방향:

- Service → CallService, Repository ✅
- ProviderService → Service ✅
- Service → ProviderService ❌

**Entity** (또는 `Page<Entity>`, `List<Entity>`) 반환 — DTO 반환 금지.

## Repository

**Aggregate Root**만 Repository 인터페이스 생성.

```
{Domain}Repository
{Domain}RepositoryCustom
{Domain}RepositoryImpl   — QueryDSL (JPAQueryFactory)
```

- JPQL 문자열 금지
- 페이징: `.offset(pageable.getOffset()).limit(pageable.getPageSize())` + 별도 `count` 쿼리
- 검색·필터는 Custom 메서드; 단순 CRUD는 JpaRepository

## Refresh Token 정책

| 이벤트 | 동작 |
|--------|------|
| 로그인 | ACCESS + REFRESH 새로 발급 |
| 토큰 갱신 | ACCESS만 재발급; REFRESH 동일 |
| Refresh 만료 | `EXPIRED_REFRESH_TOKEN` — 재로그인 필요 |
| 로그아웃 | 두 토큰 모두 삭제 |

금지: sliding session, 매 갱신마다 refresh rotation, 만료된 refresh 재발급.

## 트랜잭션·예외

- Service command/query 메서드에 적절히 `@Transactional`
- 비즈니스 실패 → `CustomException` + `ErrorCode`
- 적절한 레벨로 로깅; 예외 삼키지 않음
