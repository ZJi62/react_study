# HOOK

## state Hook

버튼을 클릭하면 값이 증가하는 간단한 카운터 예시

```jsx
import React, { useState } from 'react';

function Example() {
  // "count"라는 새 상태 변수를 선언합니다
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

여기서 `useState`가 바로 Hook

Hook을 호출해 함수 컴포넌트 안에 state를 추가(이 state는 컴포넌트가 다시 렌더링 되어도 그대로 유지될 것)

`useState`는 현재의 state 값과 이 값을 업데이트하는 함수를 쌍으로 제공

이 함수는 에번트 핸들러나 다른 곳에서 호출 가능

class의 `this.setState`와 거의 유사하지만, 이전 state와 새로운 state를 합치지 않는다는 차이점

`useState`는 인자로 초기 state 값을 하나 받음(카운터는 0부터 시작하기 때문에 위 예시에서는 초기값으로 0을 넣어줌)

`this.state`와 달리 `useState` Hook의 state는 객체일 필요 X(이 초기값은 첫 번재 렌더링에만 딱 한번 사용됨)

## 여러 state 변수 선언하기

하나의 컴포넌트 내에서 State Hook을 여러 개 사용 가능

```jsx
function ExampleWithManyStates() {
  // 상태 변수를 여러 개 선언했습니다!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: 'Learn Hooks' }]);
  // ...
}
```

배열 구조 분해(destructuring) 문법은 `useState`로 호출된 state 변수들을 다른 변수명으로 할당할 수 있게 해줌(이 변수명은 `useState` API와 관련이 없음)

React는 매번 렌더링 할 때 `useState`가 사용된 순서대로 실행할 것

## 그럼 Hook은 무엇인가?

Hook은 함수 컴포넌트에서 React state와 생명주기 기능을 "연동(hook into)" 할 수 있게 해주는 함수(Hook은 class 안에서는 동작 X)

class 없이 React를 사용할 수 있게 해주는 것(하지만 이미 짜놓은 컴포넌트를 모조리 재작성 하는 것은 권장하지 않음)

## Effect Hook

React 컴포넌트 안에서 데이터를 가져오거나 구독하고, DOM을 직접 조작하는 작업 -> side effects

Effect Hook, 즉 `useEffect`는 함수 컴포넌트 내에서 이런 side effects를 수행할 수 있게 해줌

React class의 `componentDidMount`와 `componentDidUpdate`와 같은 목적으로 제공되지만, 하나의 API로 통합된 것

예시로, React가 DOM을 업데이트한 뒤에 문서의 타이틀을 바꾸는 컴포넌트

> DOM 이란?

>> Document Object Model의 약자로, HTML 문서를 객체로 표현한 것, 문서의 구조화된 표현을 제공하며 프로그래밍 언어가 DOM 구조에 접근할 수 있는 방법을 제공하여 그들이 문서 구조, 스타일, 내용 등을 변경할 수 있게 돕는다.

```jsx
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // componentDidMount, componentDidUpdate와 비슷합니다
  useEffect(() => {
    // 브라우저 API를 이용해 문서의 타이틀을 업데이트합니다
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

`useEffect`를 사용하면, Reactsms DOM을 바꾼 뒤에 "effect" 함수를 실행할 것

Effects는 컴포넌트 안에 선언되어 있기 때문에 props와 state에 접근 가능

기본적으로 React는 매 렌더링 이후에 effects를 실행

Effect를 "헤제"할 필요가 있다면, 해제하는 함수를 반환해주면 됨

예시로 이 컴포넌트는 친구의 접속 상태를 구독하는 effect를 사용했고, 구독을 해지함으로써 해제해줌

```jsx
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

이 예시에서 컴포넌트가 unmount될 때 React는 `ChatAPI` 구독을 해제할 것

또한 재 렌더링이 일어나 effect를 재실행하기 전에도 마찬가지로 구독을 해지함

`useState`와 마찬가지로 컴포넌트 내에서 여러 개의 effect를 사용할 수 있음

```jsx
function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...
}
```

Hook을 사용하면 구독을 추가하고 제거하는 로직과 같이 서로 관련 있는 코드들을 한구데에 모아서 작성 가능

반면 class 컴포넌트에서는 생명주기 메서드 가각에 쪼개서 넣어야했음

## Hook 사용 규칙

1. **최상위**에서만 Hook을 호출해야 함(반복문, 조건문, 중첩된 함수 내에서 Hook 실행 X)

2. **React 함수** 내에서만 Hook을 호출해야 함(일반 JS 함수 내에서 Hook 호출 X)

[출처-React 공식문서](https://ko.reactjs.org/docs/hooks-overview.html)

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)