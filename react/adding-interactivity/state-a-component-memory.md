# State: A Component's Memory

각각의 컴포넌트 인스턴스는 고유의 데이터를 기억하기 하기 위한 "state (variable)" 를 가질 수 있다.

**You will learn**

- How to add a state variable with the `useState` Hook
- What pair of values the `useState` Hook returns
- How to add more than one state variable
- Why state is called local

[공식문서](https://react.dev/learn/state-a-components-memory)

---

## When a regular variable isn't enough

컴포넌트 내에 선언한 ~"Local Variable"~ 은 렌더링과 다음 렌더링 사이에서 값이 보존되지 않고 지역변수의 값이 변하더라도 리-렌더링이 발생하지 않는다.

새로운 데이터를 이용해 컴포넌트를 재실행(리-렌더링)할 때 필요한 요소들이 있다

- 데이터 보존 (**Retain**)
- 컴포넌트 재실행 (**Trigger, Re-Rendering**)

이 2가지 요건을 지킬 수 있도록 하는 변수를 제공하는 훅이 `useState`. 이 훅은 2가지를 리턴하는데 하나는 **렌더링 사이에서 데이터 값이 보존되는** `state variable`이고 나머지는 **상태 변수의 값을 업데이트하고 리액트가 해당 컴포넌트를 재실행하도록 하는** `state setter function` 이다.

## Adding a state variable

```jsx
import { useState } from "react";

// [state-variable, setter function]
const [index, setIndex] = useState(0);
```

Example

```jsx
import { useState } from "react";
import { sculptureList } from "./data.js";

/*  sculptureList
 *  { name, artist, description, url, alt }
 */

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const sculpture = sculptureList[index % sculptureList.length];
  const handleClick = () => setIndex(index + 1);
  return (
    <div>
      <button onClick={handleClick}>Next</button>
      <h2>
        {sculpture.name} by {sculpture.artist}
      </h2>
      <h3>
        {(index % sculptureList.length) + 1} of {sculptureList.length}
      </h3>
      <img
        src={sculpture.url}
        alt={sculpture.alt}
      />
      <p>{sculpture.description}</p>
    </div>
  );
}
```

### Meet your first Hook

모든 리액트 훅은 "`use`" 로 시작한다. ( "`use`" 로 시작하는 함수들은 리액트 훅으로 봐도 무방하다 ) 리액트 훅은 오직 리액트가 렌더링하는 도중에 이용 가능한 함수들이다.

(by ChatCPT) 리액트 훅은 함수 **컴포넌트에서 상태 관리, 생명 주기 이벤트, 컨텍스트 등과 같은 리액트의 다양한 기능을 사용할 수 있도록 도와주는 특별한 함수들**이다. 이러한 훅을 사용하면 클래스 컴포넌트 없이도 함수 컴포넌트에서 상태를 관리하고 다양한 기능을 사용할 수 있다. ( "훅을 걸다" 라는 표현은 특정 기능을 활용하거나 연결하여 사용한다는 의미이다 ) 또한, 코드를 간결하고 재사용 가능하게 만드는 데에 기여한다.

❗️**리액트 훅은 항상 컴포넌트 함수 최상단 또는 커스텀 훅에서만 호출해야 한다.** ( 리액트 훅을 사용할 때 지켜야할 규칙들 ) 조건문 또는 반복문 내에서 사용하면 안된다.

### Anatomy of `useState`

컴포넌트가 여러번의 렌더링에서 특정 데이터를 보존하기를 원할 때 `useState`를 호출하면 된다.

useState 훅은 상태 변수에 대한 초기값을 인자로 받고 상태변수와 세터함수를 요소로 가지는 하나의 배열을 리턴한다.( `[state variable, state setter function]` )

**Example**

```jsx
export default function Button() {
  const [index, setIndex] = useState(0);
  return <button onClick={() => setIndex(index + 1)}>Click</button>;
}
```

1. **최초 실행** : 훅의 인자로 넘긴 초기값이 0이기 때문에 `[0, setIndex]`를 리턴한다.
2. **(클릭)세터 함수 호출** : `setIndex(1)` 호출로 리액트는 index (상태 변수)의 값을 1로 저장하고 렌더링을 발생시킨다.
3. **컴포넌트 함수 재실행** : 코드는 여전히 `useState(0)` 이지만 리액트가 저장하고 있는 값은 1이기 때문에 `[1, setIndex]`를 리턴한다.

📜 `useState` 훅 사용할 때의 "convention" `const [something, setSomething] = useState(initial)`

## Giving a component multiple state variable

하나의 컴포넌트가 가질 수 있는 상태 변수의 개수는 정해진 제한이 없다.

- 데이터가 서로 관련 없는 일을 수행한다면 **별도의 상태 변수들로 관리**하는 것이 좋다
- ( 파생시켜서 ) 하나의 데이터로 관리할 수 있는 정보들이라면 **하나의 상태 변수로 관리**하는 것이 좋다.
- 서로 관련 없는 데이터지만 같이 변하는 데이터라면 **하나의 객체로 상태 변수를 관리**하는 것이 좋다

```jsx
import { useState } from "react";
import { sculptureList } from "./data.js";

export default function Gallery() {
  const [index, setIndex] = useState(0);
  const [isShow, setIsShow] = useState(false);

  const handleNextClick = () => setIndex(index + 1);
  const handleShowClick = () => setIsShow(!isShow);

  const sculpture = sculptureList[index % sculptureList.length];
  return (
    <div>
      <button onClick={handleNextClick}>Next</button>
      <h2>
        {sculpture.name} by {sculpture.artist}
      </h2>
      <h3>
        {(index % sculptureList.length) + 1} / {sculptureList.length}
      </h3>
      <button onClick={handleShowClick}>{isShow ? "Hide" : "Show"}</button>
      {isShow && <p>{sculpture.description}</p>}
      <img
        src={sculpture.url}
        alt={sculpture.alt}
      />
    </div>
  );
}
```

## State is isolated and private

각각의 리액트 컴포넌트 인스턴스는 **개별적으로 상태 변수를 관리**한다. 만약 동일한 컴포넌트 함수에 대해 N개의 컴포넌트 인스턴스를 생성했을 때 각 인스턴스들은 고유의 상태 변수를 가지고 관리한다.

**상태 변수는 그것이 선언된 ( `useState`로 ) 컴포넌트에서만 접근 가능하며 수정 가능하다.** 부모 컴포넌트도 자식 컴포넌트에 속한 상태 변수를 수정할 수 없다. 이로 인해 다른 컴포넌트에 영향 끼치는 것 없이 상태 변수를 추가하거나 삭제할 수 있다.

만약 다른 컴포넌트에서의 상태 변수에 대한 변화를 동기화 시키는 것을 원할 경우 가장 가까이 있는 공통 부모 컴포넌트에서 상태 변수를 관리하도록 해야 한다.
