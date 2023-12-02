# Updating Objects in State

`object`(of JavaScript)도 State의 값으로 설정할 수 있고 다른 primitive 타입의 데이터와 같이 오직 setting function 을 이용해 업데이트 해야 한다. **새로운 객체 또는 복사본을 인자로 넘겨야 한다** ( 어찌보면 복사본을 넘기는 것도 새로운 객체를 넘기는 것과 같다. 기존의 객체를 넘기는 것이 아니기 때문에..? )

**You will learn**

- How to correctly update an object in React state
- How to update a nested object without mutating it
- What immutability is, and how not to break it
- How to make object copying less repetitive with Immer

[공식문서](https://react.dev/learn/updating-objects-in-state)

---

## What's a mutations?

_object_ 는 **mutable** 데이터 타입이지만 State로 설정했을 때는 **immutable** 로 다뤄야 한다. _object_ 상태 변수의 프로퍼티 값을 변경할 수 있지만 변경해서는 안된다.

```jsx
const [obj, setObj] = useState({ value: 0 });

return (
  <button
    onClick={() => {
      const output = `${obj.value}`;
      obj.value += 1;
      output += ` ${obj.value}`;
      console.log(output);
    }}
  >
    click
  </button>
);
```

```
0 1
1 2
2 3
3 4
...
```

- _object_ 타입 상태 변수의 프로퍼티 값을 변경하더라도 별다른 에러 없이 동작한다

```jsx
const [num, setNum] = useState(0);

return (
  <button
    onClick={() => {
      const output = `${num}`;
      num += 1;
      output += ` ${num}`;
      console.log(output);
    }}
  >
    click
  </button>
);
```

```
Uncaught TypeError: "num" is read-only
```

- _TypeError_ 가 발생한다

\-\-\-

### Mutation & Immutation

```jsx
let count = 0;
count = count + 1; // mutation
let newCount = count + 1; // immutation
```

**mutation**

- 데이터나 객체의 상태를 직접 변경

**immutation**

- 데이터나 객체의 상태를 직접 변경하지 않고, 새로운 값을 반환하여 변경
- 안정적이고 예측 가능한 코드 생산을 가능하게 한다

**상태 예측**
객체나 데이터의 상태가 변경되지 않으므로 상태 예측이 쉽다. 이는 디버깅의 난이도를 낮춘다.

```js
// mutable
let mutableObj = { value: 5 };
mutableObj.value = 10;

// immutable
const immutableObj = { value: 5 };
const newImmutableObj = { ...immutableObj, value: 10 };
```

**병렬 처리와 쓰레드 안정성**
여러 쓰레드가 동시에 데이터를 변경하는 문제를 방지할 수 있다.

```js
// mutable
let sharedValue = 0;
function incrementSharedValue() {
  sharedValue++;
}

// immutable
const immutableSharedValue = { value: 0 };
function incrementImmutableSharedValue() {
  return { value: immutableSharedValue + 1 };
}
```

**디버깅**
상태 변경이 없기 때문에 디버깅이 용이해진다.

```js
// mutable
let mutableArray = [1, 2, 3];
mutableArray.push(4);

// immutable
const immutableArray = [1, 2, 3];
const newImmutableArray = [...immutableArray, 4];
```

**undo/redo 구현 용이**
이전 상태를 사본으로 유지하기 때문에 기능 구현을 간단하게 만든다.

```js
// mutable
let stateHistory = [];
let currentState = { value: 0 };
function updateState(newValue) {
  currentState.value = newValue;
  stateHistory.push(currentState);
}

// immutable
const immutableStateHistory = [];
const initialImmutableState = { value: 0 };
function updateImmutableState(newValue) {
  const newState = { ...initialImmutableState, value: newValue };
  immutableStateHistory.push(newState);
}
```

## Treat state as read-only

State에 _object_ 타입의 데이터를 설정했을 때 **read-only** 로 다뤄야 한다.

```jsx
// 좌표따라 원이 이동하는 코드 일부
// object ( state variable ) 를 직접 변경하는 중
<div
  onPointerMove={(e) => {
    position.x = e.clientX;
    position.y = e.clientY;
  }}
>
```

- 직접 값을 변경 중이지만 화면에 원이 표시되지 않는다. 왜냐하면 리-렌더링이 되지 않고 있기 때문에,,,
- 콘솔창에 에러 문구는 뜨지 않지만 State 로 저장한 모든 변수는 **read-only** 로 다뤄야 한다.
- 객체를 직접 변경하지 않고 의도한 대로 동작하도록 하기 위해 setting function 을 호출해야 한다.

```jsx
<div
  onPointerMove={(e) => {
    setPosition({
      x: e.clientX,
      y: e.clientY,
    })
  }}
>
```

- 새로운 객체를 생성해 setting function 의 인자로 넘긴다.

| queued                        | obj              | return            |
| ----------------------------- | ---------------- | ----------------- |
| replace with `{ x: 5, y: 5 }` | `{ x: 0, y: 0 }` | `{ x: 5, y : 5 }` |

## Copying objects with the spread syntax

기존의 객체의 값을 베이스로 깔고 새로운 값을 추가하거나 기존의 값을 변경하는 것을 원할 때는 새로운 객체를 만들 때 **spread syntax** ( `...` )를 이용하면 된다. ( 링크 : [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax) )

```jsx
<div
  onPointerMove={(e) => {
    setPosition({
      // spread syntax : ...
      ...position,
      x: e.clientX,
      y: e.clientY,
    })
  }}
>
```

**Example**

```jsx
const [person, setPerson] = useState({
  firstName: "Barbara",
  lastName: "Hepworth",
  email: "bhepworth@sculpture.com",
});
```

```jsx
function handleFirstNameChange(e) {
  person.firstName = e.target.value;
}

function handleLastNameChange(e) {
  person.lastName = e.target.value;
}

function handleEmailChange(e) {
  person.email = e.target.value;
}
```

**문제**

- state 값을 직접 변경하고 있다 ➜ 새로운 객체 생성
- 리-렌더링이 발생하지 않아 화면에 업데이트 되지 않는다 ➜ setting 함수 호출

**해결**

```jsx
function handleFirstNameChange(e) {
  setPerson({
    ...person,
    firstName: e.target.value,
  });
}

function handleLastNameChange(e) {
  setPerson({
    ...person,
    lastName: e.target.value,
  });
}

function handleEmailChange(e) {
  setPerson({
    ...person,
    email: e.target.value,
  });
}
```

## Updating a nested object

spread syntax 는 기본적으로 "얕은 복사" 를 하기 때문에 프로퍼티로 다른 객체를 가지고 있을 경우 주의해야 한다.

**Exmaple**

```jsx
const [person, setPerson] = useState({
  name: "Niki de Saint Phalle",
  artwork: {
    title: "Blue Nana",
    city: "Hamburg",
    image: "https://i.imgur.com/Sd1AgUOm.jpg",
  },
});
```

**No**

```js
const nextPerson = { ...person };
nextPerson.artwork.title = "Bitcoin";
```

- `nextPerson.artwork.title` 뿐 아니라 `person.artwork.title` 의 값도 변경된다. ( 상태변수 내부 값이 직접 변경되는 사고... )
- `person.artwork` 에는 { title: ~, city: ~, image } 객체의 레퍼런스가 들어있는데 nextPerson 에 이 객체의 레퍼런스가 복사 되기 때문에 동일한 객체를 가리키게 된다. ( 얕은복사의 한계 )

**OK**

```jsx
const nextArtwork = { ...person.artwork, city: "Dubai" };
const nextPerson = { ...person, artwork: newArtwork };
setPerson(nextPerson);
```

**OK**

```jsx
setPerson({
  ...person,
  artwork: {
    ...person.artwork,
    city: "Dubai",
  },
});
```

### Write concise update logic with lmmer

상태 변수를 업데이트할 때는 불변성을 지키면서 업데이트 해야 한다. `...` 를 통한 얕은 복사로는 불변성 유지에 한계가 있다. 객체를 프로퍼티로 가지고 레벨이 깊을수록 어려워진다.

하지만!! `useImmer` 를 통해 불변성을 지키면서 쉽게 상태 변수 업데이트가 가능하다.

**설치**
`npm i use-immer`

**모듈 불러오기**
`import { useImmer } from 'use-immer'`

**예시**

```jsx
const [person, updatePerson] = useImmer({
  name: "Niki de Saint Phalle",
  artwork: {
    title: "Blue Nana",
    city: "Hamburg",
    image: "https://i.imgur.com/Sd1AgUOm.jpg",
  },
});
```

```jsx
// title
function handleTitleChange(e) {
  updatePerson((draft) => (draft.artwork.title = e.target.value));
}
// city
function handleCityChange(e) {
  updatePerson((draft) => (draft.artwork.city = e.target.value));
}
// image
function handleImageChange(e) {
  updatePerson((draft) => (draft.artwork.image = e.target.value));
}
```

---

## [Deep Dive] Local mutation is fine

**Mutation**이 문제가 되는 경우는 오직 State로 선언한 *objects*가 변경의 대상인 경우다.

```jsx
const [position, setPosition] = useState({
  x: 0,
  y: 0,
});
```

```jsx
position.x = e.clientX;
position.y = e.clientY;
```

- State 로 저장한 객체를 수정했다. State를 **Immutable** 로 접근해야 한다 `read-only`

```jsx
// state setting function
setPosition({
  x: e.clientX,
  y: e.clientY,
});
```

```jsx
// 좀 더 풀어서 작성하면...
const nextPosition = {};
nextPosition.x = e.clientX;
nextPosition.y = e.clientY;
setPosition(nextPosition);
```

- 새로 생성한 일반 객체 변수를 변이하는 것은 문제가 없다 ➜ **"Local Mutation"** . 새로 생성한 객체가 다른 부분에서 참조되지 않은 상태에서 변경되는 것은 다른 부분에 어떠한 영향도 끼치지 않기 때문에 문제없다.
- **"Local Mutation"** 코드는 렌더링 도중에 실행되도 "완전" 문제없다.

## [Deep Dive] Objects are not really nested

한 객체가 프로퍼티로 다른 객체를 값으로 가질 때 ( nesting ) 형태만 그렇지 별도로 존재한다.

```js
let obj = {
  name: "Niki de Saint Phalle",
  artwork: {
    title: "Blue Nana",
    city: "Hamburg",
    image: "https://i.imgur.com/Sd1AgUOm.jpg",
  },
};
```

```js
let obj1 = {
  name: "Niki de Saint Phalle",
  artwork: obj2,
};

let obj2 = {
  title: "Blue Nana",
  city: "Hamburg",
  image: "https://i.imgur.com/Sd1AgUOm.jpg",
};
```

- `obj1` `obj2` 는 별도로 존재한다
- `obj1.artwork` 는 `obj2` 의 포인터(레퍼런스, 주소값)를 가진다.

## [Deep Dive] How does Immer work?

[Introduction to Immer](https://immerjs.github.io/immer/#how-immer-works)

`draft`는 current-state를 위한 Proxy객체이다. `draft`에 변경사항들을 가져가 current-state와 변경사항을 이용해 새로운 객체를 반환해준다.
