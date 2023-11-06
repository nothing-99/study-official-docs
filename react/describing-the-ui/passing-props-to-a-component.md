#react.dev/describing-the-ui/passing-props-to-a-component#
# Passing Props to a Component
Component 사이에서 데이터를 주고 받기 위해 **"Props"** 를 이용한다. 부모 컴포넌트에서 자식 컴포넌트를 호출할 때 Props로 데이터를 전달할 수 있다

**You will learn**
- How to pass props to a component
- How to read props from a component
- How to specify default values for props
- How to pass some JSX to a component
- How props change over time

---
## Familiar props
- **Props** 는 JSX 태그를 통해 전달하는 "information" 이다. `className` `src` `alt` 등 attributes 로 작성하는 것들 모두 props 이다.
- ReactDOM이 [HTML Standard](https://html.spec.whatwg.org/multipage/#toc-semantics) 에서 정의된 규칙을 따르기 때문에 html component 에는 저기서 정의된 데이터를 props 로 전달할 수 있는 반면 custom component 에는 자유롭게 데이터를 props 로 전달할 수 있다. ( 단, 컴포넌트 정의시에 전달받을 props 를 정의해야 한다 )

## Passing props to a component
- Step 1: Pass props to the child component
- Step 2: Read props inside the child component

```jsx
// Profile.js
export default function Profile() {
  return (
    <Avatar
      person={{ name: 'kim', imageId: '2Jkl3i'}}
      size={100}
    />
  );
}
```

```jsx
// Avatar.js
export default function Avatar({ person, size }) {
  return (
    <img
      className="avatar"
      src={getImageUrl(person}}
      alt={person.name}
      height={size}
      width={size}
    />
  );
}
```

- "Props" 는 리액트 컴포넌트들을 별개로 존재할 수 있도록 한다. 자식 컴포넌트에서 props 를 어떻게 사용할지 고려하지 않고 부모 컴포넌트에서 전달하는 props 의 값을 변경할 수 있고 반대로 전달받는 props 값을 고려하지 않고 자식 컴포넌트에서 어떻게 사용할 지 변경할 수 있다 ~~( 요구하는 데이터 타입을 따랐다는 전제하에 )~~
- 리액트 컴포넌트 함수는 사실상 하나의 argument를 가질 수 있는데 `props` 객체이다. 컴포넌트 함수를 정의할 때 `props` 를 인자로 명시해도 되고 자바스크립트의 "destructuring" 기법을 이용해 각 props 를 따로 명시해도 된다.

```jsx
export default function Avatar({ person , name }) { ... }
export default function Avatar(props) {
  let person = props.person;
  let name = props.name;
  ...
}
```

## Specifying a default value for a prop
- props 의 "default value" 를 설정할 수 있다. 특별한 존재는 아니고 자바스크립트에서 지원하는 기법 중 하나이다. `:` 이 아닌 `=` 를 사용해 default value 를 설정한다
- 부모 컴포넌트에서 **props 를 전달하지 않거나** or **`undefined`  를 전달했을 때** default value 가 사용된다. 주의할 점은 ~`null`, `0` 를 값으로 넘겼을 때 default value 는 사용되지 않는다~

```jsx
export default function Avatar({ person, size=100 }) { ... }
```

## Forwarding props with the JSX spread syntax
- props를 그대로 자식 컴포넌트에게 전달해야 하는 경우 spread-syntax 를 이용해 훨씬 간결한 코드를 작성할 수 있다.
- 간결한 장점은 있지만 **남용해서는 안된다.** spread syntax 를 사용하기 위해 컴포넌트를 작성한다면 구조적 문제가 발생할 수 있다. 컴포넌트가 너무 많은 역할을 수행하거나 복잡성이 향상할 수 있다.
  - ~컴포넌트는 되도록 한가지 역할을 하도록 나누는 것이 좋다고 한다~

```jsx
export default function Profile(props) {
  return (
    <div>
      <Avatar {...props} />
    </div>
  );
}
```

## Passing JSX as children
- JSX 태그 안에 작성한 content 는 감싸고 있는 JSX 태그의 `children` props 에 값으로 할당된다.
- 이 방법은 **~nesting 하고 있는 컴포넌트 내부에서 무엇을 렌더링할지 몰라도~** `children` prop 으로 값이 전달되도록만 하면 된다 ~"flexible pattern"~

```jsx
<Card>
  <Avatar />
</Card>
```
- Card 컴포넌트는 Avatar 컴포넌트를 `children` props의 값으로 가진다.

## How props change over time
- props 는 **특정 시점에서의 컴포넌트 데이터 스냅샷**이라 보면 된다. props 의 값을 변경하기 위해서는 부모 컴포넌트에 새로운 object 에 대한 요청을 보내야 하고 받으면 자바스크립트 엔진은 previous object 에 대한 메모리를 회수한다.
- user interaction 에 대해서 데이터를 변경해야 하는 경우 **state variable** 을 사용하자. 