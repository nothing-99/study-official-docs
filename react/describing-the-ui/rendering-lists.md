# Rendering Lists

data를 가진 배열로부터 각 요소를 이용한 리액트 컴포넌트의 배열을 생성 `filter()` `map()`

**You will learn**

- How to render components from an array using JavaScript's `map()`
- How to render only specific components using JavaScript's `filter()`
- When and why to use React keys

---

## Rendering data from arrays

동일한 타입의 데이터들을 담고 있는 배열에서 `map()` `filter()` JS에서 지원하는 메서드를 이용해 **컴포넌트 인스턴스들로 구성된 새로운 배열을 만들 수 있다**. 리액트는 이 배열의 요소들을 렌더링한다.

```jsx
// data array
const people = [
  "Creola Katherine Johnson: mathematician",
  "Mario José Molina-Pasquel Henríquez: chemist",
  "Mohammad Abdus Salam: physicist",
  "Percy Lavon Julian: chemist",
  "Subrahmanyan Chandrasekhar: astrophysicist",
];

export default function List() {
  // component instance array
  const listItems = people.map((person) => <li>{person}</li>);
  return <ul>{listItems}</ul>;
}
```

- 렌더링은 성공적으로 완료되지만 콘솔창에 에러 메세지를 뜬다.

```shell
Warning: Each child in a list should have a unique "key" prop.

Check the render method of `List`. See https://reactjs.org/link/warning-keys for more information.
    at li
    at List
```

## Filtering arrays of items

