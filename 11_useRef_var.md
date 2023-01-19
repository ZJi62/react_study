# 11. useRef 로 컴포넌트 안의 변수 만들기

`useRef` Hook 는 DOM 선택 말고도 다른 용도가 있음 -> 컴포넌트 안에서 조회 및 수정할 수 있는 변수를 관리하는 것

`useRef`로 관리하는 변수는 값이 바뀐다고 해서 컴포넌트가 리렌더링되지 않음

리액트 컴포넌트에서의 상태는 상태를 바꾸는 함수를 호출하고 난 다음 렌더링 이후로 업데이트 된 상태를 조회할 수 있는 반면, `useRef`로 관리하고 있는 변수는 설정 후 바로 조회 가능

이 변수를 사용하여 다음과 같은 값을 관리 가능

- `setTimeout`, `setInterval` 을 통해 만들어진 `id`

- 외부 라이브러리를 사용하여 생성된 인스턴스

- scroll 위치

App 컴포넌트에서 `useRef`를 사용하여 변수를 관리해보자

용도는 앞으로 배열에 새 항목을 추가할건데, 새 항목에서 사용할 고유 id를 관리하는 용도

`useRef` 를 사용해 변수를 관리하기 전 해야할 작업이 있음

UserList에 선언해서 사용하는 대신, 이 배열을 App에서 선언하고 UserList에게 props로 전달할 것

### App.js

```jsx
import React from 'react';

import UserList from './UserList';

function App() {
  const users = [
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com'
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com'
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com'
    }
  ];
  return <UserList users={users} />;    // users 배열을 props로 전달
}

export default App;
```

### UserList.js

```jsx
import React from 'react';

function User({ user }) {
  return (
    <div>
      <b>{user.username}</b> <span>({user.email})</span>
    </div>
  );
}

function UserList({ users }) {
  return (
    <div>
      {users.map(user => (
        <User user={user} key={user.id} />
      ))}
    </div>
  );
}

export default UserList;
```

이제 App에서 `useRef()`를 사용해 nextId 라는 변수를 만들어보자

### App.js

```jsx
import React, { useRef } from 'react';
import UserList from './UserList';

function App() {
  const users = [
    {
      id: 1,
      username: 'velopert',
      email: 'public.velopert@gmail.com'
    },
    {
      id: 2,
      username: 'tester',
      email: 'tester@example.com'
    },
    {
      id: 3,
      username: 'liz',
      email: 'liz@example.com'
    }
  ];

  const nextId = useRef(4);
  const onCreate = () => {
    // 나중에 구현 할 배열에 항목 추가하는 로직
    // ...

    nextId.current += 1;
  };
  return <UserList users={users} />;
}

export default App;
```

`useRef()` 를 사용할 때 파라미터를 넣어주면, 이 값이 `.current` 값의 기본값이 됨

그리고 이 값을 수정할 때에는 `.current` 값을 수정하면 되고 조회할 때에는 `.current`를 조회하면 됨

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)