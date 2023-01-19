# 6. useState 를 통해 컴포넌트에서 바뀌는 값 관리하기

컴포넌트에서 보여줘야 하는 내용이 사용자 인터랙션에 따라 바뀌어야 할 때 어떻게 구현할 수 있는지에 대해 알아보기

(리액트 16.8) Hooks 라는 기능이 도입되면서 함수형 컴포넌트에서도 상태를 관리할 수 있음

useState 라는 함수를 사용 -> 리액트의 Hooks 중 하나

예제로 버튼을 누르면 숫자가 바뀌는 Counter 컴포넌트 만들기

## Counter.js

```jsx
import React from 'react';

function Counter() {
  return (
    <div>
      <h1>0</h1>
      <button>+1</button>
      <button>-1</button>
    </div>
  );
}

export default Counter;
```

그 다음 App에서 Counter를 렌더링

## App.js

```jsx
import React from 'react';
import Counter from './Counter';

function App() {
  return (
    <Counter />
  );
}

export default App;
```

![Counter](https://i.imgur.com/vDFvQx6.png)

## 이벤트 설정

Counter 에서 버튼이 클릭되는 이벤트가 발생했을 때, 특정 함수가 호출되도록 설정

### Counter.js

```jsx
import React from 'react';

function Counter() {
  const onIncrease = () => {
    console.log('+1')
  }
  const onDecrease = () => {
    console.log('-1');
  }
  return (
    <div>
      <h1>0</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;
```

함수를 만들고, button의 `onClick`으로 각 함수를 연결해줌(리액트에서 엘리먼트에 이벤트를 설정해줄 때에는 `on이벤트이름={실행하고싶은함수})` 형태로 설정해주어야 함)

> 함수 형태를 넣어주어야 하며, 함수를 다음과 같이 실행해선 안됨
>> ```jsx
>> onclick = {onIncrease()}     // 렌더링 되는 시점에서 함수가 호출되어 버리기 때문임(이벤트를 설정할 땐 함수타입의 값을 넣어주어야 함)

![Counter acting](https://i.imgur.com/534RyIz.png)

## 동적인 값 끼얹기, useState

컴포넌트에서 동적인 값을 상태(state)라고 부름

**useState** -> 컴포넌트에서 상태를 관리 가능

### Counter.js

```jsx
import React, { useState } from 'react';

function Counter() {
  const [number, setNumber] = useState(0);

  const onIncrease = () => {
    setNumber(number + 1);
  }

  const onDecrease = () => {
    setNumber(number - 1);
  }

  return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;
```

```jsx
import React, { useState } from 'react';
```

이 코드는 리액트 패키지에서 `useState` 라는 함수를 불러와줌

```jsx
const [number, setNumber] = useState(0);
```

`useState`를 사용 할 때는 상태의 기본값을 파라미터로 넣어서 호출해줌

이 함수를 호출해주면 배열이 반환됨(여기서 첫 번째 원소는 현재 상태, 두 번째 원소는 Setter 함수)

원래는 다음과 같이 해야함

```jsx
const numberState = useState(0);
const number = numberState[0];
const setNumber = numberState[1];
```

배열 비구조화 할당을 통해 각 원소를 추출해 준 것

```jsx
  const onIncrease = () => {
    setNumber(number + 1);
  }

  const onDecrease = () => {
    setNumber(number - 1);
  }
```

Setter 함수는 파라미터로 전달 받은 값을 최신 상태로 설정해줌

```jsx
    <h1>{number}</h1>
```

h1 태그에서는 이제 0이 아닌 `{number}` 값을 보여주어야 함

![Counter change](https://i.imgur.com/8LxuRm1.png)

## 함수형 업데이트

Setter 함수를 사용할 때, 업데이트 하고싶은 새로운 값을 파라미터로 넣어주고 있음

그 대신 기존 값을 어떻게 업데이트 할 지에 대한 함수를 등록하는 방식으로도 값을 업데이트 가능

### Counter.js

```jsx
import React, { useState } from 'react';

function Counter() {
  const [number, setNumber] = useState(0);

  const onIncrease = () => {
    setNumber(prevNumber => prevNumber + 1);
  }

  const onDecrease = () => {
    setNumber(prevNumber => prevNumber - 1);
  }

  return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;
```

`onIncrease` 와 `onDecrease` 에서 `setNumber` 을 사용할 때 그 다음 상태를 파라미터로 넣어준 것이 아니라, 값을 업데이트 하는 함수를 파라미터로 넣어줌

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)