# API·컨트롤러 규칙

## 컨트롤러 파일 구성

```
{Domain}Controller.java           # 게스트 — @RequestMapping("/")
{Domain}LoginedController.java    # /api-logined/{domain}
{Domain}BusinessController.java   # /api-business/{domain}
{Domain}AdminController.java      # /api-admin/{domain}
```

`{Domain}Controller`만 클래스 레벨 `@RequestMapping("/")`를 쓰고, 메서드에 전체 경로를 명시:

```java
@RestController
@RequestMapping("/")
public class AuthController
{
    @PostMapping("/api-guest/auth/login")
    public ResponseEntity<CommonResponse> login(...) { }
}
```

## URL 규칙

- 리소스 세그먼트는 **단수형**: `/store`, `/ecommerce`, `/popup`
- **목록**: `{prefix}/{domain}/list` 또는 `{prefix}/{domain}/{sub}/list` — `/list`는 항상 맨 뒤
- **단건**: `{prefix}/{domain}/{id}`
- 금지: `/stores`, `/list/ad-ranking`

## DTO 규칙

- Request: `{Domain}{Action}Request`
- Response: `{Domain}{Type}Response` 또는 `{Domain}Response`
- 요청 본문에 `@Valid` 필수
- Controller에서 `.builder()`로 **인라인** 구성 — `convertToXxx()` private 메서드 금지

## CommonResponse

항상 `ResponseEntity<CommonResponse>` 반환:

```java
CommonResponse commonResponse = new CommonResponse(true, "조회 성공", dto);
return ResponseEntity.ok(commonResponse);
```

에러: `CustomException(ErrorCode.XXX)` throw — `HttpServletResponse`에 직접 쓰지 않음.
`GlobalExceptionHandler`가 실패 JSON을 만든다. 키는 아래만 허용 (`message`/`data` **금지**):

```json
{
  "success": false,
  "successMessage": null,
  "errorMessage": "유효하지 않은 리프레시 토큰입니다. 다시 로그인해주세요",
  "errorCode": "E2003",
  "content": null
}
```

허용: `success` · `successMessage` · `errorMessage` · `errorCode` · `content`.
Refresh 무효(`INVALID_REFRESH_TOKEN`) → `E2003` + HTTP 401. FE 라우트(`/session-expired` 등)는 BE 스킬 범위 밖.

## 페이징 (컨트롤러)

```java
Page<Store> page = storeService.searchStores(request, pageable);
Map<String, Object> content = PageResponseUtil.toPageResponse(page, "storeList", StoreResponse::fromEntity);
// 프로젝트 규칙상 매퍼 메서드 금지 시 Controller에서 인라인 매핑
return ResponseEntity.ok(new CommonResponse(true, "목록 조회 성공", content));
```

Service는 `Page<Entity>` 반환; Repository는 목록 쿼리 + count 쿼리 분리.

## Swagger

- SpringDoc 2.7+; 그룹 `displayName`에 숫자 접두사: `"01. User (Guest)"`
- `springdoc.swagger-ui.groups-order=ASC`
- OpenAPI 설정에 Bearer 인증
- 도메인마다 `GroupedOpenApi` bean 등록

## 인터셉터

- `AuthInterceptor`가 `request.setAttribute("userId", userId)` 설정
- Controller에서 필요 시 `@RequestAttribute("userId")` 사용
