# 24. LifeCycle Method

생명주기 메서드는 컴포넌트가 브라우저 상에 나타나고, 업데이트 되고, 사라지게 될 때 호출되는 메서드들

생명주기 메서드는 클래스형 컴포넌트에서만 사용 가능

3가지 버튼이 있는 프로젝트

- "랜덤 색상" 버튼을 누르면 숫자의 색상이 바뀜

- "토글" 버튼을 누르면 컴포넌트가 사라지거나 나타남

- "더하기" 버튼을 누르면 숫자가 1씩 더해짐

변화가 발생할 때마다 생명주기 메서드들을 호출

![image](https://i.imgur.com/cNfpEph.png)

## 마운드

마운트 될 때 발생하는 생명주기들을 알아봄

- constructor

- getDerivedStateFromProps

- render

- componentDidMount

### constructor

컴포넌트가 만들어지면 가장 먼저 실행되는 메서드

```jsx
  constructor(props) {
    super(props);
    console.log("constructor");
  }
```

### getDerivedStateFromProps

`props` 로 받아 온 값을 `state` 에 동기화 하는 작업을 해야 할 때 사용

```jsx
  static getDerivedStateFromProps(nextProps, prevState) {
    if (nextProps.color !== prevState.color) {
      return { color: nextProps.color };
    }
    return null;
  }
```

다른 생명주기 메서드와는 달리 앞에 `static` 을 필요로 하고, 이 안에서는 `this` 를 조회할 수 없음

특정 객체를 반환하게 되면 해당 객체 안에 있는 내용들이 컴포넌트의 `state` 로 설정 가능

### render

컴포넌트를 렌더링하는 메서드

### componentDidMount

컴포넌트의 첫 번째 런데링이 마치고 나면 호출되는 메서드

## 업데이트

컴포넌트가 업데이트 되는 시점에 어떤 생명주기 메서드들이 호출되는지 알아보기

- getDerivedStateFromProps

- shouldComponentUpdate

- render

- getSnapshotBeforeUpdate

- componentDidUpdate

## getDerivedStateFromProps

컴포넌트의 props 나 state 가 바뀌었을 때도 이 메서드가 호출

## shouldComponentUpdate

컴포넌트가 리렌더링을 해야 할지 말지를 결정하는 메서드

```jsx
    shouldComponentUpdate(nextProps, nextState) {
        return this.state.number !== nextState.number;
    }
```

최적화 할 때 사용하는 메서드

## render

## getSnapshotBeforeUpdate

`getSnapshotBeforeUpdate` 는 컴포넌트에 변화가 일어나기 직전의 DOM 상태를 가져와서 특정 값을 반환하면 그 다음 발생하게 되는 `componentDidUpdate` 함수에서 받아와서 사용을 할 수 있음

```jsx
  getSnapshotBeforeUpdate(prevProps, prevState) {
    if (prevProps.color !== this.props.color) {
      return this.myRef.style.color;
    }
    return null;
  }
```

## componentDidUpdate

`componentDidUpdate` 는 리렌더링이 마치고, 화면에 우리가 원하는 변화가 모두 반영되고 난 뒤 호출되는 메서드

3번째 파라미터로 `getSnapshotBeforeUpdate` 에서 반환한 값을 받아올 수 있음

```jsx
  componentDidUpdate(prevProps, prevState, snapshot) {
    if (snapshot) {
      console.log("업데이트 되기 직전 색상: ", snapshot);
    }
  }
```

## 언마운트

컴포넌트가 화면에서 사라지는 것

언마운트에 관련된 생명주기 메서드는 `componentWillUnmount` 하나

## componentWillUnmount

`componentWillUnmount` 는 컴포넌트가 화면에서 사라지기 직전에 호출됨

```jsx
  componentWillUnmount() {
    console.log("componentWillUnmount");
  }
```

DOM에 직접 등록했었던 이벤트를 제거하고, 만약에 `setTimeout` 을 걸은 것이 있다면 `clearTimeout` 을 통하여 제거 가능

추가적으로, 외부 라이브러리를 사용한게 있고 해당 라이브러리에 dispose 기능이 있다면 여기서 호출해주면 됨