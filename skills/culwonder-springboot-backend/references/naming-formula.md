# 네이밍 공식 (Naming Formula)

도메인이 바뀌어도 동일한 규칙을 적용하기 위한 **플레이스홀더 + 조합 공식**.
예시(`User`, `Store`)는 공식에 `{D}` 등을 치환한 결과일 뿐이다.

## 토큰 사전

| 토큰 | 의미 | 케이스 | 예시 |
|------|------|--------|------|
| `{D}` | 도메인(Aggregate Root) | PascalCase | `User`, `Store`, `Payment` |
| `{d}` | URL·패키지·테이블 접두 | lower_snake / camel | `user`, `store`, `payment` |
| `{A}` | 권한 청중 (Audience) | PascalCase 접미 | `∅`, `Logined`, `Business`, `Admin` |
| `{S}` | 서비스 역할 (Scope) | PascalCase 접미 | `∅`, `Call`, `Provider` |
| `{R}` | 리포지토리 변형 | PascalCase 접미 | `∅`, `Custom`, `Impl` |
| `{Act}` | CRUD·동작 | PascalCase | `Create`, `Update`, `Delete`, `Select`, `ListSearch` |
| `{Type}` | 응답 형태 | PascalCase | `List`, `Detail`, `Delete`, `∅` |
| `{P}` | API 경로 접두 | kebab | `api`, `api-guest`, `api-logined`, `api-business`, `api-admin` |
| `{res}` | 리소스 세그먼트 | **단수** camel | `store`, `deliveryAddress` |
| `{id}` | 식별자 파라미터 | camelCase | `storeId`, `userId` |
| `{child}` | 하위 엔티티 | snake | `account`, `item`, `history` |
| `{item}` | JSON 목록 키 | camelCase + `List` | `storeList`, `foodList` |
| `{Kind}` | Enum 종류 | PascalCase | `Status`, `Type`, `Role`, `EventType` |

> `{D}` = Java 클래스·파일, `{d}` = DB·패키지·URL — 같은 개념, 계층별 표기만 다름.

## 클래스·파일 공식

```
Controller   = {D}{A}Controller
Service      = {D}{S}Service
Repository   = {D}Repository{R}
Entity       = {D} | {D}{Child}
EntityKey    = {EntityName}Key
Enum         = {D}{Kind}

Request      = {D}{Feature?}{Act}Request
Response     = {D}{Type?}Response
Call DTO     = {TargetD}CallRequest | {TargetD}CallResponse
Provider DTO = {D}ProviderResponse
```

### Audience `{A}` → Controller

| `{A}` | 클래스 | `@RequestMapping` |
|-------|--------|-------------------|
| `∅` (Guest) | `AuthController` | `"/"` + 메서드에 전체 경로 |
| `Logined` | `UserLoginedController` | `/api-logined/{d}` |
| `Business` | `StoreBusinessController` | `/api-business/{d}` |
| `Admin` | `UserAdminController` | `/api-admin/{d}` |

### Scope `{S}` → Service

| `{S}` | 역할 |
|-------|------|
| `∅` | 도메인 핵심 비즈니스 |
| `Call` | 타 모듈 **아웃바운드** 호출 |
| `Provider` | 타 모듈에 **노출**하는 파사드 |

### Repository `{R}`

| `{R}` | 역할 |
|-------|------|
| `∅` | Spring Data JPA 인터페이스 |
| `Custom` | QueryDSL 커스텀 인터페이스 |
| `Impl` | QueryDSL 구현체 |

## API URL 공식

```
목록  = /{P}/{d}/{res?}/list          // /list는 항상 맨 끝
단건  = /{P}/{d}/{id}
하위목록 = /{P}/{d}/{sub-res}/list    // 예: /ad-ranking/list
```

| 공식 | 치환 예 |
|------|---------|
| `GET /{P}/{d}/list` | `GET /api/store/list` |
| `GET /{P}/{d}/{id}` | `GET /api/store/{storeId}` |
| `GET /api-logined/{d}/{res}/list` | `GET /api-logined/users/delivery-addresses/list` |

**금지**: 복수형 리소스(`/stores`), `/list`가 중간에 오는 경로(`/list/ad-ranking`).

## DB·패키지 공식

```
패키지 = …api.{d}.{layer}     // layer ∈ entity | service | repository | request | response
테이블 = {d}_module           // Aggregate Root
       | {d}_module_{child}   // 하위 엔티티 (@IdClass)

Audit (모든 테이블 고정) = createdId | createdAt | updatedId | updatedAt
```

## 메서드 공식

**Service (CRUD)**

```
create{D}() | update{D}() | delete{D}()
search{D}List() | search{D}Detail()
process{Verb}() | validate{D}() | calculate{Noun}()
```

**Repository (QueryDSL)**

```
custom{Act}{Target}()
custom{Act}{Target}Count()
create{Target}Conditions()
create{Target}OrderSpecifiers()
```

**JSON 페이징 키**

```
{item}List  where {item} = camelCase({res})
PageResponseUtil.toPageResponse(page, "{item}List", …)
```

## 금지 접미 (공식 위반)

역할은 `{A}`, `{S}`, `{Act}` 토큰으로만 표현한다. 아래 접미로 클래스를 만들지 않는다:

`Registration`, `Account`, `Management`, `Handler`, `Facade`, `Manager`, `Helper`, `Util`, `Api`, `Web`, `Rest`, `Command`, `Query`, `Event`, `Listener`, `Processor`, `Endpoint`, `Resource`, `Action`, `Business`, `Admin` (Service/Controller 표준 `{A}` 외)

## 치환 예 (Store 도메인)

| 공식 | 결과 |
|------|------|
| `{D}{A}Controller`, A=∅ | `StoreController` |
| `{D}{S}Service`, S=Call | `StoreCallService` |
| `{D}Repository{R}`, R=Impl | `StoreRepositoryImpl` |
| `{d}_module` | `store_module` |
| `{d}_module_{child}` | `store_module_image` |
| `{D}{Act}Request`, Act=Create | `StoreCreateRequest` |
| `{item}List` | `storeList` |

## 치트시트 (YAML)

```yaml
# Culwonder Naming Formula v1
tokens:
  D: PascalCase domain root
  d: lower_snake/camel for url·table·package
  A: "" | Logined | Business | Admin
  S: "" | Call | Provider
  R: "" | Custom | Impl

classes:
  controller: "{D}{A}Controller"
  service:    "{D}{S}Service"
  repository: "{D}Repository{R}"
  request:    "{D}{Feature?}{Act}Request"
  response:   "{D}{Type?}Response"
  entity:     "{D}" | "{D}{Child}"
  table:      "{d}_module" | "{d}_module_{child}"

api:
  list:   "/{P}/{d}/{res?}/list"
  detail: "/{P}/{d}/{id}"
  guest_controller: '@RequestMapping("/") + method full path'

json:
  page_key: "{camelCase(res)}List"
```