- 글자 그대로 필터링을 위한 함수를 주고 리턴값이 `true` 인 요소들로만 구성된 새로운 배열을 리턴해준다. ( JavaScript 에서 제공하는 built-in method 이다 )
  - [JavaScript reference - JavaScript | MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference)
  - [Built-in Methods in Javascript 🤓](https://dev.to/elpepebenitez/built-in-methods-in-javascript-4bll)

```jsx
import { people } from "./data.js";
import { getImageUrl } from "./utils.js";

export default function List() {
  const chemists = people.filter((person) => person.profession === "chemist");
  const listItems = chemists.map((person) => (
    <li>
      <img
        src={getImageUrl(person)}
        alt={person.name}
      />
      <p>
        <b>{person.name}:</b>
        {" " + person.profession + " "}
        known for {person.accomplishment}
      </p>
    </li>
  ));
  return <ul>{listItems}</ul>;
}
```

- [리액트에서 제공하는 Full code](https://codesandbox.io/s/spzfqd?file=%2FApp.js&utm_medium=sandpack)
- 동일하게 렌더링은 성공적이지만 콘솔창에 에러 메세지가 보인다

```bash
Warning: Each child in a list should have a unique "key" prop.

Check the render method of `List`. See https://reactjs.org/link/warning-keys for more information.
    at li
    at List
```

### Pitfall

arrow function 을 사용할 때

- 한 줄의 코드로 리턴값을 작성할 수 있다면 `=>` 이후에 바로 작성하면 된다
- 한 줄 이상의 코드를 작성할 필요가 있다면 `=>` 이후에 `{ ... }` curly braces 내부에 코드를 작성하고 리턴값이 필요하다면 `return` 문을 작성해야 한다.

```js
let arr = [1, 2, 3, 4, 5];
let arr2 = arr.map((v) => {
  console.log(v);
});
console.log(arr2); // [undefined, undefined, undefined, undefined, undefined]
```

## Keeping list items in order with `key`

- 한 배열에서 각 요소들을 식별하기 위한 **식별자로써의 역할을 수행**한다
- `string` | `number`
- 리액트가 요소의 변화를 파악하고 정확한 DOM tree 업데이트를 하도록 하기 위해 적절한 `key` 값을 설정해야 한다. ( 중요하다 )

> [Note] **`map()`을 호출시 모든 JSX 요소들에 `key` 값을 작성해줘야 한다.** JSX에서 `map()`의 호출을 통해 JSX element를 요소로 갖는 새로운 배열을 리턴할 것이고 각 요소들을 식별하기 위한 `key` 값을 작성해야 한다.

### Where to get your `key`

- 데이터베이스로부터 받는 데이터에는 각 데이터마다 고유한 key/id 가 존재하기 때문에 그것을 `key` 값으로 사용하면 된다
- 그 외에는 `crypto.randomUUID()` or `uuid` 를 사용하면 된다
  - [Crypto: randomUUID\(\) method - Web APIs | MDN](https://developer.mozilla.org/en-US/docs/Web/API/Crypto/randomUUID)
  - [uuid](https://www.npmjs.com/package/uuid)
- `map()` or `filter()` 를 통해 데이터 배열로부터 JSX 요소들의 새로운 배열을 만들 때 `key`을 생성하는 것이 아니라 데이터를 생성할 때 `key`을 생성해야 한다.

### Rules of keys

- ~동일한 배열에 존재하는 JSX요소들에는~ `key`를 명시해야 한다
  - 다른 배열에 있는 요소들끼리는 동일한 키값을 가져도 상관없다
    - [Index as a key is an anti-pattern](https://robinpokorny.medium.com/index-as-a-key-is-an-anti-pattern-e0349aece318#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImY4MzNlOGE3ZmUzZmU0Yjg3ODk0ODIxOWExNjg0YWZhMzczY2E4NmYiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMTM3OTMzODY5MTU5Nzc5MzM0NTkiLCJlbWFpbCI6ImhhbWdhamE5NUBnbWFpbC5jb20iLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwibmJmIjoxNjk5NjEwMzE0LCJuYW1lIjoiYXBlIGZyb2ciLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUNnOG9jSUhlN2pyVDB6VDZPZ2dzbWd2UFNMQlZmUzFkVkZIakJ2LVZ0Z2VubTJlPXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6ImFwZSIsImZhbWlseV9uYW1lIjoiZnJvZyIsImxvY2FsZSI6ImtvIiwiaWF0IjoxNjk5NjEwNjE0LCJleHAiOjE2OTk2MTQyMTQsImp0aSI6IjY5ZjA5Y2E0ZGU1NzExYWZmZjZkNmM1YzQ3ZmEyNjk1YzczNTQ4MTAifQ.T7bfDbHMJzhnPP16dIsrHpKXcW0H7vsNdEYXtHP-sQUMSm53hmcj7TzD9P5Aj726tyZJABcEA5Q75x-oVxeEYbPy4f8bjlKdcmhgL3O_m0jb4FdhajjZgzJldS7Z9wwMWwxH_x0QichnS2E-x1CYddY2yAlOUf9UjCoiK9Jh-9Fq7nWMdqrL3pg9s9kFDvSNvcUEDwxN0ADLHTAEaLho_sHADzB9YFSAVypkkSk6Da6SOWcXLDctzGxIn8NhtpEM0KJI-BMy-Eewv9tQ55CREkEXv9Wcn0Nz0ac_QgCJGqobc7CIcVMeJ2x_I42WWYk2PJK8poqlnMGCrph2DiIVBg) (git upload)

### Why does React need keys?

완전히 일치하지는 않겠지만 디렉토리를 예시로 들어본다.

만약 한 폴더에 동일한 이름과 확장자를 가진 파일 2개가 존재한다고 해보자. 사용자는 이 2개의 파일을 어떻게 구분할 것인가? 파일크기? 수정날짜? 생성날짜?... 이러한 것들은 직관적인 식별이 불가능하고 기억을 하거나 다른 곳에 기록을 해놔야 한다. 식별하기 위해 다른 큰? 노력들이 요구된다. ( ~식별이 어렵다...~ )

리액트의 기준으로 생각해본다

- 폴더 -> JSX요소들을 가진 하나의 배열
- 파일 -> JSX요소
- 사용자 -> 리액트

**리액트가 한 배열에서 JSX요소들을 직관적으로 (쉽게) 식별하기 위해 `key`가 필요한 것이다.** ( 이것은 의도하는 내용을 정확히 반영한 DOM-tree 구성을 가능하게 한다 )

### Pitfall

- 리액트는 따로 설정하지 않을 경우 **index를 key값으로 사용하지만 그렇게 하면 안된다.** 배열에 요소가 추가되거나 삭제되거나 혹은 재정렬될 경우 문제가 발생할 수 있기 때문이다.
  - [Index as a key is an anti-pattern](https://robinpokorny.medium.com/index-as-a-key-is-an-anti-pattern-e0349aece318#id_token=eyJhbGciOiJSUzI1NiIsImtpZCI6ImY4MzNlOGE3ZmUzZmU0Yjg3ODk0ODIxOWExNjg0YWZhMzczY2E4NmYiLCJ0eXAiOiJKV1QifQ.eyJpc3MiOiJodHRwczovL2FjY291bnRzLmdvb2dsZS5jb20iLCJhenAiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJhdWQiOiIyMTYyOTYwMzU4MzQtazFrNnFlMDYwczJ0cDJhMmphbTRsamRjbXMwMHN0dGcuYXBwcy5nb29nbGV1c2VyY29udGVudC5jb20iLCJzdWIiOiIxMTM3OTMzODY5MTU5Nzc5MzM0NTkiLCJlbWFpbCI6ImhhbWdhamE5NUBnbWFpbC5jb20iLCJlbWFpbF92ZXJpZmllZCI6dHJ1ZSwibmJmIjoxNjk5NjEwMzE0LCJuYW1lIjoiYXBlIGZyb2ciLCJwaWN0dXJlIjoiaHR0cHM6Ly9saDMuZ29vZ2xldXNlcmNvbnRlbnQuY29tL2EvQUNnOG9jSUhlN2pyVDB6VDZPZ2dzbWd2UFNMQlZmUzFkVkZIakJ2LVZ0Z2VubTJlPXM5Ni1jIiwiZ2l2ZW5fbmFtZSI6ImFwZSIsImZhbWlseV9uYW1lIjoiZnJvZyIsImxvY2FsZSI6ImtvIiwiaWF0IjoxNjk5NjEwNjE0LCJleHAiOjE2OTk2MTQyMTQsImp0aSI6IjY5ZjA5Y2E0ZGU1NzExYWZmZjZkNmM1YzQ3ZmEyNjk1YzczNTQ4MTAifQ.T7bfDbHMJzhnPP16dIsrHpKXcW0H7vsNdEYXtHP-sQUMSm53hmcj7TzD9P5Aj726tyZJABcEA5Q75x-oVxeEYbPy4f8bjlKdcmhgL3O_m0jb4FdhajjZgzJldS7Z9wwMWwxH_x0QichnS2E-x1CYddY2yAlOUf9UjCoiK9Jh-9Fq7nWMdqrL3pg9s9kFDvSNvcUEDwxN0ADLHTAEaLho_sHADzB9YFSAVypkkSk6Da6SOWcXLDctzGxIn8NhtpEM0KJI-BMy-Eewv9tQ55CREkEXv9Wcn0Nz0ac_QgCJGqobc7CIcVMeJ2x_I42WWYk2PJK8poqlnMGCrph2DiIVBg) (git upload)
- `key={Math.random()}`와 같이 **component를 생성할 때 key값을 생성해서는 안된다.** 렌더링할 때마다 새로운 컴포넌트와 DOM을 생성하기에 성능과 UX에 악영향을 준다. **데이터에 존재하는 식별할 수 있는 값을 이용하거나 데이터를 생성할 때 식별할 수 있는 값도 같이 만들어서 사용하자**
- `key`프로퍼티는 **오직 리액트가 컴포넌트를 식별하는 용도로만 사용해야 한다.** 만약 컴포넌트를 식별하기 위한 프로퍼티가 필요할 때 **별개의 프로퍼티를 만들어서 사용하자**. `<Profile key={id} userId={id} />`

---

## Deep Dive : Displaying serveral DOM nodes for each list item

데이터 배열이 있고 한 요소에서 여러개의 DOM 노드를 생성해 리턴하고자 할 때 어떻게 해야할까? 리턴문에 괄호를 열고 여러개의 DOM 노드를 동일한 레벨에 작성한 후 괄호를 닫으면 의도한 결과를 얻지 못한다. ( 아마 가장 위에 작성한 리액트 컴포넌트에 매칭되는 DOM 노드만 생성될 것이다 )

방법은 간단하다. **다른 요소들을 Wrapping 한 Wrapping Component 하나만 리턴되도록 작성하면 된다.** 대게 Wrapping 컴포넌트로 `<div>...</div>` `<>...</>` `<React.Fragment>...</React.Fragment>` 이용할 수 있는데 `key`값이 필요한 경우 `<>...</>` 는 프로퍼티 값을 넘길 수 없기 때문에 사용할 수 없다.

- `div` : DOM 트리에 포함 ✅
- `Fragment` : DOM 트리에 포함 ❌

```jsx
const listItems = people.map((person) => (
  <React.Fragment key={person.id}>
    <h1>{person.name}</h1>
    <p>{person.bio}</p>
  </React.Fragment>
));
```
