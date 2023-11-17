# Keeping Components Pure

**You will learn**

- What purity is and how it helps you avoid bugs
- How to keep components pure by keeping changes out of the render phase
- How to use Strict Mode to find mistakes in your components

[공식문서](https://react.dev/learn/keeping-components-pure)

---

## Purity: Components as formulas

==**pure function**== ~~( from chatGPT )~~
외부에 존재하는 상태나 변수의 값에 영향을 주지 않고 오로지 받는 인자를 이용해 결과를 출력하는 함수를 **"pure function (순수 함수)"** 라고 한다. 동일한 입력에 대해서 항상 동일한 결과를 출력하기 때문에 결과 예측이 가능하도록 한다. 이로인해 코드이해, 유지보수, 디버깅 및 테스트에 도움을 준다

```js
// simple example
function Add(a, b) {
  return a + b;
}
```

리액트는 개발자가 리액트 컴포넌트를 순수함수로 작성했음을 가정한다. 순수함수로 작성한 리액트 컴포넌트는 **"항상 동일한 props 에 대해서 동일한 JSX 를 리턴한다"**

```jsx
// Hello Component ( pure function )
function Hello({ name }) {
  return <h1>Hello, {name}</h1>;
}
```

## Side Effects: (un)intended consequences

리액트 컴포넌트는 항상 렌더링이 시작되기 전에 선언된 객체 또는 변수의 값을 변경하지 않고 JSX를 리턴해야 한다( 컴포넌트 함수 실행 중에 선언된 변수의 값은 가능,, 실행할 때마다 지정한 값으로 초기화되기 때문에 결과 예측이 가능하다 ).

( 리액트 공식문서에는 "Strict Mode" 가 적용되어 각 컴포넌트 함수를 두번 호출하기 때문에 2, 4, 6 으로 렌더링된 결과를 볼 수 있다 )

```jsx
// not strict mode
let count = 0;
function Counter() {
  // Bad
  count += 1;
  return <div>#{count}</div>;
}
function App() {
  return (
    <React.Fragment>
      <Counter />
      <Counter />
      <Counter />
    </React.Fragment>
  );
}
```

```
Output
#1
#2
#3
```

- Counter 컴포넌트를 동일한 props 로 여러번 호출하지만 모두 다른 결과를 리턴하고 있다. 이것은 결과 예측에 어려움을 주는 형태이다.
- 컴포넌트 내에서 외부 변수를 변경하는 게 아닌 변경된 변수의 값을 props 를 통해 전달받고 JSX를 리턴하도록 변경한다. ( **동일한 input에 대해서 항상 같은 output를 내놓는 "pure function"의 특징을 지키도록 변경** )

```jsx
function Counter({ count }) {
  return <div>{count}</div>;
}
function App() {
  return (
    <>
      <Counter count={1} />
      <Counter count={2} />
      <Counter count={3} />
    </>
  );
}
```

## Local mutation: Your component's little secret

**"순수함수는 함수 스코프 외부에 있거나 함수 호출 전에 생성된 변수 혹은 객체의 값을 직접적으로 변경시키지 않는다"**

렌더링 이전에 생성된 변수 혹은 객체의 값을 변경시키지 않지만 렌더링 도중에 생성된 변수 혹은 객체의 값은 상관없다.

```jsx
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

export default function TeaGathering() {
  // TeaGatering 실행 도중 선언 및 할당된 변수
  let cups = [];
  for (let i = 1; i <= 12; i++) {
    // cups 배열에 (JSX)요소 추가
    cups.push(
      <Cup
        key={i}
        guest={i}
      />
    );
  }
  return cups;
}
```

- `TeaGathering` 함수를 실행하면(~렌더링~) 실행도중에 `cups` 배열을 생성하고 반복문을 통해 JSX요소를 추가하고 있다. 위의 규칙을 지키고 있다.

## Where you can cause side effects

"Updating the screen", "Starting an animation", "changing the data" 등 렌더링 외의 시간에 발생하는 변화들을 **Side Effects** 라 한다.

보통 side-effects는 사용자와의 인터렉션을 통해 실행되는 이벤트 핸들러 내에서 처리된다. 비록 이벤트 핸들러 함수는 리액트 컴포넌트안에 정의되지만 렌더링 도중에 실행되지는 않는다. 그렇기 때문에 리액트 컴포넌트 함수와 달리 "pure function" 으로 작성하지 않아도 된다.

만약 side-effect를 처리하기 위한 적절한 이벤트를 찾을 수 없다면 `useEffect`를 사용할 수 있다. **하지만 side-effect 처리를 위한 `useEffect`는 이벤트 핸들러 또는 다른 옵션들을 우선적으로 고려하고 정 없을 때 사용하는 것이 좋다**

---

## Deep Dive : Detecting impure calculations with StrictMode

리액트 렌더링 동안에 읽을 수 있는 3종류의 input이 존재하는데 각각 `props` `state` `context` 이다.

user-input으로 인해 어떤 값의 변경을 원할 때 특정 변수의 값을 직접 변경하는 것은 옳지 않다. `setState` 호출을 통해 업데이트된 state값을 이용한 새로운 렌더링을 발생시켜야 한다. ( ~렌더링 도중 렌더링 이전에 존재한 변수 혹은 객체의 값을 변경하면 안된다~ )

리액트는 **"Strict Mode"** 를 제공하는데 **각 컴포넌트를 2번씩 실행시킨다**. ( 렌더링 도중 렌더링 이전에 존재한 변수 혹은 객체의 값을 변경하면 안된다 ) 규칙이 지켜지는지 쉽게 확인할 수 있다.

Strict-Mode는 개발 버전에서만 동작하기 때문에 배포 버전에서 이로인한 속도 저하를 걱정하지 않아도 된다. `<React.StrictMode>` 를 이용해 Wrapping을 하면 Strict-Mode를 이용할 수 있으며 이를 디폴트로 제공하는 프레임워크도 존재한다. ~~일단 CRA는 지원하는 것 같음~~

## Deep Dive : Why does React care about purity?

- 컴포넌트를 서버와 같은 **다른 환경에서도 사용**할 수 있다. ( same input => same output )
- 인풋 값의 변화가 없을 경우 컴포넌트 렌더링을 생략함으로써 **성능 향상**을 불러온다.
- 렌더링 도중 데이터에 변화가 있을 때 **기존 렌더링을 멈추고** 새로운 렌더링을 시작할 수 있다. 순수 함수는 외부 데이터 값의 변화를 발생시키지 않고 단순 계산하는 함수이기 때문에 중간에 멈추더라도 데이터 롤백을 고려하지 않아도 된다.
