# Updating Arrays in State

**You will learn**

- How to add, remove, or change items in an array in React state
- How to update an object inside of an array
- How to make array copying less repetitive with Immer

[react.dev](https://react.dev/learn/updating-arrays-in-state)

---

## Updating arrays without mutation

상태변수는 불변성을 지켜야 한다. 상태변수에 배열을 저장한 경우도 당연히 불변성을 지켜야 한다. `arr[0] = 'bird'` 와 같이 요소에 새로운 값을 할당하는 것도, 배열 자체를 변형시키는 메서드를 호출하는 것도 해서는 안된다.

**mutating method & non-mutating method**
| | avoid | prefer |
|-----------|:-----------------------:|:--------------------:|
| adding | `push` `unshift` | `concat` `[...arr]` |
| removing | `pop` `shift` `splice` | `filter` `slice` |
| replacing | `splice` `arr[i] = ...` | `map` |
| sorting | `reverse` `sort` | copy the array first |

- 최종적으로 상태변수의 업데이트를 위해서 새로운 배열을 setting-function의 인자로 넘겨야 한다.
- JavaScript에서 배열은 근본적으로 `object` 이다.

❗️`slice` & `splice` 를 잘 구분해서 사용해야 한다.

- `slice`는 새로운 배열 리턴
- `splice`는 자기 자신을 변형

### Adding to an array

`...` 문법을 이용해 새로운 요소를 추가한 새로운 배열을 생성할 수 있다.

```jsx
const newCoin = {
  id: crypto.randomUUID(),
  name: inputName,
};
setMyCoins([newCoin, ...myCoins]);
```

### Removing from an array

`filter`을 이용해 삭제할 요소를 제외한 새로운 배열을 생성할 수 있다.

```jsx
{
  coins.map((coin) => (
    <li key={coin.id}>
      {coin.name}{" "}
      <button
        onClick={() => {
          setCoins(coins.filter((c) => c.id !== coin.id));
        }}
      >
        Delete
      </button>
    </li>
  ));
}
```

### Transforming an array

### Replacing items in an array

`map`을 이용해 일부 또는 전체를 수정한 새로운 배열을 생성할 수 있다.

```jsx
const handleClickOdd = () => {
  const newBoxs = boxs.map((box, i) => {
    if (i % 2 === 1) {
      return {
        ...box,
        color: Math.floor(Math.random() * colors.length),
      };
    }
    return box;
  });
  console.log(newBoxs);
  setBoxs(newBoxs);
};
```

```jsx
import { Fragment, useState } from "react";

// data
const colors = ["#00FFFF", "#000000", "#0000FF", "#FF00FF", "#808080", "#008000", "#00FF00", "#800000", "#000080", "#800080", "#FFFF00"];
const initBoxs = [
  { id: crypto.randomUUID(), color: 0 },
  { id: crypto.randomUUID(), color: 1 },
  { id: crypto.randomUUID(), color: 2 },
  { id: crypto.randomUUID(), color: 3 },
  { id: crypto.randomUUID(), color: 4 },
  { id: crypto.randomUUID(), color: 5 },
];

// style
const boxStyle = {
  width: "50px",
  height: "50px",
};

export default function App() {
  const [boxs, setBoxs] = useState(initBoxs);
  const handleClickOdd = () => {
    const newBoxs = boxs.map((box, i) => {
      if (i % 2 === 1) {
        return {
          ...box,
          color: Math.floor(Math.random() * colors.length),
        };
      }
      return box;
    });
    console.log(newBoxs);
    setBoxs(newBoxs);
  };
  const handleClickEven = () => {
    const newBoxs = boxs.map((box, i) => {
      if (i % 2 === 0) {
        return {
          ...box,
          color: Math.floor(Math.random() * colors.length),
        };
      }
      return box;
    });
    console.log(newBoxs);
    setBoxs(newBoxs);
  };

  return (
    <Fragment>
      <div>
        <button onClick={handleClickOdd}>Change odd colors</button>
        <button onClick={handleClickEven}>Change even colors</button>
      </div>
      <div style={{ display: "flex" }}>
        {boxs.map((box, index) => (
          <div
            key={box.id}
            style={{ ...boxStyle, backgroundColor: colors[box.color] }}
          >
            {index}
          </div>
        ))}
      </div>
    </Fragment>
  );
}
```

### Inserting into an array

`...`와 `slice`를 이용해 배열에서 원하는 위치에 데이터가 추가된 새로운 배열을 생성할 수 있다.

```jsx
onClick={() => {
  const insertAt = 1;
  const nextMyCoins = [
    ...myCoins.slice(0, insertAt),
    {
      id: crypto.randomUUID(),
      name: inputCoin,
    },
    ...myCoins.slice(insertAt),
  ];
  setMyCoins(nextMyCoins);
  setInputCoin("");
}}
```

### Making other changes to an array

새로운 배열을 만들어 메서드를 호출해야 한다.

```jsx
onClick={() => {
  const reversed = [...myCoins].reverse();
  setMyCoins(reversed);
}}
```

`[...myCoins]` spread syntax 를 이용해 배열을 복사한 후 `reverse()` or `sort()` or `newArr[0] = 'something'` 등을 이용해 새로운 배열에 변형을 가할 수 있다. ( 리액트에서는 어떠한 경우가 있더라도 상태 변수를 변형하면 안된다 )

❗️`...`는 **"얕은 복사"** 이기 때문에 객체들로 구성된 배열의 경우 객체의 레퍼런스가 복사되기 때문에 값의 변경을 하면 안되는 경우가 존재한다.

```jsx
nextMyCoins = [...myCoins];
nextMyCoins[0].name = "alt";
setMyCoins(nextMyCoins);
```

## Updating objects inside arrays

**Same 1**

```jsx
const arr = [
  { id: 1, name: "bitcoin" },
  { id: 2, name: "ethereum" },
];
```

**Same 2**

```jsx
const obj1 = { id: 1, name: "bitcoin" };
const obj2 = { id: 2, name: "ethereum" };
const arr = [obj1, obj2];
a;
```

- ~~어딘지는 기억이 나질 않는다~~ 배열(혹은 객체)내에 존재하는 _object_ 요소들은 실제로 그 안에 존재하는 것이 아니다. <u>객체의 포인터가 배열의 요소로 들어가 있다.</u>

**Problem**

```jsx
/*  배열에 들어있는 하나의 객체 요소
 *  { id: 0, title: 'Big Bellies', seen: false }
 */
function handleToggleMyList(artworkId, nextSeen) {
  const myNextList = [...myList];
  const artwork = myNextList.find((a) => a.id === artworkId);
  artwork.seen = nextSeen;
  setMyList(myNextList);
}
```

- `myNextList`는 `myList`와 다른 새로운 배열지만 동일한 객체들을 요소로 가지고 있다. 왜냐하면 spread syntax 의 "얕은 복사"로 객체의 포인터가 복사되었기 때문이다.
- `artwork.seen = nextSeen` 구문은 myNextList 를 통해 얻은 객체의 프로퍼티를 변형시켰지만 myList 에서도 접근 가능한 객체이다. ( **myList 의 객체 요소를 변형시킨 것과 같고 State 를 직접적으로 변형시켜서는 안되는 규칙을 어기게 된다** )

**Sovled**

```jsx
function handleToggleMyList(artworkId, nextSeen) {
  setMyList(
    myList.map((artwork) => {
      if (artwork.id === artworkId) {
        return { ...artwork, seen: nextSeen };
      }
      return artwork;
    })
  );
}
```

### Write concise update logic with immer

`useImmer`를 사용하면 _object_ 를 구성하고 있는 요소들의 깊이와 상관없이 1차원적으로 업데이트가 가능하다. "Immer"를 사용함으로써 **코드를 간결하게 작성**할 수 있다.
**Solved**

```jsx
const [myList, updateMyList] = useImmer(InitialList);
...
function handleToggledMyList(id, nextSeen) {
  updatemyList(draft => {
    const artwork = draft.find(a => a.id === id);
    artwork.seen = nextSeen;
  });
}
```

📜 [Array.prototype.find\(\) - JavaScript | MDN](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/find)
