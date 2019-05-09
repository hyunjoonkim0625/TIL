# Next.js

자료의 출처는 [공식 홈페이지](https://nextjs.org/learn/basics/getting-started)입니다.

## Next.js란?

Next.js는 리액트 어플리케이션들을 위해 서버사이드 렌더링을 가능하게 해주는 프레임워크이다.

## Next.js의 기능들로는:

- 서버 사이드 렌더링
- 더 빠른 페이지 로드를 위한 코드 스플릿을 자동으로
- 페이지 기반의 간단한 클라이언트 측 라우팅
- Hot Module Replacement(HMR)을 지원하는 웹팩 기반의 개발 환경
- Express 또는 어떠한 Node.js HTTP 서버들에 추가가 가능
- 직접 설정한 바벨과 웹팩 config를 이용한 커스터마이징

등이 있다.

---

## 관련 라이브러리

- isomorphic-unfetch
  - 브라우저에서 API를 간단하게 fetch하게 도와주는 라이브러리로, 클라이언트와 서버 환경 모두에서 작동한다.

---

## 알아가는 과정

### context 매개변수

```js
Post.getInitialProps = async function(context) {
  const { id } = context.query;
  const res = await fetch(`https://api.tvmaze.com/shows/${id}`);
  const show = await res.json();

  console.log(`Fetched show: ${show.name}`);

  return { show };
};
```

위의 코드에서 context 매개변수는 우리가 fetch한 정보를 사용할 수 있도록 해주는 쿼리 필드를 가지고 있다.

### Fetch Data in Client Side

- Next.js 공식 홈페이지 튜토리얼 중

서버 사이드 렌더링을 하고 있을지라도 `<Link>` 컴포넌트로 접근한 페이지의 경우, 페이지 전환이 브라우저에서 발생한다. 그렇기 때문에 `<Link>` 컴포넌트 내에 `console.log`등을 정의하고 확인을 하면, 이는 브라우저의 콘솔에서 표시가 된다. 하지만, 만약 그 페이지를 직접 주소줄에 입력하여 접근하게 되면 그 `console.log`는 서버의 콘솔에서 표시가 된다.

### 컴포넌트 스타일링

CSS 파일(SASS 등)을 바탕으로 한 스타일링은 서버 사이드 렌더링에서 실무적인 문제들이 발생할 수가 있다. 그렇기 때문에 Next.js를 같이 사용한다고 하면, 자바스크립트 파일 내에 CSS를 작성하는 기법을 사용하는 것이 추천된다. Next.js는 `[styled-jsx](https://github.com/zeit/styled-jsx)`라는 프레임워크를 사용하여 CSS 작성을 수월하게 해준다. 이렇게 작성된 CSS들은 모두 스코프화 된다.
