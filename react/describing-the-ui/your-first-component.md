#react.dev/describing-the-ui/your-first-component#
# Your Frist Component
***Components*** 는 리액트의 핵심 개념들 중 하나이다.
---
## Components: UI building blocks
- HTML, CSS, JS를 하나의 컴포넌트에서 관리한다.
- 컴포넌트를 "조합"해서 더 큰 컴포넌트를 만들 수 있고 최종앱을 생성한다.
- 컴포넌트는 "재사용" 가능하다.
- [Chakra UI](https://chakra-ui.com/) [Material UI](https://material-ui.com/) 재사용가능한 컴포넌트를 제공한다.

## Defining a component
- 리액트 컴포넌트는 **"markup을 포함한~JavaScript function~"**이다.

```jsx
// "Button" 컴포넌트 정의
export default function Button() {
  return <button>Click</button>
}
```
- `export default`는 파일의 main-function으로 지정해 주고 다른 파일에서 기본적으로 Button 컴포넌트를 import 할 수 있도록 한다.

> [Tip] 리액트 컴포넌트는 기본적으로 자바스크립트 함수이지만 정의할 때 지켜야할 규칙이 있다. **대문자로 시작하도록** 이름을 지어야 한다.

> [Tip] 리액트 컴포넌트 return 문에서 여러 줄에 걸쳐 markup을 작성해야할 경우 **( ) 로 묶어야 한다**. 묶지 않을 경우 return 이후 두번째 라인부터는 무시한다. 그리고 `return ~;` 이런 형태로 "return" 바로 옆에 한줄을 위치시켜야 한다.

## Using a component
- 리액트 컴포넌트를 다른 리액트 컴포넌트 내부에서 사용할 수 있다. (재사용성)
- 리액트는 컴포넌트 첫번째 문자의 대문자 여부에 따라 HTML 컴포넌트와 사용자 컴포넌트를 구분한다.
- 리액트 컴포넌트는 자바스크립트 함수이기 때문에 하나의 파일에 여러개의 컴포넌트를 작성할 수 있고 별도의 파일로 작성할 수도 있다.

> [Tip] 컴포넌트에 다른 컴포넌트를 가져와 사용할 수 있지만 컴포넌트 내에 다른 컴포넌트 함수를 정의하면 안된다. 이 경우 속도측면에서 불리할 수 있고 버그를 유발할 수 있기 때문이다. **무조건 각각의 리액트 컴포넌트는 파일의 Top-Level 에 작성하도록 하자.** 부모 컴포넌트의 데이터를 필요로 하는 자식 컴포넌트가 있으면 "Props"를 이용하는 것도 하나의 방법이다.

---
## Deep Dive : Components all the way down
- React는 부분적으로도 사용할 수 있는데 이 경우 여러개의 *root* 컴포넌트를 가지게 된다.
  - [Add React to an Existing Project](https://react.dev/learn/add-react-to-an-existing-project#using-react-for-a-part-of-your-existing-page)
- 대부분의 리액트 앱은 **처음부터 끝까지 컴포넌트로 구성된다.** 컴포넌트를 만들고 컴포넌트들을 합쳐 UI를 구성하는 것이 가장 쉬운 방법이다.