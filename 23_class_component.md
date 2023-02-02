# 23. 클래스형 컴포넌트

클래스형 컴포넌트에서는 `render()` 메서드가 있어야 함

이 메서드에서 렌더링하고 싶은 JSX를 반환하시면 됨

`props` 를 조회 해야할 때에는 `this.props` 를 조회

`defaultProps` 를 설정하는 것은 이전 함수형 컴포넌트에서 했을 때와 똑같이 해도 되며, 클래스 내부에 `static` 키워드와 함께 선언 가능

### Hello.js

```jsx
import React, { Component } from 'react';

class Hello extends Component {
  static defaultProps = {
    name: '이름없음'
  };
  render() {
    const { color, name, isSpecial } = this.props;
    return (
      <div style={{ color }}>
        {isSpecial && <b>*</b>}
        안녕하세요 {name}
      </div>
    );
  }
}

export default Hello;
```

### Counter.js

```jsx
import React, { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}

function Counter() {
  const [number, dispatch] = useReducer(reducer, 0);

  const onIncrease = () => {
    dispatch({ type: 'INCREMENT' });
  };

  const onDecrease = () => {
    dispatch({ type: 'DECREMENT' });
  };

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

클래스형 컴포넌트로 작성

```jsx
import React, { Component } from 'react';

class Counter extends Component {
  render() {
    return (
      <div>
        <h1>0</h1>
        <button>+1</button>
        <button>-1</button>
      </div>
    );
  }
}

export default Counter;
```

## + 커스텀 메서드 만들기, 상태 선언하기, 상태 업데이트하기, setState 의 함수형 업데이트

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)