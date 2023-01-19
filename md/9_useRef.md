# 9. useRef 로 특정 DOM 선택하기

JS를 사용할 때, 특정 DOM을 선택해야 하는 상황에 `getElementById`나 `querySelector`를 사용하여 DOM을 선택

리액트에서는 `ref` 라는 것을 사용

함수형 컴포넌트에서 `ref` 를 사용 할 때에는 `useRef` 라는 Hook 함수를 사용

클래스형 컴포넌트에서는 콜백 함수를 사용하거나 `React.createRef` 라는 함수를 사용(후에 다룸, 클래스 컴포넌트는 이제 별로 중요하지 않기 때문)

앞서 만든 예제에 초기화 버튼을 클릭했을 때, 이름 input에 포커스가 잡히도록 `useRef`를 사용하여 기능을 구현

### InputSample.js

```jsx
import React, { useState, useRef } from 'react';

function InputSample() {
  const [inputs, setInputs] = useState({
    name: '',
    nickname: ''
  });
  const nameInput = useRef();

  const { name, nickname } = inputs; // 비구조화 할당을 통해 값 추출

  const onChange = e => {
    const { value, name } = e.target; // 우선 e.target 에서 name 과 value 를 추출
    setInputs({
      ...inputs, // 기존의 input 객체를 복사한 뒤
      [name]: value // name 키를 가진 값을 value 로 설정
    });
  };

  const onReset = () => {
    setInputs({
      name: '',
      nickname: ''
    });
    nameInput.current.focus();
  };

  return (
    <div>
      <input
        name="name"
        placeholder="이름"
        onChange={onChange}
        value={name}
        ref={nameInput}
      />
      <input
        name="nickname"
        placeholder="닉네임"
        onChange={onChange}
        value={nickname}
      />
      <button onClick={onReset}>초기화</button>
      <div>
        <b>값: </b>
        {name} ({nickname})
      </div>
    </div>
  );
}

export default InputSample;
```

`useRef()` 를 사용하여 `ref` 객체를 만들고, 이 객체를 우리가 선택하고 싶은 DOM에 `ref` 값으로 설정해주어야 함 -> ref 객체의 `.current` 값은 우리가 원하는 DOM을 가르키게 됨

위 예제에서는 `onReset` 함수에서 input 에 포커스를 하는 `focus()` DOM API를 호출

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)