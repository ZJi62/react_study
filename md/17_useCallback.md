# 17. useCallback 을 사용하여 함수 재사용하기

`useCallback` 은 `useMemo`와 비슷한 Hook

`useMemo` 는 **특정 결과값 재사용** 때 사용하는 반면, `useCallback` 은 특정 함수를 **새로 만들지 않고 재사용** 하고 싶을 때 사용

### App.js

```jsx
// onCreate, onRemove, onToggle 함수를 확인해보자

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
```

이 함수들은 컴포넌트가 리렌더링 될 때마다 새로 만들어짐

한번 만든 함수를 필요할 때만 새로 만들고 재사용하는 것은 중요한데, 나중에 컴포넌트에서 `props`가 바뀌지 않았으면 Virtual DOM 에 새로 렌더링하는 것조차 하지 않고 컴포넌트의 결과물을 재사용하는 최적화 작업을 할 때 함수를 재사용하는 것이 필수

`useCallback` 은 하단과 같은 방식으로 사용함

### App.js

```jsx
import React, { useRef, useState, useMemo, useCallback } from 'react';
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
  // useCallback 을 사용하여 함수를 재사용(1)
  const onChange = useCallback(
    e => {
      const { name, value } = e.target;
      setInputs({
        ...inputs,
        [name]: value
      });
    },
    [inputs]
  );
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
  // useCallback 을 사용하여 함수를 재사용(2)
  const onCreate = useCallback(() => {
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
  }, [users, username, email]);

  // useCallback 을 사용하여 함수를 재사용(3)
  const onRemove = useCallback(
    id => {
      // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
      // = user.id 가 id 인 것을 제거함
      setUsers(users.filter(user => user.id !== id));
    },
    [users]
  );
  // useCallback 을 사용하여 함수를 재사용(4)
  const onToggle = useCallback(
    id => {
      setUsers(
        users.map(user =>
          user.id === id ? { ...user, active: !user.active } : user
        )
      );
    },
    [users]
  );
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
```

함수 안에서 사용하는 상태 혹은 props가 있다면 꼭 `deps` 배열 안에 포함시켜야 된다는 것

`deps` 배열 안에 함수에서 사용하는 값을 넣지 않게 된다면, 함수 내에서 해당 값들을 참조할 때 가장 최신 값을 참조 할 것이라고 보장 X

props 로 받아온 함수가 있다면 이 또한 `deps` 에 넣어주어야 함

`useCallback`을 사용함으로써, 바로 이뤄낼 수 있는 눈에 띄는 최적화는 없음

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)