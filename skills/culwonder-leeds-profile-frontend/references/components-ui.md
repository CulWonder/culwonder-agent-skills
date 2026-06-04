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

## shadcn

- `components.json`: style default, neutral, cssVariables
- 클래스 병합: `cn(...)` from `@/lib/utils.js`
