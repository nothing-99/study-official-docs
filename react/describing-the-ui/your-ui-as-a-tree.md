# Your UI as a Tree

(리액트 포함)많은 UI 라이브러리들은 **트리 구조를 이용해 UI를 모델링**한다. 이것은 컴포넌트 간의 관계를 파악하거나 앱을 디버깅하는데 도움이 된다.

**You will learn**

- How React “sees” component structures
- What a render tree is and what it is useful for
- What a module dependency tree is and what it is useful for

[Understanding Your UI as a Tree](https://react.dev/learn/understanding-your-ui-as-a-tree)

---

## Your UI as a tree

리액트는 트리 구조를 이용해 컴포넌트 간의 관계를 표현한다. 트리 구조를 사용함으로써 얻는 이점들이 있다.

- 컴포넌트 사이의 "data flow" 이해
- 리액트 앱의 렌더링과 사이즈의 최적화

## The Render Tree

( A major feature of components ) **컴포넌트들로 구성된 새로운 컴포넌트를 생성할 수 있다.** 여기서 _parent component_ 와 _child component_ 라는 개념을 가질 수 있다. 그리고 부모 컴포넌트는 다른 컴포넌트의 자식 컴포넌트가 될 수 있다. ~이런 컴포넌트들의 관계를~ 모델링한 트리 구조의 개체가 바로 **(React) "render tree"** 이다.

- 각각의 컴포넌트를 가리키는 노드들로 구성
- 루트 노드는 리액트 렌더링이 처음 시작되는 컴포넌트를 가리킨다

**( a single render pass )** ~conditional rendering~ 에 의해 부모 컴포넌트는 "props data passing" 에 따라 다른 자식 컴포넌트를 가질 수 있다. 리렌더링 시 이전 렌더링에서 생성된 렌더 트리와 동일한 렌더 트리 혹은 새로운 렌더 트리가 생성된다.

render-tree 를 통해 **Top-level Components** 와 **leaf Components** 를 식별할 수 있다. 이러한 행위는 "data flow" 뿐 아니라 "performance of app" 파악에 도움을 준다

**Top-level Components**

- 루트 컴포넌트에 가까이 있는 컴포넌트들
- 후손 컴포넌트들의 렌더링 성능에 영향을 줄 수 있다.

**leaf Components**

- 자식 컴포넌트를 가지지 않은 바닥에 있는 컴포넌트들
- 리렌더링이 자주 된다.

## The Module Dependency Tree

리액트는 컴포넌트 간의 관계를 나타내는 "render tree" 외에 **모듈 의존관계를나타내는 "module dependency tree" ( dependency tree ) 를 가진다.**

- 루트 노드는 일반적으로 루트 컴포넌트를 포함한 모듈을 나타내고 ~"entrypoint file"~ 이라 한다
- 각각의 노드는 컴포넌트, 함수 혹은 상수들을 포함하고 있는 JS 모듈(파일) 이다.
- 노드들은 `inport` 를 통해 연결

| Render Tree             | Dependency Tree                          |
| ----------------------- | ---------------------------------------- |
| `component`             | `module`                                 |
| only `component`        | `component` `function` `constant` module |
| `return` 문을 통해 결정 | `import` 문을 통해 결정                  |

```jsx
import FancyText from "./FancyText";
import InspirationGenerator from "./InspirationGenerator";
import Copyright from "./Copyright";

export default function App() {
  return (
    <>
      <FancyText
        title
        text="Get Inspired App"
      />
      <InspirationGenerator>
        <Copyright year={2004} />
      </InspirationGenerator>
    </>
  );
}
```

**"Render Tree"**

```
- App
	- FancyText
	- InspirationGenerator
		- Copyright
```

- Copyright 컴포넌트는 InspirationGenerator 컴포넌트의 child props 로 전달되기 때문에 App 컴포넌트가 아닌 InspirationGenerator 컴포넌트와 부모-자식 관계를 가진다

**"Dependency Tree "**

```
- App.js
	- FancyText.js
	- InspirationGenerator.js
	- Copyright.js
```

---

## Deep Dive : Where are the HTML tags in the render tree?

React render tree 는 **리액트 앱의 "insight" 를 제공하기 위한 목적**으로 리액트 컴포넌트로만 구성된다.
