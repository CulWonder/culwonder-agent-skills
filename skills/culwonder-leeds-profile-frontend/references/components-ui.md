# 컴포넌트·UI 참고

## Import 예시

```javascript
import { Button } from '@/components/ui/button';
import Modal from '@/components/atom/Modal';
import FoodCard from '@/components/molecule/FoodCard';
import HomeHeader from '@/components/organism/HomeHeader';
import ProtectedRoute from '@/components/helper/ProtectedRoute';
import ProtectedImage from '@/components/atom/ProtectedImage';
```

## ProtectedImage

```jsx
<ProtectedImage src={url} alt="..." width={w} height={h} />
<ProtectedImage native src={externalUrl} alt="..." />
<ProtectedImage protect={false} ... />  {/* 업로드 UI */}
```

직접 `<Image>` / `<img>` 쓸 때: `draggable="false"`, 컨테이너에 `no-image-download`.

## 로딩·빈 상태 (전체 패턴)

```jsx
{isLoading ? (
  <div className="py-[100px] flex flex-col items-center justify-center gap-y-[12px]">
    <div className="w-[60px] h-[60px] border-4 border-[#FEC104] border-t-transparent rounded-full animate-spin" />
    <p className="text-[14px] text-gray-500">데이터가 없습니다.</p>
  </div>
) : items.length === 0 ? (
  <div className="py-[100px] flex flex-col items-center justify-center gap-y-[12px]">
    <Image src="/icons/....svg" alt="데이터 없음" width={60} height={60} draggable="false" loading="lazy" />
    <p className="text-[14px] text-gray-500">데이터가 없습니다.</p>
  </div>
) : (
  /* 목록 */
)}
```

## 페이지네이션

목록 UI는 **`@/components/molecule/CustomPagination`** 사용 (규칙 내장). 페이지마다 `getPageNumbers` 재작성 금지.

### `getPageNumbers` 요약 (CustomPagination 내부와 동일)

- `totalPages = Math.ceil(total / size)`, `currentPage` 0-based
- `totalPages <= 5`: 전부 표시
- `currentPage <= 2`: 0..4 + ellipsis + last
- `currentPage >= totalPages - 3`: 0 + ellipsis + last 5
- 그 외: 0 + ellipsis + (current±2) + ellipsis + last-1
- 렌더 시 페이지 번호는 `page + 1`, ellipsis는 UI에서 `...`

## 텍스트 UX (모바일 카피 줄바꿈)

긴 안내·배너 카피는 모바일에서 **의미 단위 줄바꿈**을 명시한다. (`SKILL.md` §텍스트 UX 요약)

### 대상

- `DomainGuideBanner` (`title`, `description`)
- 도메인 홈 상단·하단 안내 문구, 긴 섹션 부제 등 한 줄 카피

### 규칙

1. 카피는 해당 라우트 **`page.js` 파일 내부** 상수 (`ReactNode`)
2. 모바일만 줄바꿈: `<br className="sm:hidden" />`
3. `sm` 이상: `br` 숨김 → 데스크톱 한 줄
4. 끊는 위치: 절·구 호흡 (예: `…촬영전에` / `어떤 스타일로…`)
5. `DomainGuideBanner`에 `title={CONST}` / `description={CONST}` 전달

### 예시 — food 식단 배너

```javascript
const FOOD_MEAL_CALC_BANNER_TITLE = (
  <>
    식단계산기로 체계적인 <br className="sm:hidden" />
    관리를 해보세요
  </>
);

const FOOD_MEAL_CALC_BANNER_DESCRIPTION = (
  <>
    키·체중·활동량만 입력하면 <br className="sm:hidden" />
    하루 맞춤 탄·단·지 목표를 바로 확인할 수 있어요.
  </>
);
```

### 금지

- 설명 `p`에 `h-[22px]` 등 고정 높이
- 모바일/데스크톱 문구 이중 관리 (문장 동일 + `br`만 조건부)
- 카피를 `components/page/*Content.js` 등으로 분리 (§7 위반)

## shadcn

- `components.json`: style default, neutral, cssVariables
- 클래스 병합: `cn(...)` from `@/lib/utils.js`
