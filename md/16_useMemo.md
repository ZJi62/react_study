# 16. useMemo 를 사용하여 연산한 값 재사용하기

성능 최적화를 위하여 연산된 값을 `useMemo`라는 Hook을 사용하여 재사용하는 방법

App 컴포넌트에서 다음과 같이 `countActiveUsers` 라는 함수를 만들어, `active` 값이 `true` 인 사용자의 수를 세어서 화면에 렌더링

### App.js

```jsx
import React, { useRef, useState } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

// active 값이 true인 사용자의 수를 세는 함수
function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');    // 함수가 호출될 때마다 우리가 알 수 있게 표시
  return users.filter(user => user.active).length;
}

function App() {
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });
  const { username, email } = inputs;
  const onChange = e => {
    const { name, value } = e.target;
    setInputs({
      ...inputs,
      [name]: value
    });
  };
  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);

  const nextId = useRef(4);
  const onCreate = () => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users.concat(user));

    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  };

  const onRemove = id => {
    // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
    // = user.id 가 id 인 것을 제거함
    setUsers(users.filter(user => user.id !== id));
  };
  const onToggle = id => {
    setUsers(
      users.map(user =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  };
  const count = countActiveUsers(users);
  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;
```

`countActiveUsers` 함수에서 콘솔에 메시지를 출력하도록 한 이유는 이 함수가 호출될 때마다 알 수 있게 하기 위함

![countActiveUsers](https://i.imgur.com/VdBCPKo.png)

다른 계정명을 눌러 초록색으로 만들면 활성 사용사 수 또한 업데이트 될 것

![countActiveUsers2](https://i.imgur.com/NyyTCK3.png)

여기서 발생하는 한가지 성능적 문제

input 의 값을 바꿀 때에도 `countActiveUsers` 함수가 호출된다는 것

![countActiveUsers3](https://i.imgur.com/247MDqy.png)

활성 사용자 수를 세는 건, users에 변화가 있을 때만 세야하는건데, input 값이 바뀔 때에도 리렌더링 되어 자원이 낭비

이러한 상황에는 `useMemo` 라는 Hook 함수를 사용하면 성능 최적화 가능

Memo는 "momoized"를 의미하고, 이는 이전에 계산한 값을 재사용 한다는 의미를 가짐

### App.js

```jsx
import React, { useRef, useState, useMemo } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

function App() {
  const [inputs, setInputs] = useState({
    username: '',
    email: ''
  });
  const { username, email } = inputs;
  const onChange = e => {
    const { name, value } = e.target;
    setInputs({
      ...inputs,
      [name]: value
    });
  };
  const [users, setUsers] = useState([
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com',
      active: true
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com',
      active: false
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com',
      active: false
    }
  ]);

  const nextId = useRef(4);
  const onCreate = () => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users.concat(user));

    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  };

  const onRemove = id => {
    // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
    // = user.id 가 id 인 것을 제거함
    setUsers(users.filter(user => user.id !== id));
  };
  const onToggle = id => {
    setUsers(
      users.map(user =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  };
  // useMemo 를 사용하여 countActiveUsers 함수의 결과값을 재사용
  const count = useMemo(() => countActiveUsers(users), [users]);
  return (
    <>
      <CreateUser
        username={username}
        email={email}
        onChange={onChange}
        onCreate={onCreate}
      />
      <UserList users={users} onRemove={onRemove} onToggle={onToggle} />
      <div>활성사용자 수 : {count}</div>
    </>
  );
}

export default App;

`useMemo` 의 첫 번째 파라미터에는 어떻게 연산할지 정의하는 함수를 넣어주고, 두 번째 파라미터에는 deps 배열을 넣어주면 됨

이 배열 안에 넣은 내용이 바뀌면, 등록한 함수를 호출해서 값을 연산해주고, 만약에 내용이 바뀌지 않았다면 이전에 연산한 값을 재사용하게 됨
```

![useMemo](https://i.imgur.com/LWGbRRm.png)

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)