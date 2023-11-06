#react.dev/describing-the-ui/javascript-in-jsx-with-curly-braces#
# Javascript in JSX with Curly Braces
**"Curly Braces" 를 이용해** JSX 코드 내에 ~JavaScript Logic or Reference~ 를 추가할 수 있다

**You will learn**
- How to pass strings with quotes
- How to reference a JavaScript variable inside JSX with curly braces
- How to call a JavaScript function inside JSX with curly braces
- How to use a JavaScript object inside JSX with curly braces
---
## Passing strings with quotes
- 문자열을 직접 사용할 때는 `"` or `'` 를 이용하면 된다
- 변수를 사용할 때는 ~**~~`{ variable }`  형태로 이용하면 된다**~~~
- **"Curly Braces" 는 JSX 내에서 JavaScript 코드를 사용할 수 있도록 해준다**

```jsx
export default function Avatar() {
  const description = 'Dog';
  const url = 'https://picsum.photos/id/237/200/300';
  return (
    <img
      className="avatar"
      src={url}
      alt={description}
    />
  );
}
```

## Using curly braces: A window into the JavaScript world
- Curly Braces는 JSX내에서 자바스크립트 로직과 변수를 사용가능하게 한다.

**JSX에서 curly braces 를 사용하는 경우**
- As text `<h1>{name}'s To Do List</h1>`
- As attributes `<img src={avatar} />` 

## Using "double curlies" : CSS and other objects in JSX
값으로 "object" 를 넘겨야 하는 경우 "JSX에서 JavaScript 코드를 작성하기 위한 { }", "object 를 표현하는 { }" 이 2가지로 인해 ~"Double Curlies"~ 를 사용한다.

"Double Curlies" 는 특별한 개념이 아니다. 단지 "Double Curlies 를 사용하는 경우는 JSX에서 object를 값으로 넘기는 것이다"

> [주의] style attribute 에 object 를 넘길 때 object 의 key 값들은 모두 camelCase 형식을 따라야 한다

## More fun with JavaScript objects and curly braces
`{ backgroundColor: 'black', color: 'white' }`

```jsx
<div style={{ backgroundColor: 'black', color: 'white' }}>...</div>
```

```jsx
const theme = { backgroundColor: 'black', color: 'white' };
<div style={theme}>...</div>
```
