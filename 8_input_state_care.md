# 8. 여러 개의 input 상태 관리하기

input이 여러 개일 때의 관리법

이번에는 input이 비어져 있을 때, input에 대한 설명을 보여주는 `placeholder` 값도 설정할 것

기존에 만들었던 상태는 지우고 `onChange`와 `onReset` 함수는 비워두기

### InputSample.js

```jsx
import React, { useState } from 'react';

function InputSample() {
  const onChange = (e) => {
  };

  const onReset = () => {
  };


  return (
    <div>
      <input placeholder="이름" />
      <input placeholder="닉네임" />
      <button onClick={onReset}>초기화</button>
      <div>
        <b>값: </b>
        이름 (닉네임)
      </div>
    </div>
  );
}

export default InputSample;
```

![inputSample](https://i.imgur.com/otlM7Jc.png)

input의 개수가 여러 개가 됐을 때는 단순히 `useState`를 여러번 사용하고 `onChange`도 여러 개 만들어 구현할 수 있음

더 좋은 방법으로 input에 `name`을 설정하고 이벤트가 발생했을 때 이 값을 참조하는 것

그리고 `useState`에서는 문자열이 아니라 객체 형태의 상태를 관리해주어야 함

### InputSample.js

```jsx
import React, { useState } from 'react';

function InputSample() {
  const [inputs, setInputs] = useState({
    name: '',
    nickname: ''
  });
  const { name, nickname } = inputs; // 비구조화 할당을 통해 값 추출

  const onChange = (e) => {
    const { value, name } = e.target; // e.target 에서 name과 value 추출
    setInputs({
      ...inputs, // 기존의 input 객체를 복사한 뒤
      [name]: value // name 키를 가진 값을 value로 설정
    });
  };

  const onReset = () => {
    setInputs({
      name: '',
      nickname: ''
    });
  };

  return (
    <div>
      <input name="name" placeholder="이름" onChange={onChange} value={name} />
      <input name="nickname" placeholder="닉네임" onChange={onChange} value={nickname}/>
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

리액트 상태에서 객체를 수정해야 할 때에는,

```jsx
inputs[name] = value;
```

이런 식으로 직접 수정 X

새로운 객체를 만들어서 새로운 객체에 변화를 주고, 이를 상태로 사용해주어야 함

```jsx
setInputs({
  ...inputs,
  [name]: value
});
```

여기서 사용한 `...` 문법은 spread 문법 -> 객체의 내용을 모두 펼쳐서 기존 객체를 복사해줌(불변성을 지키는 작업)

불변성을 지켜주어야만 리액트 컴포넌트에서 상태가 업데이트가 됐음을 감지할 수 있고, 이에 따라 필요한 리렌더링이 진행 + 컴포넌트 업데이트 성능 최적화가 제대로 됨

만약 `inputs[name] = value` 와 같은 식으로 기존 상태를 직접 수정하게 되면, 값을 바꿔도 리렌더링이 되지 않음

> 리액트에서 객체를 업데이트 할 때는 기존 객체를 직접 수정 X, 새로운 객체를 만들어 새 객체에 변화를 주어야 함