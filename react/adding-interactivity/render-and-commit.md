# Render and Commit

리액트 컴포넌트가 브라우저 화면에 표시되기 위해서는 **"리엑트에 의한 렌더링"** 과정을 거쳐야만 한다.

1. **"Triggering"** a render : 고객의 주문을 주방에 전달하는 것
2. **"Rendering"** the component : 주문 받은 요리를 완성
3. **"Committing"** to the DOM : 요리 전달

**You will learn**

- What rendering means in React
- When and why React renders a component
- The steps involved in displaying a component on screen
- Why rendering does not always produce a DOM update

[공식문서](https://react.dev/learn/render-and-commit)

---

## Step 1: Trigger a render

렌더링을 발생시키는 2가지

- 컴포넌트의 **최초 실행**
- 컴포넌트의 **`state`의 업데이트**

### Initial render

( 루트 노드가 될 ) target DOM node 를 인자로 받아 `createRoot` 를 실행하고 리턴된 루트 객체의 `render` 메서드를 호출함으로써 **"initial rendering"** 이 발생한다.

```jsx
import { createRoot } from "react-dom/client";
import App from "./App";

// 1. createRoot()
const root = createRoot(document.getElementById("root"));
// 2. render()
root.render(<App />);
```

### Re-renders when state updates

상태 변수 업데이트를 위해`useState` 훅의 리턴값으로 받는 **setter function** 호출을 하면 렌더링을 "예약한다". 예약한다는 것은 세터함수 호출 직후에 리-렌더링이 발생하는 것이 아니기 때문이다. ( 예약된 렌더링은 적절한 시점에 수행된다 : 이는 리액트의 업데이트 프로세스를 관리하고 불필요한 렌더링을 방지하여 성능을 향상시킨다 by openai )

## Step 2: React renders your components

(in React) **"Rendering" 은 리액트가 컴포넌트를 실행시키는 것과 같다.**

- Initial Render : 루트 컴포넌트 실행
- the other Render : 상태 변수 업데이트가 발생한 컴포넌트 실행

하나의 컴포넌트 실행 후 리턴된 JSX에 컴포넌트가 존재할 경우 그 컴포넌트도 실행한다. 이 과정은 재귀적으로 진행된다.

❗️렌더링은 항상 [Pure Calculation](https://react.dev/learn/keeping-components-pure)

- 항상 동일한 입력에 대해서 동일한 출력을 해야 한다.
- 실행되기 전에 존재했던 데이터에 대한 변경이 발생하면 안된다.
- 리액트는 "Strict Mode" 를 지원함으로써 컴포넌트를 2번 연속 실행함으로써 impure function 으로 인해 발생할 수 있는 문제를 마주할 수 있도록 한다.

## Step 3: React commits changes to the DOM

렌더링(컴포넌트 호출) 이후 리액트는 DOM 을 수정한다. ( 리액트 렌더링은 DOM을 수정하는 단계는 아니네...? )

- Initial Render : `appendChild()` DOM API 호출을 통해 모든 DOM node 를 추가한다.
- Re-Renders : DOM에 필요한 (렌더링 중에 계산)최소한의 작업만을 적용한다.

리액트 렌더링 결과 "Virtual DOM(가상돔)" 이 생성된다. 리-렌더링은 렌더링 이전에 존재했던 가상돔과 렌더링 이후에 생성된 가상돔을 비교하여 실제DOM에 반영할 최소한의 작업을 계산하는 과정을 포함한다. 그 후 "Commit" 단계에서 렌더링 과정에서 계산한 최소한의 작업을 DOM에 반영한다. ( 변경된 부분만을 업데이트하기 때문에 성능 향상을 가져온다 )

## Epilogue: Browser paint

Rendering(컴포넌트 호출)과 Commit(돔 업데이트)과정을 거친 후 브라우저는 페인팅을 진행한다.

---

## Deep Dive : Optimizing performance

- [Optimizing Performance](https://legacy.reactjs.org/docs/optimizing-performance.html) ( 성능 향상을 위한 여러 가지 방법들 )
- 업데이트 되는 컴포넌트가 렌더 트리 상단에 위치하는 경우 해당 컴포넌트 아래에 있는 모든 컴포넌트가 재실행되는 데 성능 측면에서 좋은 것은 아니다.
- **하지만 너무 초장부터 성능 최적화를 위한 코드를 작성하는 것은 코드의 복잡성을 증가시킬 수 있기 때문에 성능 문제가 발생할 경우 최적화를 적용하자!!**
