밑에와 같은 핸들러가 있을 때, 이것을 어떠한 컴포넌트에 prop으로 넘겨준다고 하자.

```js
// 검색 필터링을 위한 핸들러
handleSearch = (value: string) => {
  return (event: any): void => {
    event.preventDefault();

    const defaultClubList = this.props.clubList;

    const lowerCasedValue = value.toLowerCase();

    const searchedList = defaultClubList.filter((item: any) =>
      item.clubName.toLowerCase().includes(lowerCasedValue)
    );

    const trimmedValue = value.trim();

    // 검색창에 아무것도 입력이 안되었을 시에는 초기 목록을 불러온다
    if (trimmedValue === "") {
      this.setState({
        currentlyShownList: defaultClubList
      });
    } else {
      this.setState({
        currentlyShownList: searchedList
      });
    }
  };
};
```

이 핸들러의 형태를 보면, `handleSearch = () => {return ()=> {}}` 이라고 볼 수 있다. 이를 prop으로 받는 곳에서 interface를 작성할 때는:

```js
interface SearchProps {
  handleSearch: (
    value: string
  ) => (event: React.FormEvent<HTMLFormElement>) => void;
}
```

역시 prop으로 받을 때도 `handlerSearch: () => () => 무언가` 의 형태로 작성을 해줘야...한다... 당연히!!

---

# 5월 8일

MeetingsView 컴포넌트에서 prop으로 받아온 clubList에 타입을 적용하던 중에:

```js
interface MeetingsViewProps {

  clubList: IClub[];
}

interface MeetingsViewState {

  currentlyShownList: IClub[];
  value: string;
  loading: boolean;
  items: number;
}

class MeetingsView extends React.Component<
  MeetingsViewProps,
  MeetingsViewState
> {
  state = {
    currentlyShownList: [],
    value: "",
    loading: true,
    items: 5
  };

  // ...
```

위와 같이 내부 상태에서 list의 초기값을 빈 배열로 설정해주었을 때, 나중의 코드에서 이를 사용해 map을 돌리려하면 `never[]` 판정(?)을 받게 된다.

이 경우를 해결하려면...

```js
state = {
    currentlyShownList: [] as IClub[],
    value: "",
    loading: true,
    items: 5
  };
```

`as` 구문을 사용하여 IClub의 타입값을 강제시켜버렸다.

다른 방법도 생각해봐야지.

## target vs. currentTarget

아래의 핸들러에서 `value`부분에 에러가 발생했는데:

```js
handleChange = (event: React.FormEvent<HTMLInputElement>): void => {
  this.setState({
    value: event.target.value
  });
};

// ... render() ...

<form className="Search" onSubmit={this.props.handleSearch(this.state.value)}>
  <input
    className="Search__input"
    value={this.state.value}
    onChange={this.handleChange}
    placeholder="클럽명 검색"
    type="search"
  />
  <input className="Search__submit" type="submit" value="검색" />
</form>;
```

에러메세지는 `Property 'value' does not exist on type 'EventTarget'.`이다. 즉, value가 target에 존재하지 않는다라고 볼 수 있다. `target`은 input의 value가 아닌 그것을 감싸고 있는 `form`에서 value값을 찾기 때문이라고 생각한다. 그래서:

```js
handleChange = (event: React.FormEvent<HTMLInputElement>): void => {
  this.setState({
    value: event.currentTarget.value
  });
};
```

으로 수정했더니 에러가 없어졌다. `currentTarget`은 핸들러가 할당된 그 요소에서 value를 찾을 것이기 때문에 내가 원하는 방식의 작동은 `currentTarget`을 사용하여 input 요소의 value 값을 찾는 것이다.

---

## 무한 스크롤 등, setState을 많이 불러올 수 있는 것들에 대한 처리

[정보의 출처: https://webclub.tistory.com/607](https://webclub.tistory.com/607)
