# Queueing a Series of State Updates

<<<<<<< HEAD
Setter 함수를 호출하는 것은 렌더링을 예약(queue)하는 것이다.

**You will learn**

- What “batching” is and how React uses it to process multiple state updates
- How to apply several updates to the same state variable in a row
=======
Setter 함수를 호출하는 것은 렌더링을 예약(queue)하는 것이다. 

**You will learn**
* What “batching” is and how React uses it to process multiple state updates
* How to apply several updates to the same state variable in a row
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044

[공식문서](https://react.dev/learn/queueing-a-series-of-state-updates)

---
<<<<<<< HEAD

## React batches state updates

- 이벤트 핸들러 내에 여러개의 `setState()`가 있더라도 이벤트 핸들러 함수 실행을 끝낸 후 <u>state 업데이트를 일괄처리한다</u> (==**batching**==)
=======
## React batches state updates
- 이벤트 핸들러 내에 여러개의 `setState()`가 있더라도 이벤트 핸들러 함수 실행을 끝낸 후 ~state 업데이트를 일괄처리한다~ (==**batching**==)
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044
- "click"과 같은 이벤트들은 일괄처리하지 않고 별도로 하나씩 처리한다
- **`setState()`를 호출하면 인자로 넘긴 함수나 값을 "Queue" 에 저장**하고 이벤트 함수가 끝난 후 하나씩 처리한다

## Updating the same state multiple times before the next render
<<<<<<< HEAD

### What happens if you update state after replacing it

=======
### What happens if you update state after replacing it
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044
### What happens if you replace state after updating it

- `setState(() => { ... })` (==**Updater Function**==) : 큐에서 바로 직전에 얻은 업데이트된 state 값을 이용할 수 있다.
- `setState(value)` ( `setState(state => value)` 와 같다 )
- Queue에 넣을 때
  - `setState(() => { ... })` -> `() => { ... }`
  - `setState(value)` -> `replace with "value"`
<<<<<<< HEAD
- 새로운 렌더링이 시작되면 queue에서 차례대로 꺼내 상태 업데이트를 하기 때문에 큐에 추가할 수 있는 ==updater function 은 무조건 **pure function** 으로 작성==해야 한다. ( **<u>렌더링 도중에 실행되는 함수이기 때문이다~</u>** )

**Example**

```jsx
<button
  onClick={() => {
    setNumber(number + 1);
    setNumber(number + 1);
    setNumber(number + 1);
  }}
>
  +3
</button>
=======
- 새로운 렌더링이 시작되면 queue에서 차례대로 꺼내 상태 업데이트를 하기 때문에 큐에 추가할 수 있는 ==updater function 은 무조건 **pure function** 으로 작성==해야 한다. ( **~렌더링 도중에 실행되는 함수이기 때문이다~** )

**Example**
```jsx
<button onClick={() => {
  setNumber(number + 1);
  setNumber(number + 1);
  setNumber(number + 1);
}}>+3</button>
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044
```

\-\-\-렌더링 시작\-\-\-
**queue**
<<<<<<< HEAD
| queued update | n | returns |
|------------------|-----|---------|
| "replace with 1" | 0 | 1 |
| "replace with 1" | 1 | 1 |
| "replace with 1" | 1 | 1 |

- `[1, setNumber]`

**Example**

```jsx
<button
  onClick={() => {
    setNumber(number + 5);
    setNumber((n) => n + 1);
    setNumber(42);
  }}
>
  +3
</button>
=======
| queued update    | n   | returns |
|------------------|-----|---------|
| "replace with 1" | 0   | 1       |
| "replace with 1" | 1   | 1       |
| "replace with 1" | 1   | 1       |
- `[1, setNumber]`

**Example**
```jsx
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
  setNumber(42);
}}>+3</button>
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044
```

\-\-\-렌더링 시작\-\-\-
**queue**
<<<<<<< HEAD
| queued update | n | returns |
|-------------------|-----|---------|
| "replace with 5" | 0 | 5 |
| `n => n + 1` | 5 | 6 |
| "replace with 42" | 6 | 42 |

- `[42, setNumber]`

**Example**

```jsx
<button
  onClick={() => {
    setNumber(number + 5);
    setNumber((n) => n + 1);
    setNumber(42);
    setTimeout(() => alert(number), 5000);
  }}
>
  +3
</button>
```

=======
| queued update     | n   | returns |
|-------------------|-----|---------|
| "replace with 5"  | 0   | 5       |
| `n => n + 1`      | 5   | 6       |
| "replace with 42" | 6   | 42      |
- `[42, setNumber]`

**Example**
```jsx
<button onClick={() => {
  setNumber(number + 5);
  setNumber(n => n + 1);
  setNumber(42);
  setTimeout(() => alert(number), 5000);
}}>+3</button>
```
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044
- `() => alert(number)`가 스케쥴링된 시점의 JSX snapshot에 존재하는 state 값은 `0` 이기 때문에 새로운 렌더링의 새로운 JSX snapshot 과 상관없이 `0` 이 메세징 된다.
- UI에 `number` 을 출력하는 부분이 있다면 `42` 가 보일 것이다.

### Naming conventions
<<<<<<< HEAD

```jsx
// 1
setLastName((ln) => ln.reverse());
// 2
setLastName((lastName) => lastName.reverse());
// 3
setLastName((prevLastName) => prevLastName.reverse());
```
=======
```jsx
// 1
setLastName(ln => ln.reverse());
// 2
setLastName(lastName => lastName.reverse());
// 3
setLastName(prevLastName => prevLastName.reverse());
```
---
#react.dev/adding-interactivity/queueing-a-series-of-state-updates#
>>>>>>> 08914c21a5a1811d84dcd63224b6a9b9c296b044
