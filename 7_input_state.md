# 7. input 상태 관리하기

리액트에서 사용자가 입력할 수 있는 input 태그의 상태를 관리하는 방법

### InputSample.js

```jsx
import React from 'react';

function InputSample() {
  return (
    <div>
      <input />
      <button>초기화</button>
      <div>
        <b>값: </b>
      </div>
    </div>
  );
}

export default InputSample;
```

그 다음 이 컴포넌트를 App에서 렌더링

### App.js

```jsx
import React from 'react';
import InputSample from './InputSample';

function App() {
  return (
    <InputSample />
  );
}

export default App;
```

![inputSample](https://i.imgur.com/tsDzOFM.png)

input에 입력하는 값이 하단에 나타나게 하고, 초기화 버튼을 누르면 input 값이 비워지도록 구현

이번에도 `useState`를 사용 + input의 `onChange`라는 이벤트를 사용

이벤트에 등록하는 함수에서는 이벤트 객체 `e`를 파라미터로 받아와 사용할 수 있는데 이 객체의 `e.target`은 이벤트가 발생한 DOM인 input DOM을 가리킴

이 DOM의 `value`값, 즉 `e.target.value`를 조회하면 현재 input에 입력한 값이 무엇인지 알 수 있음 -> 이 값을 `useState`를 통해 관리하면 됨

### InputSample.js

```jsx
import React, { useState } from 'react';

function InputSample() {
  const [text, setText] = useState('');

  const onChange = (e) => {
    setText(e.target.value);
  };

  const onReset = () => {
    setText('');
  };

  return (
    <div>
      <input onChange={onChange} value={text}  />
      <button onClick={onReset}>초기화</button>
      <div>
        <b>값: {text}</b>
      </div>
    </div>
  );
}

export default InputSample;
```

input의 상태를 관리할 때에는 input 태그의 `value`값을 설정해주는 것이 중요 -> 상태가 바뀌었을 때 input의 내용도 업데이트 됨