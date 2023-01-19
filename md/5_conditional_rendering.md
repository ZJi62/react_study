# 5. 조건부 렌더링

특정 조건에 따라 다른 결과물을 렌더링 하는 것

예시로 App 컴포넌트에서 Hello 컴포넌트를 사용할 때, `isSpecial`이라는 props를 설정

### App.js

```jsx
import React from 'react';
import Hello from './Hello';
import Wrapper from './Wrapper';


function App() {    // isSpecial의 true는 JS 값이기 때문에 중괄호로 감쌈
  return (
    <Wrapper>
      <Hello name="react" color="red" isSpecial={true}/>
      <Hello color="pink" />
    </Wrapper>
  )
}

export default App;
```

Hello 컴포넌트에서는 isSpecial이 `true`이나 `false`이냐에 따라서 컴포넌트의 좌측에 * 표시를 보여주게함

이를 처리하는 가장 기본적인 방법은 삼항연산자를 사용하는 것

### Hello.js

```jsx
import React from 'react';

function Hello({ color, name, isSpecial }) {
  return (
    <div style={{ color }}>
      { isSpecial ? <b>*</b> : null }
      안녕하세요 {name}
    </div>
  );
}

Hello.defaultProps = {
  name: '이름없음'
}

export default Hello;
```

`isSpecial`이 `true`일 때만 `<b>*</b>`를 렌더링하고, `false`일 때는 `null`을 렌더링

> JSX에서 null, false, undefined를 렌더링하게 된다면 아무것도 나타나지 않게 됨

![삼항연산자](https://i.imgur.com/Pmhtt0i.png)

내용이 달라지는 것이 아닌, 단순히 특정 조건이 `true`면 보여주고, 그렇지 않으면 숨겨주는 상황에서는 `&&` 연산자를 사용하는 것이 간편

### Hello.js

```jsx
import React from 'react';

function Hello({ color, name, isSpecial }) {
  return (
    <div style={{ color }}>
      {isSpecial && <b>*</b>}
      안녕하세요 {name}
    </div>
  );
}

Hello.defaultProps = {
  name: '이름없음'
}

export default Hello;
```

`isSpecial && <b>*</b>` 의 결과는 `isSpecial`이 `false`일 땐 `false`, `true`일 땐 `<b>*</b>`가 됨

## props 값 설정을 생략하면 ={true}

컴포넌트의 props 값을 설정하게 될 때 만약 props 이름만 작성하고 값 설정을 생략한다면, 이를 `true`로 간주

### App.js

```jsx
import React from 'react';
import Hello from './Hello';
import Wrapper from './Wrapper';

function App() {
  return (
    <Wrapper>
      <Hello name="react" color="red" isSpecial />
      <Hello color="pink"/>
    </Wrapper>
  );
}

export default App;
```

`isSpecial`을 이름만 넣어주면 `isSpecial={true}`로 설정한 것과 동일

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)