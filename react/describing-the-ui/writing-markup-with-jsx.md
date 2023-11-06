#react.dev/describing-the-ui/writing-markup-with-jsx#
# Writing Markup with jsx
*JSX* 는 JavaScript 파일에 HTML과 거의 동일한 마크업을 작성하기 위한 **"syntax extension from JavaScript"** 이다.

**You will learn**
- Why react mixes markup with rendering logic
- How JSX is different from HTML
- How to display information with JSX

---
## JSX: Putting markup into JavaScript
- React Component : "Markup" + "Logic" ( + "Style" )

오랜기간 동안 개발자들은 HTML, CSS, JS 각각의 파일에서 content, style, logic을 위한 마크업 및 코드를 작성했다. 시간이 지남에 따라 ~사용자와의 "interaction"이 많아지며 logic에 의해 UI가 결정~되는 지경에 이르렀다. 즉, JS이 HTML의 마크업을 담당하게 된 것이다. **이것이 리액트가 Mark-up과 Logic을 리액트-컴포넌트 한 공간에서 관리하도록 코드를 작성하는 이유이다.**
- 서로 관련이 없는 UI 요소는 별도의 리액트 컴포넌트로 만들면 된다.
- 리액트 컴포넌트는 **"markup을 표현하기 위한 JSX를 포함한 자바스크립트 함수"**

html + js
```html
// html
<form>...</form>
```
```js
// js
onClick() {...}
onSubmit() {...}
```
react-component
```jsx
// js(react)
Form() {
  onClick() {...}
  onSubmit() {...}
  return <form onSubmit>...</forom>;
}
```

> [Note] React와 JSX는 독립적인 존재이다. JSX는 "syntax extension", React는 "JavaScript Library"

## Converting HTML to JSX
JSX에는 지켜야할 규칙이 존재한다.

### 1. Return a single root element
- <div>를 사용해 여러개의 요소들을 "Wrapping"한다. 그 결과 return 문은 여러개의 child element를 가진 하나의 element를 리턴한다.
- 래핑할 때 `<>...</>` **React Fragment** 를 이용해도 무방하다. ( Empty tag : 이것은 브라우저 HTML 트리에는 표현되지 않는다 ) [<Fragment> \(<>...</>\)](https://react.dev/reference/react/Fragment)
```jsx
return (
  <div>
    <h1>header</h1>
    <span>span</span>
    <footer>footer</footer>
  </div>
);
```

### 2. Close all the tags
- "close tag" 를 무조건 명시해야 한다.
  - self-closing `<img />`
  - plain closing `<div></div>`

### 3. camelCase most of the things
- JSX에서 작성하는 attribute들은 변환된 JavaScript Object의 property가 된다.
- 대부분의 attribute는 camelCase로 작성해야 하지만 `aria-*`, `data-*` 은 예외이다.
- [Common components \(e.g. <div>\)](https://react.dev/reference/react-dom/components/common)

**JSX Converter**
- [HTML to JSX](https://transform.tools/html-to-jsx)
---
## Deep Dive : Why do multiple JSX tags need to be wrapped?
JSX가 HTML과 외형은 거의 유사하지만 JSX는 하나의 **"JavaScript Object" 변환이 된다**. 만약 래핑되지 않은 여러개의 JSX tag가 나란히 return 문에 위치한다면 배열이나 객체로 만들지 않는 이상 모두를 리턴하는 것은 불가능하다.