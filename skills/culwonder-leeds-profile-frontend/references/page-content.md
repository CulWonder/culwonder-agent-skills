# 화면 전용 콘텐츠 (copy·정적 데이터)

SKILL.md §7의 상세. 에이전트는 화면 copy·정적 데이터를 다룰 때 이 문서를 읽는다.

### 7. 화면 전용 콘텐츠 (copy·정적 데이터)

카피, 탭 옵션, 섹션·가이드 본문(근육 리스트, 운동 설명 등)은 **반드시 해당 라우트의 `page.js` 파일 안에** 직접 작성한다.

핵심은 파일 이름이 아니다. `*Content.js`든 `*Data.js`든 `workoutGuideData.js`든 **화면과 분리된 파일이면 안 된다.**

| 허용 | 금지 |
|------|------|
| `page.js` 상단 `const` / `function` + 하단 `export default function …Page()` | `components/page/**/*Content.js` 신규 생성 |
| `page.js` **같은 파일** 안에 데이터 + 컴포넌트 (길어져도 OK) | 라우트 폴더 형제 `*Data.js`, `*Content.js`, `workoutGuideData.js` 등 **분리 파일** |
| 다른 라우트가 동일 데이터 필요 시 → **원본 `page.js`에서 named export** | organism/molecule이 가이드 데이터 파일을 직접 import |
| 전역 설정 (`apiPaths.js`, CDN URL 등) | UI 컴포넌트에 대량 copy만 몰아넣기 (데이터는 `page.js`, 마크업은 컴포넌트) |

**배치 (예외 없음)**

1. 데이터·헬퍼 → `src/app/(ui)/{route}/page.js` **파일 내부**
2. 재사용 UI → `organism` / `molecule` — **props로** `page.js`에서 데이터 전달
3. `components/page/{domain}/` — **여러 라우트가 공유**하는 config만 (예: `styleBySlug.js`)

**다른 라우트가 같은 가이드를 쓸 때**

- 데이터 원본은 **가이드 본문 `page.js` 한 곳**에만 둔다.
- 홈 미리보기 등은 그 `page.js`의 **named export**를 import (데이터를 다시 분리 파일로 빼지 않음).

```javascript
// ✅ workout-guide/page.js — 데이터·화면이 같은 파일
const workoutTabOptions = [/* … */];
const muscleListByPart = { /* … */ };

function getMuscleExercises(partId, name) { /* … */ }

export {
  getMuscleExercises,
  muscleListByPart,
  workoutTabOptions,
};

export default function HelperWorkoutGuidePage() {
  return (
    <HelperMuscleDetailPanel
      getMuscleExercises={getMuscleExercises}
      muscleListByPart={muscleListByPart}
      /* … */
    />
  );
}
```

```javascript
// ❌ page.js 옆 형제 파일로 분리
// workout-guide/workoutGuideData.js
// workout-guide/muscleListData.js

// ❌ components/page/helper/ 로 분리
// workoutGuideContent.js
```

---
