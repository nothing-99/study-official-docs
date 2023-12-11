# Choosing the State Structure

**You will learn**

- When to use a single vs multiple state variables
- What to avoid when organizing state
- How to fix common issues with the state structure

➭ [공식문서](https://react.dev/learn/choosing-the-state-structure)

---

## Principles for structuring state

1. ==**Group related state**== 2개 이상의 상태 변수를 항상 함께 업데이트 해야할 경우 **"하나의 상태변수"로 합치는 것**을 생각해본다.
2. ==**Avoid contradictions in state**== 실수로 인해 모순적 상황이 발생할 가능성을 없애야 한다.
3. ==**Avoid redundant state**== 기존의 데이터들로 계산할 수 있는 경우 새로운 상태 변수로 생성하지 않아야 한다.
4. ==**Avoid duplication in state**== 동일한 데이터를 여러 곳의 상태 변수에 저장하는 것을 피해야 한다.
5. ==**Avoid deeply nested state**== 데이터의 깊이를 최대한 줄이는 것이 좋다.

## Group related state

**Code1**

```jsx
const [x, setX] = useState(0);
const [y, setY] = useState(0);
```

**Code2**

```jsx
const [position, setPosition] = useState({
  x: 0,
  y: 0,
});
```

- Code1에서 x와 y는 **항상 함께 업데이트되는 상태변수이기 때문에** 하나의 객체로 묶어 하나의 상태변수로 합치는 것이 좋다. 업데이트할 때 setX or setY 중 하나를 누락하는 실수를 없앨 수 있다.

**사용자와의 상호작용으로 상태가 늘어날 수 있는 경우** 객체나 배열로 묶어 하나의 상태 변수로 관리하는 것이 좋다.

📜 상태변수가 객체나 배열일 때 하나의 프로퍼티 혹은 하나의 요소를 직접 수정할 수 없고 `...` 또는 적절한 메서드를 이용해 새로운 객체 혹은 새로운 배열을 생성한 후 setting function 을 호출해야 한다. ( ~~배열도 사실 객체긴 함~~ )

## Avoid contradiction in state

**Code1**

```jsx
const [text, setText] = useState("");
const [isSending, setIsSending] = useState(false);
const [isSent, setIsSent] = useState(false);

const handleSubmit = async (e) => {
  e.preventDefault();
  setIsSending(true);
  await sendMessage(text);
  setIsSending(false); // --(1)
  setIsSent(true); // --(2)
};
```

**Code2**

```jsx
const [text, setText] = useState("");
const [status, setStatus] = useState("typing");

const handleSubmit = async (e) => {
  e.preventDefault();
  setStatus("sending");
  await sendMessage(text);
  setStatus("sent");
};
```

- Code1에서 (1)과 (2)를 함께 호출해야 하는 데 실수로 (1)을 빼먹을 경우 **"isSending"과 "isSent"가 모두 `true`의 값을 가지는 모순적 상황이 발생한다.** ( 상황적으로 보내지는 과정을 거친 "후" 보내진 상태를 가지기 때문에 2개 동시에 `true`값을 가질 수 없다 )
- 때문에 하나의 상태변수로 이를 관리함으로써 모순적 상황을 피할 수 있다.

## Avoid redundant state

props 또는 다른 state variables 를 이용해 계산할 수 있는 데이터들은 그렇게 하는 것이 좋다.

**Code1**

```jsx
const [firstName, setFirstName] = useState("");
const [lastName, setLastName] = useState("");
const [fullName, setFullName] = useState("");
```

**Code2**

```jsx
const [firstName, setFirstName] = useState("");
const [lastName, setLastName] = useState("");
const fullName = lastName + " " + firstName;
```

- Code2에서 setFirstName 또는 setLastName setting function 호출을 통해 리렌더링이 발생하면서 fullName이 생성된다. ( 리-렌더링 === 컴포넌트 함수 재실행 )

## Avoid duplication in state

특정 데이터에 대한 중복된 상태 변수 선언은 피해야 한다.

**Data**

```jsx
const initialItems = [
  { title: "pretzels", id: 0 },
  { title: "crispy seaweed", id: 1 },
  { title: "granola bar", id: 2 },
];
```

**Code1**

```jsx
const [items, setItems] = useState(initialItems);
const [selectedItem, setSelectedItem] = useState(null);
```

**Code2**

```jsx
const [items, setItems] = useState(initialItems);
const [selectedId, setSelectedId] = useState(null);
```

- Code1의 데이터 형태
  - items `[ { title: 'pretzels', id: 0 }, ... ]`
  - selectedItem `{ title: 'pretzels', id: 0 }` || `null`
  - 동일한 데이터가 **별도의 공간에 각각 저장돼 있는 상태이다.**
- Code2의 데이터 형태
  - items `[ { title: 'pretzels', id: 0 }, ... ]`
  - selectedId `number` || `null`

```jsx
import { useState } from "react";

const initialItems = [
  { title: "pretzels", id: 0 },
  { title: "crispy seaweed", id: 1 },
  { title: "granola bar", id: 2 },
];

export default function App() {
  const [items, setItems] = useState(initialItems);
  const [selectedId, setSelectedId] = useState(null);

  const selectedItem = items.find((item) => item.id === selectedId);

  const handleItemChange = (e, id) => {
    setItems(
      items.map((i) => {
        if (i.id === id) {
          return { ...i, title: e.target.value };
        }
        return i;
      })
    );
  };
  return (
    <>
      <h1>What&apos;s your travel snack</h1>
      <div>
        <ul>
          {items.map((item) => (
            <li key={item.id}>
              <input
                value={item.title}
                onChange={(e) => handleItemChange(e, item.id)}
              />{" "}
              <button onClick={() => setSelectedId(item.id)}>Choose</button>
            </li>
          ))}
        </ul>
      </div>
      <h4>I like {selectedItem?.title}</h4>
    </>
  );
}
```

## Avoid deeply nested state

➭ [Choosing the State Structure](https://react.dev/learn/choosing-the-state-structure#avoid-deeply-nested-state) ( plan data 보기 )

state의 Nesting 정도가 크다면 "flat(normalized)" 를 고려해야 한다. 이러한 리팩토링이 state 업데이트를 좀 더 쉽게 만들어 준다.

---

## Deep Dive : Don't mirror props in state

useState의 인자값으로 props로 전달받은 데이터를 주면 안된다. 왜냐하면 부모 컴포넌트에서 새로운 props 값을 주더라도 setting function 이 호출된 적이 없기 때문에 상태 변수의 값은 변경되지 않는다.

**Code1**

```jsx
function Message({ messageColor }) {
  const [color, setColor] = useState(messageColor);
}
```

**Code2**

```jsx
function Message({ messageColor }) {
  const color = messageColor;
}
```

- Code1은 부모 컴포넌트에서 messageColor에 대한 새로운 값을 주더라도 Message 컴포넌트가 재실행될 때 state queue에 어떠한 것도 없기 때문에 color는 업데이트되지 않는다. ( ~리-렌더링시 state-queue를 보고 state 값이 업데이트되는 것을 생각하자~ )
- Code2는 변경된 color 변수를 사용할 수 있다.
