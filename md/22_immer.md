# 22. Immer 를 사용한 더 쉬운 불변성 관리

## Immer 사용법

코드 상단에 immer 를 불러와서 immer 를 불러오기

```jsx
import produce from 'immer';
```

`produce` 함수를 사용 할 때에는 첫 번째 파라미터는 수정하고 싶은 상태, 두 번째 파라미터에는 어떻게 업데이트하고 싶을지 정의하는 함수를 넣어줌

```jsx
const state = {
  number: 1,
  dontChangeMe: 2
};

const nextState = produce(state, draft => {
  draft.number += 1;
});

console.log(nextState);
// { number: 2, dontChangeMe: 2 }
```

## 리듀서에서 Immer 사용하기

### App.js

```jsx
import React, { useReducer, useMemo } from 'react';
import UserList from './UserList';
import CreateUser from './CreateUser';
import produce from 'immer';

function countActiveUsers(users) {
  console.log('활성 사용자 수를 세는중...');
  return users.filter(user => user.active).length;
}

const initialState = {
  users: [
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
  ]
};

function reducer(state, action) {
  switch (action.type) {
    case 'CREATE_USER':
      return produce(state, draft => {
        draft.users.push(action.user);
      });
    case 'TOGGLE_USER':
      return produce(state, draft => {
        const user = draft.users.find(user => user.id === action.id);
        user.active = !user.active;
      });
    case 'REMOVE_USER':
      return produce(state, draft => {
        const index = draft.users.findIndex(user => user.id === action.id);
        draft.users.splice(index, 1);
      });
    default:
      return state;
  }
}

// UserDispatch 라는 이름으로 내보내줍니다.
export const UserDispatch = React.createContext(null);

function App() {
  const [state, dispatch] = useReducer(reducer, initialState);

  const { users } = state;

  const count = useMemo(() => countActiveUsers(users), [users]);
  return (
    <UserDispatch.Provider value={dispatch}>
      <CreateUser />
      <UserList users={users} />
      <div>활성사용자 수 : {count}</div>
    </UserDispatch.Provider>
  );
}

export default App;
```

## Immer 와 함수형 업데이트

만약 `produce` 함수에 두개의 파라미터를 넣게 된다면, 첫 번째 파라미터에 넣은 상태를 불변성을 유지하면서 새로운 상태를 만들어주지만, 만약 첫 번째 파라미터를 생략하고 바로 업데이트 함수를 넣어주게 된다면, 반환 값은 새로운 상태가 아닌 상태를 업데이트 해주는 함수가 됨

```jsx
const todo = {
  text: 'Hello',
  done: false
};

const updater = produce(draft => {
  draft.done = !draft.done;
});

const nextTodo = updater(todo);

console.log(nextTodo);
// { text: 'Hello', done: true }
```

`produce` 가 반환하는 것이 업데이트 함수가 되기 때문에 `useState` 의 함수를 사용할 때 다음과 같이 구현 가능

```jsx
const [todo, setTodo] = useState({
  text: 'Hello',
  done: false
});

const onClick = useCallback(() => {
  setTodo(
    produce(draft => {
      draft.done = !draft.done;
    })
  );
}, []);
```

Immer 라이브러리는 데이터 구조가 복잡해져서 불변성을 유지하면서 업데이트하려면 코드가 복잡해지는 상황이 온다면, 이를 사용하는 것을 권장

[출처-벨로퍼트와 함께하는 모던 리액트](https://react.vlpt.us/)