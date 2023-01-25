15. useEffect를 사용하여 마운트/언마운트/업데이트시 할 작업 설정하기

useEffect라는 Hook을 사용하여

1. 컴포넌트가 마운트 됐을 때 (처음 나타났을 때)

2. 언마운트 됐을 때 (사라질 때)

3. 업데이트 될 때 (특정 props가 바뀔 때)

특정 작업을 처리하는 방법

> 컴포넌트(재사용이 가능한 각각의 독립된 모듈)가 실제 DOM에 삽입되는 것을 **마운트**, 컴포넌트가 변하는 것을 **업데이트**, 컴포넌트가 DOM 상에서 제거되는 것을 **언마운트**

## 마운트 / 언마운트

### UserList.js

```jsx
import React, { useEffect } from 'react';

function User({ user, onRemove, onToggle }) {
  // useEffect 사용
  useEffect(() => {
    console.log('컴포넌트가 화면에 나타남');
    return () => {
      console.log('컴포넌트가 화면에서 사라짐');
    };
  }, []);
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
}

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

export default UserList;
```

`useEffect` 를 사용할 때에는 첫 번째 파라미터에는 **함수**, 두 번째 파라미터에는 **의존값이 들어있는 배열(`deps`)**을 넣음

`deps` 배열을 비우게 된다면, 컴포넌트가 처음 나타날 때만 `useEffect` 에 등록한 함수가 호출됨

`useEffect`에서는 함수를 반환 가능 = `cleanup` 함수

`cleanup` 함수는 컴포넌트가 사라질 때 호출됨(`deps`가 비어있을 때)

![componenet_mount](https://i.imgur.com/kBFMe8m.png)

**마운트** 시에 하는 작업들

- `props` 로 받은 값을 컴포넌트의 로컬 상태로 설정

- 외부 API 요청 (REST API 등)

- 라이브러리 사용 (D3, Video.js 등)

- setInterval 을 통한 반복작업 혹은 setTimeout 을 통한 작업 예약

**언마운트** 시에 하는 작업들

- setInterval, setTimeout 을 사용하여 등록한 작업들 clear 하기 (clearInterval, clearTimeout)

- 라이브러리 인스턴스 제거

## deps 에 특정 값 넣기

deps 에 특정 값을 넣게 된다면, **컴포넌트가 처음 마운트 될 때에도 호출**되고, **지정한 값이 바뀔 때에도 호출됨**

그리고 deps 안에 특정 값이 있다면 언마운트 시에도 호출되고, 값이 바뀌기 직전에도 호출됨

### UserList.js

```jsx
import React, { useEffect } from 'react';

function User({ user, onRemove, onToggle }) {
  // useEffect 안의 내용을 수정
  useEffect(() => {
    console.log('user 값이 설정됨');
    console.log(user);
    return () => {
      console.log('user 가 바뀌기 전..');
      console.log(user);
    };
  }, [user]);   // deps 에 user 를 넣어줌
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
}

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

export default UserList;
```

![userlist_edit](https://i.imgur.com/d2oJt9L.png)

`useEffect` 안에서 사용하는 상태 or props 가 있다면, `useEffect` 의 `deps`에 넣어주어야 함

> `useEffect` 안에서 사용하는 상태 or props 를 `deps` 에 넣지 않게 된다면 `useEffect` 에 등록한 함수가 실행될 때 최신 props / 상태를 가르키지 않게 됨

## deps 파라미터를 생략하기

`deps` 파라미터를 생략하면, **컴포넌트가 리렌더링 될 때마다 호출됨**

### UserList.js

```jsx
import React, { useEffect } from 'react';

function User({ user, onRemove, onToggle }) {
  useEffect(() => {
    console.log(user);
  });   // deps 를 생략
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
}

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

export default UserList;
```

![nondeps](https://i.imgur.com/mh4bqfl.png)

> 리액트 컴포넌트는 기본적으로 부모 컴포넌트가 리렌더링되면 자식 컴포넌트 또한 리렌더링 됨 -> 실제 DOM 에 변화가 반영되는 것은 바뀐 내용이 있는 컴포넌트에만 해당, 하지만 Virtual DOM 에는 모든 걸 다 렌더링함

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)