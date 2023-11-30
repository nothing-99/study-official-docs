# State as a Snapshot

`State`는 일반 자바스크립트 객체 또는 변수처럼 동작하지만 **"스냅샷"** 에 더 가깝다

**You will learn**

- How setting state triggers re-renders
- When and how state updates
- Why state does not update immediately after you set it
- How event handlers access a “snapshot” of the state

[공식문서](https://react.dev/learn/state-as-a-snapshot)

---

## Setting state triggers renders

"Stter 함수는 리-렌더링을 **요청한다**"
➜ 이벤트에 대한 반응으로 UI 요소를 변경하기 위해서는 Setter 함수를 호출해야 한다!!!

**Example**

```jsx
function App() {
  const [isSent, setIsSent] = React.useState(false);
  const [msg, setMsg] = React.useState("");
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("[message]" + msg);
    setIsSent(true);
    setMsg("");
  };
  const handleChange = (e) => {
    setMsg(e.target.value);
  };

  if (isSent) {
    return <h1>Sent</h1>;
  }
  return (
    <form onSubmit={handleSubmit}>
      <textarea
        placeholder="Message"
        value={msg}
        onChange={handleChange}
      />
      <button type="submit">Send</button>
    </form>
  );
}
```

**Send 버튼 클릭**

1. "submit" 이벤트 발생
2. 이벤트 핸들러 함수 실행 `handleSubmit`
   1. `setIsSent` 실행 : `isSent`를 `true`로 설정하고 ~렌더링을 요청~
   2. `setMsg` 실행 : `msg`를 `""`로 설정하고 ~렌더링을 요청~
3. 리액트는 새로운 값을 이용해 리-렌더링을 수행

## Rendering takes a snapshot in time

렌더링은 리액트가 컴포넌트 함수를 실행하는 것이며 그 결과 이벤트 핸들러와 같은 로직을 포함하고 있는 JSX를 얻는다. 이것은 그 순간의 **"UI snapshot"** 과 같으며 리액트는 이것과 일치하도록 DOM 업데이트를 진행한다.

- **컴포넌트 함수 실행**
  - 각 렌더링은 ~그 순간에 가지고 있는 (받는) 상태 변수를 이용해~ { props, event handlers, local variables etc } 를 계산한다.
- **JSX snapshot 리턴**
- **DOM 업데이트**

컴포넌트에서 `useHook`훅을 통해 정의한 상태 변수는 함수 실행 종료 후 사라지는 일반 변수와는 다르다. 상태 변수들은 각각의 컴포넌트가 아닌 **리액트 자체에 저장돼 있다.**

만약 세터 함수의 호출로 리렌더링이 발생한다면 컴포넌트가 재실행되고 `useHook`을 호출할 때 **저장돼 있는 state 값을 넘겨준다.**

- Setter 함수 호출 : 리액트는 state 값을 업데이트 후 렌더링 요청
- 렌더링 시작 : 컴포넌트 함수 실행
- `useHook` 실행 : 리액트에 저장돼 있는 state 값 리턴

**Example**

```jsx
export default function App() {
  const [number, setNumber] = useState(0);
  const handleClick = () => {
    setNumber(number + 1); // (1)
    setNumber(number + 1); // (2)
    setNumber(number + 1); // (3)
  };
  return (
    <>
      <h1>{number}</h1>
      <button onClick={handleClick}>+3</button>
    </>
  );
}
```

- (1) (2) (3) 은 함수 실행 중에 접근 가능한 `number`의 값을 변경하는 것이 아닌 리액트에 저장된 `number` state의 값을 업데이트 하는 것이다. 그렇기 때문에 함수 실행 중에 모든 `number`의 값은 `0` 이다.

**Exec Snapshot**

```jsx
// 첫번째 클릭
const handleClick = () => {
  setNumber(0 + 1);
  setNumber(0 + 1);
  setNumber(0 + 1);
};
```

**Exec Snapshot**

```jsx
// 두번째 클릭
const handleClick = () => {
  setNumber(1 + 1);
  setNumber(1 + 1);
  setNumber(1 + 1);
};
```

**Exec Snapshot**

```jsx
// 세번째 클릭
const handleClick = () => {
  setNumber(2 + 1);
  setNumber(2 + 1);
  setNumber(2 + 1);
};
```

## State over time

**Example**

```jsx
export default function App() {
  const [number, setNumber] = useState(0);

  const handleClick = () => {
    setNumber(number + 5);
    setTimeout(() => {
      alert(number);
    }, 5000);
  };

  return (
    <>
      <h1>{number}</h1>
      <button onClick={handleClick}>+5</button>
    </>
  );
}
```

**Exec Snapshot**

```jsx
// 첫번째 클릭
setNumber(0 + 5);
setTimeout(() => {
  alert(0);
}, 5000);
```

**Exec Snapshot**

```jsx
// 두번째 클릭
setNumber(5 + 5);
setTimeout(() => {
  alert(5);
}, 5000);
```

- **하나의 JSX snapshot 에서는 절대로 상태 변수의 값이 변하지 않는다.** ( 말 그대로 snapshot 이기 때문에!! ) 만약 이벤트 핸들러 함수에 상태 변수를 이용하는 비동기 코드가 있다면 **스케쥴링된 시점의** 상태 변수의 값을 이용한다. ( ~이벤트 핸들러 함수를 포함하고 있는 JSX snapshot 에 저장된 상태 변수 값 이용하는 걸로 생각하면 될 듯?~ )
- 상태 변수를 사용하는 비동기 함수와 State Setter 함수를 포함하고 있는 이벤트 핸들러 함수가 실행되었다고 가정해보자.
  - 비동기 함수는 setter 함수가 어디에 있든 setter 함수 호출로 인한 state 변수의 값 변동을 걱정하지 않아도 된다. **JSX snapshot 내에서는 절대로 state 값이 변하지 않기 때문이다.** 비동기 함수를 호출하는 이벤트 핸들러 함수가 연결된 JSX snapshot 에 저장된 state 변수를 이용한다.
  - 비동기 함수가 스케쥴링 되었지만 실행되기 전에 리렌더링이 완료돼 리액트가 새로운 JSX snapshot 을 얻게 되더라도 상관없다. **비동기 함수의 실행이 아닌 스케쥴링 시점에 얻은 JSX snapshot 의 state 를 이용하기 때문이다.**
- 리렌더링을 하면 새로운 JSX snapshot 를 얻고 새로운 이벤트 핸들러가 연결된다고 보면 된다.

==하나의 "JSX Snapshot" 에는 하나의 "State Snapshot" 이 존재한다!!!!==
