# 18. React.memo 를 사용한 컴포넌트 리렌더링 방지

컴포넌트의 props 가 바뀌지 않았다면, 리렌더링을 방지하여 컴포넌트의 리렌더링 성능 최적화를 해줄 수 있음

컴포넌트에서 리렌더링이 필요한 상황에서만 리렌더링을 하도록 설정 가능

감싸주기만 하면 끝

### CreateUser.js

```jsx
import React from 'react';

const CreateUser = ({ username, email, onChange, onCreate }) => {
  return (
    <div>
      <input
        name="username"
        placeholder="계정명"
        onChange={onChange}
        value={username}
      />
      <input
        name="email"
        placeholder="이메일"
        onChange={onChange}
        value={email}
      />
      <button onClick={onCreate}>등록</button>
    </div>
  );
};

// React.memo 를 사용하여 컴포넌트 리렌더링 방지
export default React.memo(CreateUser);
```

### UserList.js

```jsx
import React from 'react';

const User = React.memo(function User({ user, onRemove, onToggle }) {
  return (
    <div>
      <b
        style={{
          cursor: 'pointer',
          color: user.active ? 'green' : 'black'
        }}
        onClick={() => onToggle(user.id)}
      >
        {user.username}
      </b>
      &nbsp;
      <span>({user.email})</span>
      <button onClick={() => onRemove(user.id)}>삭제</button>
    </div>
  );
});

function UserList({ users, onRemove, onToggle }) {
  return (
    <div>
      {users.map(user => (
        <User
          user={user}
          key={user.id}
          onRemove={onRemove}
          onToggle={onToggle}
        />
      ))}
    </div>
  );
}

// React.memo 를 사용하여 컴포넌트 리렌더링 방지
export default React.memo(UserList);
```

input을 수정할 때 하단의 UserList 가 리렌더링 되지 않는 것을 확인

![react_memo](https://i.imgur.com/3OAEO1o.gif)

User 중 하나라도 수정하면 모든 User 들이 리렌더링되고, CreateUser 도 리렌더링 됨

![react_memo2](https://i.imgur.com/vAUbNRk.gif)

users 배열이 바뀔 때마다 onCreate 도 새로 만들어지고, onToggle, onRemove 도 새로 만들어지기 때문임


```jsx
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

const onRemove = useCallback(
  id => {
    // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
    // = user.id 가 id 인 것을 제거함
    setUsers(users.filter(user => user.id !== id));
  },
  [users]
);
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
```

`deps` 에 `users` 가 들어있기 때문에 배열이 바뀔 때마다 함수가 새로 만들어지는 것

deps 에서 `users`를 지우고, 함수들에서 현재 `useState`로 관리하는 `users`를 참조하지 않게 하면 됨 -> 함수형 업데이트

함수형 업데이트를 하게 되면, `setUsers`에 등록하는 콜백 함수의 파라미터에서 최신 `users`를 참조할 수 있기 때문에 `deps`에 `users`를 넣지 않아도 됨

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
  const onChange = useCallback(e => {
    const { name, value } = e.target;
    setInputs(inputs => ({
      ...inputs,
      [name]: value
    }));
  }, []);
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
  const onCreate = useCallback(() => {
    const user = {
      id: nextId.current,
      username,
      email
    };
    setUsers(users => users.concat(user));

    setInputs({
      username: '',
      email: ''
    });
    nextId.current += 1;
  }, [username, email]);

  const onRemove = useCallback(id => {
    // user.id 가 파라미터로 일치하지 않는 원소만 추출해서 새로운 배열을 만듬
    // = user.id 가 id 인 것을 제거함
    setUsers(users => users.filter(user => user.id !== id));
  }, []);
  const onToggle = useCallback(id => {
    setUsers(users =>
      users.map(user =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  }, []);
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

이렇게 하면, 특정 항목을 수정하게 될 때, 해당 항목만 리렌더링 될 것

> 리액트 개발 시, `useCallback`, `useMemo`, `React.memo` 는 컴포넌트의 성능을 실제로 개선할 수 있는 상황에서만 하기

>> 예시로, User 컴포넌트에 `b` 와 `button` 에 `onClick`으로 설정해준 함수들은, 해당 함수들을 `useCallback` 으로 재사용한다고 해서 리렌더링을 막을 수 있는 것은 아니므로, 굳이 그렇게 할 필요 X

> 렌더링 최적화 하지 않을 컴포넌트에 React.memo 를 사용하는 것은, 불필요한 props 비교만 하는 것이기 때문에 실제로 렌더링을 방지할 수 있는 상황이 있는 경우에만 사용하기