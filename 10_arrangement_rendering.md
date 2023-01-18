# 10. 배열 렌더링하기

리액트에서 배열을 렌더링하는 방법

예시로 이러한 배열이 있다고 가정

```javascript
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
```

이 내용을 컴포넌트로 렌더링 할 때, 가장 기본적인 방법(비효율적)으로 그냥 그대로 코드를 작성하는 것

### UserList.js

```jsx
function UserList() {
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
  return (
    <div>
      <div>
        <b>{users[0].username}</b> <span>({users[0].email})</span>
      </div>
      <div>
        <b>{users[1].username}</b> <span>({users[1].email})</span>
      </div>
      <div>
        <b>{users[2].username}</b> <span>({users[1].email})</span>
      </div>
    </div>
  );
}

export default UserList;
```

하지만 재사용되는 코드를 일일히 넣어주는 것은 비효율적이므로, 컴포넌트를 재사용 할 수 있도록 새로 만들어 줄 것

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

function UserList() {
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

  return (
    <div>
      <User user={users[0]} />
      <User user={users[1]} />
      <User user={users[2]} />
    </div>
  );
}

export default UserList;
```

![userlist_normal](https://i.imgur.com/DtbthZm.png)

배열이 고정적이라면 상관없겠지만, 배열의 인덱스를 하나씩 조회해가며 렌더링하는 방법은 동적인 배열을 렌더링하지 못함

동적인 배열을 렌더링해야 할 때에는 JS 배열의 내장함수 `map()`을 사용

`map()` 함수는 배열 안에 있는 각 원소를 변환하여 새로운 배열을 만들어줌

리액트에서 동적인 배열을 렌더링해야 할 때는 이 함수를 사용하여 일반 데이터 배열을 리액트 엘리먼트로 이루어진 배열로 변환해주면 됨

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

function UserList() {
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

  return (
    <div>
      {users.map(user => (
        <User user={user} />
      ))}
    </div>
  );
}

export default UserList;
```

이렇게 하면 배열의 모든 원소가 렌더링 됨

하지만 브라우저에서 콘솔을 열어보면 다음과 같은 에러가 보일 것

![userlist_error](https://i.imgur.com/xbYuPhH.png)

리액트에서 배열을 렌더링 할 땐 `key` 라는 props 를 설정해야 함

`key` 값은 각 원소들마다 가지고 있는 고유값으로 설정해야 함

지금의 경우엔 `id`가 고유의 값

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

function UserList() {
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

만약 배열 안의 원소가 가지고 있는 고유한 값이 없다면 `map()` 함수를 사용할 때 설정하는 콜백함수의 두 번재 파라미터 `index` 를 `key` 로 사용하면 됨

```jsx
<div>
  {users.map((user, index) => (
    <User user={user} key={index} />
  ))}
</div>
```

만약 배열을 렌더링 할 때 `key` 설정을 하지 않게 된다면 기본적으로 배열의 `index` 값을 `key`로 사용하게 되고, 전에 봤던 경고 메시지가 뜨게 됨

이렇게 경고 메시지가 뜨는 이유는 각 고유 원소에 `key` 가 있어야만 배열이 업데이트 될 때 효율적으로 렌더링 될 수 있기 때문임

## key 의 존재 유무에 따른 업데이트 방식

예를 들어 다음과 같은 배열이 있다고 가정

```jsx
const array = ['a', 'b', 'c', 'd'];
```

그리고 위 배열을 다음과 같이 렌더링한다고 가정

```jsx
array.map(item => <div>{item}</div>);
```

위 배열의 b와 c 사이에 z를 삽입하게 된다면, 리렌더링을 하게 될 때 `<div>b</div>` 와 `<div>c</div>` 사이에 새 `div` 태그를 삽입하게 되는 것이 아닌 기존의 c가 z로 바뀌고, d는 c로, 맨 마지막에 d가 새로 삽입됨

그 다음 a 를 제거하게 된다면, 기존의 a가 b로, b는 z로, z는 c로, c는 d로 바뀌고, 맨 마지막에 있는 d가 제거됨

![key](https://i.imgur.com/3rkaiY1.gif)

하지만 `key`가 있다면 비효율적인 이 작업은 개선됨

객체에 다음과 같이 `key`로 사용할 수 있는 고유 값이 있고

```jsx
[
  {
    id: 0,
    text: 'a'
  },
  {
    id: 1,
    text: 'b'
  },
  {
    id: 2,
    text: 'c'
  },
  {
    id: 3,
    text: 'd'
  }
];
```

다음과 같이 렌더링 된다면

```jsx
array.map(item => <div key={item.id}>{item.text}</div>);
```

배열이 업데이트 될 때, `key` 가 없을 때처럼 비효율적으로 업데이트 하는 것이 아닌 수정되지 않는 기존의 값은 그대로 두고 원하는 곳에 내용을 삽입 또는 삭제함

![key2](https://i.imgur.com/yEUS6Bx.gif)

따라서 배열을 렌더링 할 때에는 고유한 `key` 값이 있는 것이 중요하며, 만약 배열 안에 중복되는 `key`가 있을 땐 렌더링 시에 오류메시지가 콘솔에 나타나게 되며, 업데이트가 제대로 이루어지지 않게 됨