# 3. JSX의 기본 규칙 알아보기

```jsx
return <div>안녕하세요</div>;
```

리액트 컴포넌트 파일에서 XML 형태로 코드를 작성하면 babel이 JSX를 JS로 변환해줌

JSX가 JS로 제대로 변환이 되려면 지켜야 할 몇가지 규칙이 있음

## 1 꼭 닫혀야 하는 태그

태그는 꼭 닫혀있어야 함

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <div>
      <Hello />
      <Hello />
      <Hello />
      <div>        // 태그를 열었으면 닫는 태그가 필요
    </div>
  );
}

export default App;
```

태그와 태그 사이에 내용이 들어가지 안ㅇㅎ을 때에는, Self Closing 태그 라는 것을 사용해야 함 -> `<Hello />`처럼 작성

## 2 꼭 감싸져야하는 태그

두 개 이상의 태그는 무조건 하나의 태그로 감싸져 있어야 함

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <Hello />
    <div>안녕히계세요.</div>
  );
}

export default App;
```

위 코드는 에러가 발생

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <div>                   // 두 개 이상의 태그가 있으므로 하나의 태그로 감싸줌
      <Hello />
      <div>안녕히계세요</div>
    </div>
  );
}

export default App;
```

이렇게 단순히 감싸기 위해 불필요한 div로 감싸는 것은 별로 좋지 않은 상황도 있음

이럴 땐, 리액트의 Fragment 라는 것을 사용하면 됨

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  return (
    <>
      <Hello />
      <div>안녕히계세요</div>
    </>
  );
}

export default App;
```

태그를 작성할 때, 이름 없이 작성하면 Fragment가 만들어지는데, Fragment는 브라우저 상에서 별도의 엘리먼트로 나타나지 않음

## 3 JSX 내부에서 자바스크립트 값 사용하기

JSX 내부에서 자바스크립트 값을 보여 주고 싶을 때에는 `{}`를 사용

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  const name = 'react';
  return (
    <>
      <Hello />
      <div>{name}</div>
    </>
  );
}

export default App;
```

### style & className

JSX 에서 태그에 `style`과 CSS class를 설정하는 방법은 HTML 에서 설정하는 방법과 다름

인라인 스타일은 객체 형태로 작성을 해야 하며, `background-color`처럼 `-`로 구분되어 있는 이름들은 `backgroundColor`처럼 카멜 표기법으로 작성해야 함

### App.js

```jsx
import React from 'react';
import Hello from './Hello';

function App() {
  const name = 'react';
  const style = {
    backgroundColor: 'black',
    color: 'aqua',
    fontSize: 24, // 기본 단위 px
    padding: '1rem' // 다른 단위 사용 시 문자열로 설정
  }

  return (
    <>
      <Hello />
      <div style={style}>{name}</div>
    </>
  );
}

export default App;
```

CSS class 를 설정 할 때 `class=`가 아닌 `className=`으로 설정을 해주어야 함

### App.css

```css
.gray-box {
  background: gray;
  width: 64px;
  height: 64px;
}
```

그 다음 App.js 를 다음과 같이 수정

### App.js

```jsx
import React from 'react';
import Hello from './Hello';
import './App.css';


function App() {
  const name = 'react';
  const style = {
    backgroundColor: 'black',
    color: 'aqua',
    fontSize: 24, // 기본 단위 px
    padding: '1rem' // 다른 단위 사용 시 문자열로 설정
  }

  return (
    <>
      <Hello />
      <div style={style}>{name}</div>
      <div className="gray-box"></div>  // className 으로 설정
    </>
  );
}

export default App;
```

## 4 주석

JSX 내부에서 주석을 작성할 때에는 `{/* */}`를 사용

추가적으로, 열리는 태그 내부에서는 `// 이런 형태로도` 주석 작성이 가능

### App.js

```jsx
import React from 'react';
import Hello from './Hello';
import './App.css';


function App() {
  const name = 'react';
  const style = {
    backgroundColor: 'black',
    color: 'aqua',
    fontSize: 24, // 기본 단위 px
    padding: '1rem' // 다른 단위 사용 시 문자열로 설정
  }

  return (
    <>
      {/* 주석은 화면에 보이지 않습니다 */}
      /* 중괄호로 감싸지 않으면 화면에 보입니다 */
      <Hello 
        // 열리는 태그 내부에서는 이렇게 주석을 작성 할 수 있습니다.
      />
      <div style={style}>{name}</div>
      <div className="gray-box"></div>
    </>
  );
}

export default App;
```

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)