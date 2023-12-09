# Reacting to Input with State

**You will learn**

- How declarative UI programming differs from imperative UI programming
- How to enumerate the different visual states your component can be in
- How to trigger the changes between the different visual states from code

[공식문서](https://react.dev/learn/reacting-to-input-with-state)

---

## How declarative UI compares to imperative

**Imperative Programming** 는 모든 과정을 작성해야하지만 **Declrative Programming**은 목표로 하는 UI만 작성하면 된다.

==아래의 코드를 시작점으로 코드 작성해 보면서 공부하기==

**Start Code**

```jsx
export default function Form({ status = "empty" }) {
  if (status === "success") {
    return <h1>That's right!</h1>;
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>In which city is there a billboard that turns air into drinkable water?</p>
      <form>
        <textarea />
        <br />
        <button>Submit</button>
      </form>
    </>
  );
}
```

## Thinking about UI declaratively

### Step 1: Identify your component's different visual states

모든 조건에 대해서 대응되는 **UI 구조**를 작성한다.

- **Empty**: Form has a disabled “Submit” button.
- **Typing**: Form has an enabled “Submit” button.
- **Submitting**: Form is completely disabled. Spinner is shown.
- **Success**: “Thank you” message is shown instead of a form.
- **Error**: Same as Typing state, but with an extra error message.

```jsx
export default function Form({ status = "submitting" }) {
  if (status === "success") {
    return <h1>That&apos;s right!</h1>;
  }
  return (
    <>
      <h2>City quiz</h2>
      <p>In which city is there a billboard that turns air into drinkable water?</p>
      <form>
        <textarea />
        <br />
        <button disabled={status === "empty" || status === "submitting"}>Submit</button>
        {status === "error" && <p>Error</p>}
      </form>
    </>
  );
}
```

### Step 2: Determine what triggers those state changes

상태에 따라 UI를 업데이트해야 하기 때문에 리렌더링을 위한 **state variable**은 필수다.

- **Changing the text input** (human) should switch it from the *Empty* state to the *Typing* state or back, depending on whether the text box is empty or not.
- **Clicking the Submit button** (human) should switch it to the *Submitting* state.
- **Successful network response** (computer) should switch it to the *Success* state.
- **Failed network response** (computer) should switch it to the *Error* state with the matching error message.

### Step 3: Represent the state in memory with `useState`

즉각적으로 각 상태의 연관성을 파악해 최소한의 `useState`를 사용할 수 없다면 가능한 한 모든 상태에 대해서 사용하면 된다.

```jsx
const [answer, setAnswer] = useState("");
const [error, setError] = useState(null);

const [isEmpty, setIsEmpty] = useState(true);
const [isTyping, setIsTyping] = useState(false);
const [isSubmitting, setIsSubmitting] = useState(false);
const [isSuccess, setIsSuccess] = useState(false);
const [isError, setIsError] = useState(false);
```

### Step 4: Remove any non-essential state variables

굳이 필요 없는 state variable 을 삭제한다 ( 리팩토링 )

1. error와 isError 는 같은 맥락이기 때문에 하나만 존재하면 된다.
   - `isError` `error !== null`
   - isError 삭제
2. isEmpty는 글자수의 길이로 표현할 수 있다.
   - `isEmpty` `answer.length === 0`
   - isEmpty 삭제
3. isEmpty, isTyping 그리고 isSuccess는 하나의 상태로 관리할 수 있다.
   - `const [status, setStatus] = useState('typing')`
   - `// typing, submitting, success`
   - status 추가, ( isTyping, isSubmitting, isSuccess ) 삭제

```jsx
const [answer, setAnswer] = useState("");
const [error, setError] = useState(null);
const [status, setStatus] = useState("typing");
```

### Step 5: Connect the event handlers to set state

setting function 실행문을 포함한 이벤트 핸들러 함수를 생성한다.

```jsx
const handleSubmit = async (e) => {
  e.preventDefault();
  setStatus("submitting");
  await new Promise((res, rej) => {
    // 검사
    if (checking(answer)) {
      res();
    } else {
      rej(new Error("incorrect"));
    }
  })
    .then(() => {
      // 성공
      setStatus("success");
    })
    .catch((err) => {
      // 실패
      setError(err);
      setStatus("typing");
    });
};

function checking(answer) {
  return correct.includes(answer);
}
```

**Code**

```jsx
import { useState, useRef } from "react";

const correct = ["stacks", "rootstock"];

export default function Form() {
  const [answer, setAnswer] = useState("");
  const [error, setError] = useState(null);
  const [status, setStatus] = useState("typing");
  const textareaRef = useRef();

  const handleSubmit = async (e) => {
    e.preventDefault();
    // 제출한 후 검사하기
    setStatus("submitting");
    await new Promise((res, rej) => {
      if (checking(answer)) {
        res();
      } else {
        rej(new Error("incorrect"));
      }
    })
      .then(() => {
        // 성공
        setStatus("success");
      })
      .catch((err) => {
        // 실패
        setError(err);
        setStatus("typing");
        // 커서 이동
        textareaRef.current.focus();
      });
  };

  if (status === "success") {
    return <h1>That&apos;s right!</h1>;
  }
  return (
    <>
      <h2>Bitcoin Quiz</h2>
      <p>layer-2 coin on bitcoin</p>
      <form onSubmit={handleSubmit}>
        <textarea
          value={answer}
          onChange={(e) => {
            setAnswer(e.target.value);
            setError(null);
          }}
          ref={textareaRef}
        />
        <br />
        <button disabled={answer.length === 0 || status === "submitting"}>Submit</button>
        {error && <p>{error.message}</p>}
      </form>
    </>
  );
}

function checking(answer) {
  return correct.includes(answer);
}
```

---

## Deep Dive : Displaying many visual states at once

상태가 여러개라면 각각의 상태에 대한 UI(컴포넌트)를 한페이지에 표시하는 것이 좋다. 이를 "living styleguids" or "storybooks" 라고 한다.

➭ [Reacting to Input with State](https://react.dev/learn/reacting-to-input-with-state#displaying-many-visual-states-at-once)

## Deep Dive : Eliminating "impossible" states with a reducer

Reducers는 다수의 상태 변수를 하나의 객체에서 관리할 수 있도록 한다.

```jsx
const [answer, setAnswer] = useState("");
const [error, setError] = useState(null);
const [status, setStatus] = useState("typing");
```

- 위의 상태 변수 3개도 충분하지만 리듀서를 이용한다면 더 줄일 수 있다고 한다...

➭ [Extracting State Logic into a Reducer](https://react.dev/learn/extracting-state-logic-into-a-reducer)
