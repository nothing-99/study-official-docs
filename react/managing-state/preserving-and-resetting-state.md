# Preserving and Resetting State

React는 (Render Tree)UI-tree를 기반으로 각각의 state들이 어떤 component에 속하는지 추적한다. 그리고 개발자는 렌더링 사이에서 특정 state를 보존할지 혹은 새롭게 만들지 결정할 수 있다.

> **You will learn**
> * When React chooses to preserve or reset the state
> * How to force React to reset component’s state
> * How keys and types affect whether the state is preserved

➭ [공식문서](https://react.dev/learn/preserving-and-resetting-state)

---
## State is tied to a position in the render tree
한 Component에 State를 작성할 때 컴포넌트 내부에 State가 존재한다고 생각할 수 있지만 실제로는 그렇지 않다. Component 내부가 아닌 **React 에 각각의 State가 존재한다.** 

**React는 State를 빌드 과정에서 생성된 UI-tree(Render-tree)에서 맞는 컴포넌트를 연결한다**

**Code1**
```jsx
// App.jsx
export default function App() {
  return (
    <div>
      <Counter />
      <Counter />
    </div>
  );
}
```

```jsx
// Counter.jsx
function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

**Render tree**
```
- App
	- Counter count 0
	- Counter count 0
```

- **렌더 트리 기준으로 각각의 컴포넌트는 고유의 state를 가진다.** 위 예시처럼 `Counter`를 나란히 위치시켜도 각각의 `Counter`컴포넌트는 고유의 state를 가진다. { `score`, `hover` }
- 동일한 컴포넌트 함수를 사용했더라도 렌더 트리 상 생성된 컴포넌트들은 서로에게 영향을 주지 않는다.
- 한쪽의 "Add one" 버튼을 클릭해보면 한쪽의 숫자만 올라간다. 
- [코드 결과](https://codesandbox.io/s/79lrxv?file=%2Fsrc%2FApp.js&utm_medium=sandpack) 

**Code2**
```jsx
export default function App() {
  const [showB, setShowB] = useState(true);
  return (
    <div>
      <Counter />
      {showB && <Counter />} 
      <label>
        <input
          type="checkbox"
          checked={showB}
          onChange={e => {
            setShowB(e.target.checked)
          }}
        />
        Render the second counter
      </label>
    </div>
  );
}
```

- **리액트는 렌더트리에서 state와 결합해야할 적절한 컴포넌트를 찾는다.** 
- 2번째 Counter의 버튼을 클릭하면 카운트가 되고 체크박스를 2번 클릭하면 사라졌다가 다시 나타난다. 이때!! 이전의 카운트가 아닌 초기값으로 설정된 것을 볼 수 있다. **이것은 리액트가 가지고 있던 count state와 결합할 컴포넌트가 렌더트리에서 사라졌기 때문이다.**
- **Render-tree에 결합해야할 컴포넌트가 존재하는 한 리액트는 해당 state를 가지고 있는다.**
- [코드 결과](https://codesandbox.io/s/g4zcfc?file=%2Fsrc%2FApp.js&utm_medium=sandpack) 

## Same component at the same position preserves state
**Render-tree 에서 동일한 위치, 동일한 컴포넌트에 대한 State는 리액트에 의해 보존된다.**

**Code1** [CodeSandbox](https://codesandbox.io/s/6f2zp8?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
```jsx
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <Counter isFancy={true} /> 
      ) : (
        <Counter isFancy={false} /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

❗️Reat가 State와 Component를 연결할 때 작성한 JSX가 아닌 **생성된 Render-tree를 기준으로 한다.** ( ~Render-tree를 기준으로 생각하자~ )

## Different components at the same position reset state
**Render-tree에서 Subtree 구조가 변경되면 가지고 있던 State는 리셋된다.** State와 연관된 컴포넌트가 Render-tree에서 잠깐이라도 사라지고 생성되더라도 State도 같이 제거되고 다시 생성된다.

**Code1** [CodeSandbox](https://codesandbox.io/s/sxz2lq?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
```jsx
import { useState } from 'react';

export default function App() {
  const [isPaused, setIsPaused] = useState(false);
  return (
    <div>
      {isPaused ? (
        <p>See you later!</p> 
      ) : (
        <Counter /> 
      )}
      <label>
        <input
          type="checkbox"
          checked={isPaused}
          onChange={e => {
            setIsPaused(e.target.checked)
          }}
        />
        Take a break
      </label>
    </div>
  );
}

function Counter() {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

- 체크박스를 체크하고 풀 경우 Render-tree의 구성요소가 변경되기 때문에 State값은 초기화된다.
- `div - Counter: 3` ➜ `div - p` ➜  `div - Counter: 0`

**Code2** [CodeSandbox](https://codesandbox.io/s/rx57gr?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
```jsx
import { useState } from 'react';

export default function App() {
  const [isFancy, setIsFancy] = useState(false);
  return (
    <div>
      {isFancy ? (
        <div>
          <Counter isFancy={true} /> 
        </div>
      ) : (
        <section>
          <Counter isFancy={false} />
        </section>
      )}
      <label>
        <input
          type="checkbox"
          checked={isFancy}
          onChange={e => {
            setIsFancy(e.target.checked)
          }}
        />
        Use fancy styling
      </label>
    </div>
  );
}

function Counter({ isFancy }) {
  const [score, setScore] = useState(0);
  const [hover, setHover] = useState(false);

  let className = 'counter';
  if (hover) {
    className += ' hover';
  }
  if (isFancy) {
    className += ' fancy';
  }

  return (
    <div
      className={className}
      onPointerEnter={() => setHover(true)}
      onPointerLeave={() => setHover(false)}
    >
      <h1>{score}</h1>
      <button onClick={() => setScore(score + 1)}>
        Add one
      </button>
    </div>
  );
}
```

- 체크박스를 체크하고 풀 경우 Render-tree의 구성요소가 변경되기 때문에 State값은 초기화된다.
- `div - section - Counter: 3` ➜ `div - div - Counter: 0`

❗️**컴포넌트 함수는 항상 Top-Level 로 작성해야 한다.**

**Code** [CodeSandbox](https://codesandbox.io/s/8kntyv?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
```jsx
import { useState } from 'react';

export default function MyComponent() {
  const [counter, setCounter] = useState(0);

  function MyTextField() {
    const [text, setText] = useState('');

    return (
      <input
        value={text}
        onChange={e => setText(e.target.value)}
      />
    );
  }

  return (
    <>
      <MyTextField />
      <button onClick={() => {
        setCounter(counter + 1)
      }}>Clicked {counter} times</button>
    </>
  );
}
```

- 버튼을 클릭 후 리렌더링 되더라도 Render-tree 상 MyTextFeild의 위치는 동일하기 때문에 text-State 가 보존되리라 생각할 수 있지만 그렇지 않다.
- setting function 의 호출로 myComponent가 재실행되면서 MyTextField가 새로 정의되면서 이 새로운 함수로 컴포넌트를 생성하기 때문에 다른 컴포넌트라고 볼 수 있다. 고로 text-State는 보존되지 않는다.
- ~"Different component in the Same position"~

## Resetting state at the same position
React는 기본적으로 Same Component, Same Position 에 대한 컴포넌트의 State를 보존한다. 이 기본적인 동작말고 리셋하는 것을 원할 때 사용할 수 있는 방법이 있다.
1. 컴포넌트가 다른 위치에 가도록 한다.
2. `key`를 이용한다.

[CodeSsanbox - 기본코드](https://codesandbox.io/s/rn3mk9?file=%2Fsrc%2FApp.js&utm_medium=sandpack)

### Option 1: Rendering a component in different positions
*Use Same Position*

**Code** [CodeSandbox - Full](https://codesandbox.io/s/g9f9r8?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
```jsx
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      // first node
      {isPlayerA &&
        <Counter person="Taylor" />
      }
      // second node
      {!isPlayerA &&
        <Counter person="Sarah" />
      }
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

### Option 2: Resetting state with a key
*Use Same Component*

리액트는 동일한 부모 컴포넌트 아래에 있는 자식 컴포넌트들을 구분할 때 `key`를 사용한다. 다시 말하면 `key`가 다른 컴포넌트들은 엄연히 다른 컴포넌트이다!!! 

**Code** [CodeSandbox](https://codesandbox.io/s/wvssxq?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
```jsx
import { useState } from 'react';

export default function Scoreboard() {
  const [isPlayerA, setIsPlayerA] = useState(true);
  return (
    <div>
      {isPlayerA ? (
        <Counter key="Taylor" person="Taylor" />
      ) : (
        <Counter key="Sarah" person="Sarah" />
      )}
      <button onClick={() => {
        setIsPlayerA(!isPlayerA);
      }}>
        Next player!
      </button>
    </div>
  );
}
```

### Resetting a form with a key

**Code1** [CodeSandbox - Full](https://codesandbox.io/s/ydhypj?file=%2Fsrc%2FApp.js&utm_medium=sandpack)
**Code2**
```jsx
// App.js
<Chat key={to.id} contact={to} />
```

- 새로운 State를 가지도록 하기 위해 리액트가 다른 컴포넌트로 인지하도록 하면 된다. `key`

---
## Deep Dive : Preserving state for removed components
제거된 컴포넌트의 State를 유지하는 방법이 존재한다.
1. 현재 보여야할 컴포넌트를 제외한 나머지 컴포넌트는 CSS를 이용해 보이지 않도록 한다. 이것은 Render-tree 상에는 해당 컴포넌트가 존재하기 때문에 화면에서는 보이지 않지만 State가 삭제되지 않는다. 
2. 공통 부모에서 State를 관리하는 방법이 있다. 이것이 가장 흔한 방법이다. 자식 컴포넌트가 삭제되더라도 해당 컴포넌트에서 생성된 (관리하는) State가 아니기 때문에 삭제되지 않는다.
3. LocalStorage와 같은 React 외부 소스를 이용하는 것도 하나의 방법이다. LocalStorage에 State를 저장해두면 해당 컴포넌트가 삭제되더라도 LocalStorage에서 불러오도록 하면 된다.
---
#react.dev/managing-state/preserving-and-resetting-state#