# Responding to Events

"click", "hover", "input" 등 interaction을 통해 실행되는 함수를 **event handler** 로써 JSX에 추가할 수 있다.

**You will learn**

- Different ways to write an event handler
- How to pass event handling logic from a parent component
- How events propagate and how to stop them

[공식문서](https://react.dev/learn/responding-to-events)

---

## Adding event handlers

1. 이벤트 핸들러 함수 **정의**
2. 함수 로직 **구현**
3. JSX 컴포넌트에 "prop" 으로 **전달**

```jsx
export default function Button() {
  // 1. define function
  const handleClick = () => {
    // 2. implement logic
    alert("Clicked");
  };

  return (
    // 3. pass it as a prop
    <button onClick={handleClick}>click</button>
  );
}
```

**이벤트 핸들러 함수에 대한 관례**

- 컴포넌트 내부에 정의하기
- 함수 이름은 `handle`로 시작해 이벤트 이름이 뒤따르도록 작성하기 `onClick={handleClick}` `onMouseEnter={handleMouseEnter}`

다른 방법으로도 이벤트 핸들러를 설정할 수 있다. 모두 동일한 동작을 하고 경우에 따라 inline 방식이 편하기도 하다. ( 매우 간단한 동작의 경우 inline 으로 작성해도 될 듯? )

```jsx
<button
  onClick={function handleClick() {
    alert("Clicked");
  }}
>
  click
</button>
```

```jsx
<button onClick={() => alert("Clicked")}>click</button>
```

#### ==주의할 점 : 절대로 함수 실행을 위한 `( )` 를 뒤에 붙이면 안된다.==

`<button onClick={handleClick}>` (이 코드)는 **리액트에게 "click" 이벤트가 발생할 경우 "handleClick" 함수를 실행**하도록 하는 반면에 `<button onClick={handleClick()}>` (이 코드)는 **렌더링 도중에 실행되고 이벤트가 발생했을 때 실행되지 않는다.** ( 렌더링은 해당 컴포넌트 실행을 기반으로 하고 렌더링 될 때마다 해당 함수가 실행된다 )

### Reading props in event handlers

이벤트 핸들러 함수는 기본적으로 컴포넌트 내부에 작성하기 때문에 컴포넌트의 `props` 를 사용할 수 있다.

```jsx
function AlertButton({ message, children }) {
  return <button onClick={() => alert(message)}>{children}</button>;
}
export default function Toorbar() {
  return (
    <div>
      <AlertButton message="Playing">Play</AlertButton>
      <AlertButton message="Uploading">Upload</AlertButton>
    </div>
  );
}
```

- `children` 은 컴포넌트 재사용에 도움을 준다

### Passing event handlers as props

이벤트 핸들러 함수를 `props`로 받아 실행하도록 하면 **동일한 이벤트에 대해서 다른 함수를 실행하도록 만들 수 있다.**

```jsx
export default function ToolBar() {
  return (
    <div>
      <PlayButton />
      <UploadButton />
    </div>
  );
}
function PlayButton() {
  return <Button onClick={() => alert("Playing!")}>Play</Button>;
}
function UploadButton() {
  return <Button onClick={() => alert("Uploading!")}>Upload</Button>;
}
function Button({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
}
```

(읽어보기) [Everything you need to know about Design Systems](https://uxdesign.cc/everything-you-need-to-know-about-design-systems-54b109851969)

### Naming event handler props

> `<button>`, `<div>` 와 같은 것을 **Built-in component**

빌트인 컴포넌트들은 `onClick`과 같은 [browser event names](https://react.dev/reference/react-dom/components/common#common-props)만 지원하고 사용자 컴포넌트의 경우는 어떠한 이름의 props 도 가능하다. 관례로 이벤트 핸들러에 대한 props 는 `on` 으로 시작하고 `[event]`가 camel-case 로 뒤따른다.

```jsx
<Button onSmash={() => ...}></Button> // ✅
<button onSmash={() => ...}></button> // ❌
```

이벤트 핸들러 props 에 대한 이름을 지을 때 "역할, 행동, 개념"( ~app-specific interactions~ )과 연관지어서 네이밍 하는 것이 좋다

```jsx
export default function ToolBar({ onPlayMovie, onUploadImage }) {
  return (
    <div>
      <Button onClick={onPlayMovie}>Play</Button>
      <Button onClick={onUploadImage}>Upload</Button>
    </div>
  );
}
function Button({ onClick, children }) {
  return <button onClick={onClick}>{children}</button>;
}
```

**부모 컴포넌트는 자식 컴포넌트에게 이벤트 핸들러를 props 로 전달한 뿐이고 자식 컴포넌트에서 어떻게 실행되는지 몰라도 된다**

📜 **이벤트 핸들러를 추가할 때 적절한(알맞은) HTML-tags( Built-in Component ) 를 사용해야 한다.** 이렇게 해야 코드가 명확하게 읽히고 유지보스 측면에서도 유리하다. 예를들어, `onClick`은 `<button>`태그와 관련된 이벤트 핸들러이기 때문에 다른 태그를 사용하기 보다는 `<button>`태그를 사용해야 한다. 외관상의 이유로 다른 태그를 사용하기 보다는 CSS를 통해 스타일링 하는 것이 바람직하다.

## Event propagation

이벤트는 위로 전파된다. 만약, 한 컴포넌트에서 `click` 이벤트가 발생했을 경우 타겟 빌트인 컴포넌트를 포함한 모든 조상 컴포넌트에서 `onclick` 이벤트 핸들러 함수가 실행된다.

```jsx
export default function ToorBar() {
  return (
    <div onClick={() => alert("div")}>
      <button onClick={() => alert("playing")}>Play</button>
      <button onClick={() => alert("uploading")}>Upload</button>
    </div>
  );
}
```

- `Play` or `Upload` 버튼을 클릭했을 때 2번의 alert 가 실행된다
- `div` 영역을 클릭했을 때 1번의 alert 가 실행된다

❗️`onScroll`을 제외한 모든 이벤트들은 전파된다 ( **propagation** )

### Stopping propagation

이벤트가 발생하고 이벤트 핸들러 함수가 실행될 때 발생한 ~이벤트에 대한 정보를 담고 있는 **event object** 를 인자로 받는다.~ 일반적으로 `e` 로 작성한다.

이벤트 객체에는 **"event propagation" 을 멈출 수 있는 함수**를 제공한다. `e.stopPropagation()`

```jsx
function Button({ onClick, children }) {
  return (
    <button
      onClick={(e) => {
        e.stopPropagation();
        onClick();
      }}
    >
      {children}
    </button>
  );
}
export default function ToorBar() {
  return (
    <div onClick={() => alert("div")}>
      <Button onClick={() => alert("playing")}>Play</Button>
      <Button onClick={() => alert("uploading")}>Upload</Button>
    </div>
  );
}
```

- `Play` or `Upload` or `div` 를 클릭했을 때 1번의 alert 가 실행된다
- 만약 조상 컴포넌트에 해당 이벤트에 대한 이벤트 핸들러 함수가 의도치 않게 실행될 수 있기 때문에 `e.stopPropagation()`를 추가해야 한다.

### Passing handlers as alternative to propagation

이벤트 처리를 위한 구체적인 **로직은 부모 컴포넌트에서 관리**하고 자식 컴포넌트에 props 을 통해 전달한다. 자식 컴포넌트에서 빌트인 컴포넌트에 이벤트 핸들러 함수를 작성할 때 ( inline or callback ) **props 를 통해 받은 함수의 실행문을 포함**한다. 이러한 패턴은 이벤트 핸들러 함수를 작성할 때 이벤트 처리를 위한 함수 외에 다른 코드도 포함시킬 수 있다.

```jsx
export default function Button({ onClick, children }) {
  return (
    <button onClick={(e) => {
      e.stopPropagation();
      ...
      onClick();
    }}>
      {children}
    </button>
}
```

### Preventing default behavior

브라우저에는 특정 이벤트에 대한 "Default Behavior" 이 존재하는데 이것이 실행되지 않도록 하는 방법이 있다. `e.prventDefault()`

`form` 의 "submit" 이벤트가 발생했을 때 페이지가 리로딩되는 Default Behavior 이 있다.

```jsx
export default function Form() {
  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        alert("submitting");
      }}
    >
      <input />
      <button>Submit</button>
    </form>
  );
}
```

- `preventDefault()` : 이벤트에 대한 ~Default Behavior~ 을 막는다.
- `stopPropagation()` : 이벤트 전파를 막는다.

## Can event handlers have side effects?

이벤트 핸들러 함수는 ~side-effects~ 처리를 위한 최적의 장소이다. ( side-effect는 렌더링 외에 일어나느 모든 변화들을 일컫는다 )

리액트 컴포넌트 함수는 **purity** 를 지켜야 하지만 이벤트 핸들러 함수는 그렇지 않다. ( 왜냐하면 렌더링 도중에 실행되는 함수가 아니기 때문이다 ) 그렇기에 이벤트 핸들러 함수는 _side-effects_ 처리를 위한 최적의 장소이다.

---

## Deep Dive : Capture phase events

_stopped propagation_ 하더라도 해당 이벤트를 잡을 수 있다. 이것이 필요한 경우는 많이 없지만 routing 이나 analytics를 위한 기록이 필요할 때 유용할 수 있다. `on[Event]Capture`

```jsx
<div onClickCapture={() => { ... }}>
  <button onClick={e => e.stopPropagation()} />
  <button onClick={e => e.stopPropagation()} />
</div>
```

[버블링과 캡처링](https://ko.javascript.info/bubbling-and-capturing)
**DOM 이벤트에서 정의한 이벤트 흐름**

1. Capture Phase : 이벤트가 하위 요소로 전파되는 단계
2. Target Phase : 이벤트가 실제 타깃 요소에 전달되는 단계
3. Bubbling Phase : 이벤트가 상위 요소로 전파되는 단계

Vanilla JS

```js
// Capture Phase 에서 실행할 이벤트 핸들러 함수 설정
elem.addEventListener(..., { capture: true });
elem.addEventListener(..., true);
```

```html
<form>
  FORM
  <div>
    DIV
    <p>P</p>
  </div>
</form>

<script>
  for (let elem of document.querySelectorAll("*")) {
    // Capture Phase 에 실행
    elem.addEventListener("click", (e) => alert(`캡쳐링: ${elem.tagName}`), true);
    // Bubbling Phase 에 실행
    elem.addEventListener("click", (e) => alert(`버블링: ${elem.tagName}`));
  }
</script>
```
