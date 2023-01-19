# 4. props 를 통해 컴포넌트에게 값 전달하기

어떠한 값을 컴포넌트에게 전달해줘야 할 때, props를 사용

## props의 기본 사용법

예를 들어, App 컴포넌트에서 Hello 컴포넌트를 사용 할 때 `name` 이라는 값을 전달해주고 싶다고 가정

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <Hello name="react" />
  );
}

export default App;
```

Hello 컴포넌트에서 name 값을 사용하고 싶을 때

### Hello.js

```jsx
import React from 'react';

function Hello(props) {
  return <div>안녕하세요 {props.name}</div>
}

export default Hello;
```

컴포넌트에게 전달되는 props는 파라미터를 통하여 조회 가능

props는 객체 형태로 전달되며, 만약 `name` 값을 조회하고 싶다면 `props.name`을 조회하면 됨

## 여러 개의 props, 비구조화 할당

Hello 컴포넌트에 또 다른 props를 전달하기 위해 `color` 값 설정

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <Hello name="react" color="red"/>
  );
}

export default App;
```

그 다음 Hello 컴포넌트에서 `color` 값을 조회해서 폰트의 색상으로 설정

### Hello.js

```jsx
import React from 'react';

function Hello(props) {
  return <div style={{ color: props.color }}>안녕하세요 {props.name}</div>
}

export default Hello;
```

props 내부의 값을 조회할 때마다 `props.`를 입력해야 하는 불편함이 있음

-> 비구조화 할당 문법을 사용하면 조금 더 코드를 간결히 작성 가능

### Hello.js

```jsx
import React from 'react';

function Hello({ color, name }) {
  return <div style={{ color }}>안녕하세요 {name}</div>
}

export default Hello;
```

## defaultProps로 기본값 설정

컴포넌트에 props를 지정하지 않았을 때 기본적으로 사용할 값을 설정하고 싶다면 컴포넌트에 `defaultProps` 라는 값을 설정

### Hello.js

```jsx
import React from 'react';

function Hello({ color, name }) {
  return <div style={{ color }}>안녕하세요 {name}</div>
}

Hello.defaultProps = {
  name: '이름없음'
}

export default Hello;
```

App에서 name이 없는 Hello 컴포넌트를 렌더링

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <>
      <Hello name="react" color="red"/>
      <Hello color="pink"/>
    </>
  );
}

export default App;
```

![defaultProps](https://i.imgur.com/WXSoZyf.png)

## props.children

컴포넌트 태그 사이에 넣은 값을 조회하고 싶을 땐, `props.children`을 조회

### Wrapper.js

```jsx
import React from 'react';

function Wrapper() {
  const style = {
    border: '2px solid black',
    padding: '16px',
  };
  return (
    <div style={style}>

    </div>
  )
}

export default Wrapper;
```

이 컴포넌트를 App에서 사용

### App.js

```jsx
import React from 'react';
import Hello from './Hello';
import Wrapper from './Wrapper';

function App() {
  return (
    <Wrapper>
      <Hello name="react" color="red"/>
      <Hello color="pink"/>
    </Wrapper>
  );
}

export default App;
```

Wrapper 태그 내부에 Hello 컴포넌트 두 개가 있는데, 브라우저를 확인하면 다음과 같이 Hello 컴포넌트들은 보여지지 않을 것

![props.children unused](https://i.imgur.com/HO90Kwh.png)

내부의 내용이 보여지기 위해선 Wrapper에서 `props.children`을 렌더링 해주어야 함

### Wrapper.js

```jsx
import React from 'react';

function Wrapper({ children }) {    // props.children을 조회
  const style = {
    border: '2px solid black',
    padding: '16px',
  };
  return (
    <div style={style}>
      {children}
    </div>
  )
}

export default Wrapper;
```

![props.children used](https://i.imgur.com/WEe30dz.png)

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)