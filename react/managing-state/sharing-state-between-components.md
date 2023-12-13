# Sharing State Between Components

서로 다른 컴포넌트에 존재하는 state를 함께 업데이트하기를 원할때 각 컴포넌트에서 state를 삭제하고 가장 가까운 공통 조상 컴포넌트까지 올려야 한다 ➭ ==**"lifting state up"**==

**You will learn**

- How to share state between components by lifting it up
- What are controlled and uncontrolled components

➭ [공식문서](https://react.dev/learn/sharing-state-between-components)

---

## Lifting state up by example

**Start code**

```jsx
import { useState } from "react";

function Panel({ title, children }) {
  const [isActive, setIsActive] = useState(false);
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? <p>{children}</p> : <button onClick={() => setIsActive(true)}>Show</button>}
    </section>
  );
}

export default function Accordion() {
  return (
    <>
      <h2>Almaty, Kazakhstan</h2>
      <Panel title="About">With a population of about 2 million, Almaty is Kazakhstan's largest city. From 1929 to 1997, it was its capital city.</Panel>
      <Panel title="Etymology">
        The name comes from <span lang="kk-KZ">алма</span>, the Kazakh word for "apple" and is often translated as "full of apples". In fact, the region surrounding Almaty is thought to be the
        ancestral home of the apple, and the wild <i lang="la">Malus sieversii</i> is considered a likely candidate for the ancestor of the modern domestic apple.
      </Panel>
    </>
  );
}
```

**Render Tree**

```
- Accordion
  - Panel
  - Panel
```

- 각각의 `Panel` 컴포넌트는 서로 어떠한 영향도 주지 않는다. 첫번째 Panel 의 버튼을 클릭했을 경우 문구가 뜨지만 두번째 Panel 에는 어떠한 일도 일어나지 않는다. 모든 버튼을 클릭하면 모든 문구가 뜬 상태로 나온다.
- 오직 하나의 패널의 문구만 뜨도록 만들어 본다. 즉, 하나의 패널의 버튼을 클릭하면 문구가 보이고 나머지 패널들은 버튼이 보이도록 만든다.
  1. **Remove** : `Panel` 컴포넌트에서 state를 제거한다.
  2. **Pass** : `Accordion` 컴포넌트( 가장 가까운 공통 조상 컴포넌트 )는 `Panel` 컴포넌트에 데이터 전달을 위해 하드코딩을 한다.
  3. **Add** : `Accordion` 컴포넌트( 가장 가까운 공통 조상 컴포넌트 )에 state를 추가하고 이벤트 핸들러 함수와 함께 `Panel` 컴포넌트에 전달한다.

### Step 1: Remove state from the child components

```jsx
// Remove
const [isActive, setIsActive] = useState(false);
```

```jsx
function Panel({ title, children, isActive }) { ... }
```

- isActive의 관리를 부모 컴포넌트에 넘긴다. `Panel`의 부모 컴포넌트는 isActive를 관리하며 `Panel`에 props를 통해 전달한다.
- 다시 말해 `Panel`은 더이상 isActive 값의 관리권한을 가지지 않는다. ( _no control_ )

### Step 2: Pass hardcoded data from the common parent

```jsx
<Panel isActive={true} ...>
```

- "lift state up" 을 위한 가장 가까운 공통 조상 컴포넌트를 찾아야 한다.

### Step 3: Add state to the common parent

```jsx
// Accordion Component
const [activeIndex, setActiveIndex] = useState(0);
```

- _Lifting state up_ 으로 state 의 관리 형태가 변할 수도 있다.
- `Accordion`이 하나의 패널만을 추적하도록 한다.

```jsx
// Accordion Component
<>
  <Panel
    isActive={activeIndex === 0}
    onShow={() => setActiveIndex(0)}
  >
    ...
  </Panel>
  <Panel
    isActive={activeIndex === 1}
    onShow={() => setActiveIndex(1)}
  >
    ...
  </Panel>
</>
```

- setActiveIndex는 `Accordion`에서 정의된 setting function 이기 때문에 다른 컴포넌트에서 직접적으로 호출은 불가능하다.
- **setting function 호출문을 포함한 함수를 props 로 넘겨야 한다.**

```jsx
function Panel({ title, children, isActive, onShow }) {
  return (
    <section className="panel">
      <h3>{title}</h3>
      {isActive ? <p>{children}</p> : <button onClick={onShow}>Show</button>}
    </section>
  );
}
```

- 공통 state를 공통 조상 컴포넌트로 올림으로써 조상 컴포넌트는 (관련된) 자식 컴포넌트들을 동시에 조절한다.
- boolean이 아닌 index(number)를 이용함으로써 오직 하나의 패널만을 활성화시킨다.
- setting function 실행문을 포함한 함수를 자식 컴포넌트에 넘김으로써 자식 컴포넌트가 간접적으로 부모 컴포넌트의 state 업데이트를 가능하게 한다.

## A single source of truth for each state

여러 컴포넌트에서 공유하는 state는 사용하는 컴포넌트들의 공통 조상 컴포넌트에서 해당 state를 관리하고 props를 통해 보내주는 형태로 코드를 작성해야 한다.

---

## Deep Dive : controlled and uncontrolled components

"controlled" 와 "uncontrolled" 는 딱 정해지기 보다는 상대적인 개념으로 보면 좋을 것 같다. props와 state 모두 사용하는 컴포넌트도 흔하다.

**controlled** 컴포넌트

- **props** 로 받는 데이터에 의해 리턴값이 많이 결정된다. props는 부모 컴포넌트로부터 받기 때문에 상대적으로 부모 컴포넌트가 영향을 많이 끼친다고 볼 수 있다.
- 부모 컴포넌트로 받는 props 가 많기 때문에 유연성은 극대화된다. 하지만 상대적으로 props 가 많을수록 부모 컴포넌트에서 넘겨야할 프로퍼티가 많기 때문에 쉽게 사용하기는 어렵다.

**uncontrolled** 컴포넌트

- **local state** 에 의해 리턴값이 많이 결정된다. props 보다는 컴포넌트의 state 를 많이 사용하기 때문에 상대적으로 부모 컴포넌트에 영향을 덜 받는다.
- 상대적으로 부모 컴포넌트의 영향을 덜 받기 때문에 어떠한 곳에든 쉽게 사용할 수 있다. 하지만 유연성은 떨어진다. 예를 들어 여러 컴포넌트를 일괄적으로 처리할 때 불리하다.

컴포넌트를 작성할 때 특정 데이터들을 props로 작성할 지 혹은 state로 작성할 지 생각하면서 하자. 추후에 언제든지 변경하고 리팩토링할 수 있다.
