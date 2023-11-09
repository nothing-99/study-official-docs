#react.dev/describing-the-ui/conditional-rendering#
# Conditional Rendering
`if` , `&&` , `? :`
**You will learn**
- How to return different JSX depending on a condition
- How to conditionally include or exclude a piece of JSX
- Common conditional syntax shortcuts you'll encounter in React codebases

---
## Conditionally returning JSX
- **`if` 문을 이용**해서 다른 JSX를 리턴하도록 하면 된다.
```jsx
export default function Item({ toDo, checked }) {
  if (checked) return <li>{toDo} ✅</li>
  return <li>{toDo}</li>
}
```

- 어떠한 것도 렌더링 되는 것을 원하지 않는 경우 **null 을 리턴**한다. ~~( null 을 리턴하도록 하는 패턴의 사용은 자제하자 )~~
```jsx
export default function Item({ toDo, checked }) {
  if (checked) return null;
  return <li>{toDo}</li>
}
```

## Conditionally including JSX
한 컴포넌트가 조건에 따라 부분적으로 다른 JSX를 리턴하는 경우 여러개의 `return` 문을 가지도록 하는 것은 별로 좋지 않은 방법이다. ( 동일한 부분에 대해서 수정을 할 때 모든 return 문을 수정해야 한다... ) 그래서 조건에 따라 부분적으로 JSX를 결정하는 방법을 제공한다. 

### Conditional (ternary) operator (`? :`)

```jsx
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```

```jsx
function Item({ name, isPacked }) {
  return (
    <li className="item">
      {isPacked ? (
        <del>
          {name + ' ✔'}
        </del>
      ) : (
        name
      )}
    </li>
  );
}
```

### Logical AND operator (`&&`)
- 참일 경우 뒤의 표현식을 렌더링 ✅
- 거짓일 경우 렌더링 ❌

```jsx
return (
  <li className="item">
    {name}{isPacked && ' ✔'}
  </li>
);
```

React에서 지원하는 것이 아닌 JavaScript 에서 지원하는 구문이다 `left-side && right-side`
- if left-side `true` : `return right-side`
- if left-side `false` : `return false`
  - 리액트는 `false`를 `null` or `undefined` 와 같은 취급을 한다. 그래서 어떠한 것도 렌더링하지 않는다.

#### Pitfall : Don't put numbers on the left side of `&&`
- JavaScript는 left-side의 결과를 `boolean` 형태로 바꾼 값을 평가한다.
  - Boolean(left-side) `true` : **right-side**
  - Boolean(left-side) `false` : **left-side**
- 문제는 left-side에 `0` 또는 그 값을 가지는 변수를 놓을 경우 발생한다. 0을 boolean 값으로 변환할 경우 `false` 로 변환될 것이고 리액트가 렌더링할 때 어떠한 것도 렌더링 하지 않기를 원하겠지만 **아니다**. left-side 의 boolean으로 변환된 값이 아닌 그 자체를 리턴하기에 `0` 을 렌더링할 것이다.
  - `count && <span>New messages</span>` ❌
  - `count > 0 && <span>New message</span>` ✅

### Conditionally assigning JSX to a variable
- 삼항연산자(` ? : `) or AND연산자(`&&`) 를 사용해 return 문에 JSX 내부에 작성할 수 있다
- `if` 연산자를 이용해 content를 변수에 담아서 그 변수를 return 문에 작성할 수 있다. 변수에 조건에 따라 다른 값을 할당하고..

```jsx
function Item({ name, isPacked }) {
  let itemContent = name;
  if (isPacked) {
    itemContent = (
      <del>
        {name + " ✔"}
      </del>
    );
  }
  return (
    <li className="item">
      {itemContent}
    </li>
  );
}
```
---
## Assignment
before
```jsx
function Drink({ name }) {
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{name === 'tea' ? 'leaf' : 'bean'}</dd>
        <dt>Caffeine content</dt>
        <dd>{name === 'tea' ? '15–70 mg/cup' : '80–185 mg/cup'}</dd>
        <dt>Age</dt>
        <dd>{name === 'tea' ? '4,000+ years' : '1,000+ years'}</dd>
      </dl>
    </section>
  );
}
```

after
```jsx
function Drink({ name }) {
  let plant = null;
  let caffein = null;
  let age = null;

  if (name === 'tea') {
    plant = 'leaf';
    coffein = '15–70 mg/cup';
    age = '4,000+ years'
  } else if (name === 'coffee') {
    plant = 'bean';
    coffein = '80–185 mg/cup';
    age = '1,000+ years';
  } else {
    return null;
  }
  return (
    <section>
      <h1>{name}</h1>
      <dl>
        <dt>Part of plant</dt>
        <dd>{plant}</dd>
        <dt>Caffeine content</dt>
        <dd>{coffein}</dd>
        <dt>Age</dt>
        <dd>{age}</dd>
      </dl>
    </section>
  );
}
```
- 코드는 전자가 훨씬 짧지만 보기에는 후자가 더 좋아 보인다. ( 개인 선호도에 따라 작성하는 방법이 달라질 듯? )
---
## Deep Dive : Are these two examples fully equivalent?

```jsx
// Example 1
if (isPacked) {
  return <li className="item">{name} ✔</li>;
}
return <li className="item">{name}</li>;
```

```jsx
// Example 2
return (
  <li className="item">
    {isPacked ? name + ' ✔' : name}
  </li>
);
```

- 객체지향 프로그래밍의 대표적인 언어 "자바"는 클래스를 기반으로 객체(instance)를 생성하며 각 객체는 고유의 상태를 가진다.
- JSX요소는 특정 객체의 인스턴스도 아니고 고유의 내부 상태도 가지지 않고 실제 DOM노드도 아니다. JSX요소는 실제 DOM노드를 생성하기 위한 "blueprint" ("template") 으로 보면 된다.
- 위의 예시는 동일한 DOM노드를 생성하기 위한 "blueprint" 이기 때문에 2가지 예는 "같다로" 볼 수 있다.

---
## `<del>`: The Deleted Text element
[<del>: The Deleted Text element - HTML: HyperText Markup Language | MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/del)
- 삭제하기 위한 문자열

[<ins>: The Inserted Text element - HTML: HyperText Markup Language | MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/ins)
- 추가하기 위한 문자열

```html
<-- html -->
<blockquote>
  There is <del>nothing</del> <ins>no code</ins> either good or bad, but <del>thinking</del> <ins>running it</ins> makes
  it so.
</blockquote>
```

```css
// css
del {
  text-decoration: line-through;
  background-color: #fbb;
  color: #555;
}
ins {
  text-decoration: none;
  background-color: #d4fcbc;
}
blockquote {
  padding-left: 15px;
  border-left: 3px solid #d7d7db;
  font-size: 1rem;
}
```
