#react.dev/describing-the-ui/importing-and-exporting-components#
# Importing and Exporting Components
리액트 컴포넌트의 가장 핵심은 ==**reusability**==이다. 더 많은 컴포넌트를 생성하고 더 깊게 컴포넌트를 구성할수록 다른 파일로 나눠 관리하는 것이 좋다.

**You will learn**
- What a ~root component file~ is
- How to ~import and export~ a component
- When to use ~default and named~ imports and exports
- How to import and export multiple components from one file
- How to split components into multiple files
---
## The root component file
기본적으로는 `App.js` 파일을 사용하지만 개발자의 설정에 따라 달라질 수도 있다. `Next.js` 와 같은 라우팅 기반으로 하는 프레임워크를 사용할 경우 각 페이지마다 다른 **"root component"** 를 가진다.

## Exporting and importing a component
before
```jsx
// App.js
function Hello() {
  return <div>Hello</div>
}
export default function App() {
  return (
    <div>
      <Hello />
      <Hello />
    </div>
  );
}
```

after
```jsx
// Hello.js
export default function Hello() {
  return <div>Hello</div>
}
```

```jsx
// App.js
import Hello from './Hello.jsx';
export default function App() {
  return (
    <div>
      <Hello />
      <Hello />
    </div>
  );
}
```

> [Note] import 할 때 `.js` 확장자를 생략할 수 있다.

## Exporting and importing multiple components from the same file
- `.js`파일은 오직 1개의 `default export` 를, 0개 이상의 `named export` 를 가질 수 있다.

> [Note] 어떤 식으로 할지는 팀마다 다르다. 어떤 팀은 `default export` 만을 사용하고 어떤 팀은 `named export` 만을 사용한다. 

```js
// Gallery.js
export function Profile() { ... }
export default function Gallery() { ... }
```

```js
// App.js
import Gallery, { Profile } from './Gallery';
export default function App() { ... }
```
---
## Deep Dive : Default vs named exports
| Syntax  | Export statement                      | Import statement                        |
|:-------:|:-------------------------------------:|:---------------------------------------:|
| Default | `export default function Button() {}` | `import Button from './Button.js';`     |
| Named   | `export function Button() {}`         | `import { Button } from './Button.js';` |
- 하나의 파일에 하나의 컴포넌트를 export 할 때 *default export* 를 사용
- 하나의 파일에 여러개의 컴포넌트를 export 할 때 *named export* 를 사용

`export default () => {}` 구문을 사용해도 무방하지만 자제하자. 왜냐하면 디버깅시 어려움을 주기 때문이다.