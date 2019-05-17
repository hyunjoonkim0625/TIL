# MobX 알아가기

Velopert님의 MobX 튜토리얼을 따라하는 과정입니다. 참고자료는 [여기!](https://velog.io/@velopert/MobX-1-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-9sjltans3p)

---

## MobX의 주요 개념들

MobX의 주요 개념들로는 4가지가 있다.

1. Observable State

MobX를 사용하고 있는 앱의 상태는 항상 관찰할 수 있는 상태이다. 이 뜻은, 앱에서 사용하고 있는 상태의 특정 부분에 변화가 일어난다면 MobX에서는 이를 알 수 있다. 이 값의 특성에 상관없이(원시적 값, 객체, 배열 내부의 객체 또는 객체의 키일지라도) 말이다.

2. Computed Value

연산된 값은 기존의 상태의 값과 다른 연산된 값에 기반하여 만들어질 수 있는 값이다. 이는 주로 성능의 최적화를 위하여 사용된다. 어떤 값을 연산해야 할 때, 연산에 기반이 되는 값이 바뀔 때만 새로 연산을 하게 하고 바뀌지 않았다면 그냥 기존의 값을 사용하게 한다.

3. Reactions

Reactions는 computed value와 비슷하다고 볼 수 있는데, computed value의 경우는 특정 값을 연산해야 될 때만 처리가 되는 반면, reactions는 값이 바뀜에 따라 해야 할 일을 정하는 것을 의미한다. 예를 들어, observable state의 내부의 값이 바뀔 때마다 `console.log`등을 사용하여 무엇이 바뀌는지를 확인할 수 있다.

4. Actions

Actions는 상태에 변화를 일으키는 것을 말한다. Observable state에 변화를 발생시키는 코드를 호출하는 것은 하나의 action이라고 보면 된다.

## MobX의 리액트 컴포넌트 최적화

1. 리스트를 렌더링 할 땐, 컴포넌트에 리스트 관련 데이터만 props로 넣자

예시:

```js
@observer
class MyComponent extends Component {
  render() {
    const { todos, user } = this.props;
    return (
      <div>
        {user.name}
        <ul>
          {todos.map(todo => (
            <TodoView todo={todo} key={todo.id} />
          ))}
        </ul>
      </div>
    );
  }
}
```

이 코드는 좋은 예가 아닌데, 그 이유는 `user.name`에 변경이 일어날 때도 컴포넌트가 다시 렌더링이 되기 때문이다. 이 뜻은, 변화가 자주 일어나는 요소와 그렇지 않은 요소에 대해 잘 이해하고 컴포넌트 구성을 해줘야 성능에도 좋은 영향을 끼칠 수 있다:

```js
@observer class MyComponent extends Component {
    render() {
        const {todos, user} = this.props;
        return (<div>
            {user.name}
            <TodosView todos={todos} />
        </div>)
    }
}

@observer class TodosView extends Component {
    render() {
        const {todos} = this.props;
        return <ul>
            {todos.map(todo => <TodoView todo={todo} key={todo.id} />)}
        </ul>)
    }
}
```

2. 세부참조는 최대한 늦게

세부참조(혹은 역참조)란, 특정 객체의 내부 값을 조회하는 것을 뜻한다. 예를 들어, 장바구니의 정보들을 보여줄 때 사용된 코드를 보면:

```js
const itemList = items.map(item => (
  <BasketItem
    name={item.name}
    price={item.price}
    count={item.count}
    key={item.name}
    onTake={onTake}
  />
));
```

여기서 item을 통해 name, price, count를 조회하는 것이 세부참조이다.

하지만 밑과 같은 방식으로 코드를 작성하면 업데이트 성능 최적화를 이뤄낼 수 있다:

```js
const itemList = items.map(item => (
  <BasketItem item={item} key={item.name} onTake={onTake} />
));
```

변동이 일어날 수 있는 count값의 세부참조를 `<BasketItem>` 컴포넌트 내부에서 하면, 더 높은 성능으로 컴포넌트를 업데이트 할 수 있다. 여기서 item.name의 값은 바뀌지 않기 때문에 key 설정 부분에서는 문제가 되지 않는다.

3. 함수는 미리 바인딩하고, 파라미터는 내부에서 넣어주기

컴포넌트에 함수를 전달할 때는 미리 바인딩 하는 것이 좋고, 파라미터가 유동적일 때는 파라미터를 넣는 작업을 컴포넌트 밖이 아닌 안에서 하는 것이 좋다.

이러한 코드는 좋은 예가 아니다:

```js
render() {
    return <MyWidget onClick={() => { alert('hi') }} />
}
```

그 대신에:

```js
render() {
    return <MyWidget onClick={this.handleClick} />
}

handleClick = () => {
    alert('hi')
}
```

이러한 코드도 **좋지 않다**:

```js
const ShopItemList = ({ onPut }) => {
  const itemList = items.map(item => (
    <ShopItem
      {...item}
      key={item.name}
      onPut={() => onPut(item.name, item.price)}
    />
  ));
  return <div>{itemList}</div>;
};
```

대신에 `onPut={onPut}`과 같이 전달하고 파라미터는 컴포넌트의 내부에서 넣어주는 것이 좋다:

```js
const ShopItemList = ({ onPut }) => {
  const itemList = items.map(item => (
    <ShopItem {...item} key={item.name} onPut={onPut} />
  ));
  return <div>{itemList}</div>;
};

const ShopItem = ({ name, price, onPut }) => {
  return (
    <div className="ShopItem" onClick={() => onPut(name, price)}>
      <h4>{name}</h4>
      <div>{price}원</div>
    </div>
  );
};
```
