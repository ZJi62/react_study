# 2. 리액트 컴포넌트 생성

### Hello.js

```jsx
import React from 'react';

function Hello() {
  return <div>안녕하세요</div>
}

export default Hello;
```

리액트 컴포넌트를 만들 때는

```jsx
import React from 'react';
```

를 통하여 리액트를 불러와 주어야 함

지금은 함수로 리액트 컴포넌트를 작성하는 방법에 대해서만 알아봄

리액트 컴포넌트에서는 XML 형식의 값을 반환 가능, 이를 **JSX** 라 부름

코드의 최하단

```jsx
export default Hello;
```

이 코드는 Hello 라는 컴포넌트를 내보내겠다는 의미 -> 이렇게 하면 다른 컴포넌트에서 불러와서 사용 할 수 있음

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <div>
      <Hello />
    </div>
  );
}

export default App;
```

상단에 있던

```jsx
import logo from './logo.svg';
import './App.css';
```

는 SVG 파일을 불러오고, CSS 를 적용하는 코드인데 이는 현재 불필요하므로 제거

컴포넌트는 일종의 UI 조각이며, 쉽게 재사용 가능

```jsx
import React from 'react';
import Hello from './Hello';


function App() {
  return (
    <div>
      <Hello />
      <Hello />
      <Hello />
    </div>
  );
}

export default App;
```

### index.js

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

ReactDOM.render(<App />, document.getElementById('root'));

// If you want your app to work offline and load faster, you can change
// unregister() to register() below. Note this comes with some pitfalls.
// Learn more about service workers: https://bit.ly/CRA-PWA
serviceWorker.unregister();
```

여기서 ReactDOM.render 의 역할은 브라우저에 있는 실제 DOM 내부에 리액트 컴포넌트를 렌더링하겠다는 것 -> `id`가 `root`인 DOM을 선택중

public/index.html 을 열어보면 내부에

```html
<div id="root"></div>
```

을 찾아볼 수 있음

리액트 컴포넌트가 렌더링 될 때 렌더링 된 결과물이 위 div 내부에 렌더링 되는 것

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)