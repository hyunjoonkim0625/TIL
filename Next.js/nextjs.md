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

---

# Next.js 공부

Next.js 공부 노트 관련 요약입니다. 트레바리 개발자 양종훈님이 해주신 것을 참고했습니다.

## 동형 랜더링(Isomorphic Rendering)의 이해

- 기존 웹페이지의 전달 방식

  - Server Side Rendering(SSR): 각 페이지 별로 서버에서 렌더링 하여 페이지를 보여주는 방식

    - 첫 접속은 빠르나 전체적인 웹페이지 접속으 느리다(매번 새로운 페이지를 다시 내려받기 때문)

  - Client Side Rendering(CSR): 클라이언트 측에서 페이지를 변경해주고 필요한 데이터만 서버에서 받는 방식(Single Page Application이라고도 한다)

    - 사이트 첫 접속 시, 페이지 및 변경 가능 페이지의 렌더링 정보를 받아온다(구조가 커질수록 첫 로딩이 느리다).

    - SEO(Search Engine Optimization)이 어렵다 - SPA는 브라우저의 history API를 이용하여 주소조작을 통해 라우팅하기 때문에

* 동형 렌더링(Isomorphic Rendering)

동형 렌더링을 사용하여 Universal Web Application(UWA)을 구현할 수 있는데, UWA란 하나의 코드를 가지고 서버, 웹, 모바일 모두에서 구동이 되는 것을 말한다.

동형 렌더링은:

- 첫 접속 시, 서버로부터 필요한 페이지의 내용만 받는다
- 검색엔진에 검색되기 용이하다
- 페이지 이동이 빠르다
- 전체적으로 개별적인 상황에 맞춰 데이터를 수신하기 때문에 속도가 빠르다

1. Request: 브라우저가 서버에 요청을 할 때, DOM 요소나 페이지를 포함한 최대 가능한 만큼의 JS파일이나 CSS파일을 번들링하는 메인(초기) 요청이 들어가며, 유저가 이를 결정한다(번들링은 트리구조로 가능)

2. Process & Render: 서버는 요청을 처리하는데, HTML을 그리기 위해 유저가 요청한 경로를 검색(main page, section, item detail)하고 필수 데이터들을 검색한다.

3. Response: 서버는 이전에 완성한 HTML 페이지를 클라이언트에게 반환하고 클라이언트는 필요한 이미지, CSS 및 JS파일들이 들어있는 번들링이 된 문서를 로드한다.

4. Interaction: 이제 클라이언트는 렌더링에 필요로 하는 코드를 모두 가지고 있게 된다. 그렇기 때문에 초기 페이지는 서버로부터 렌더링이 되고, 이후의 모든 요청은 비동기적으로 웹페이지의 새로고침 없이 렌더링 할 수 있게 되어 유저 경험에 긍정적 효과를 준다.

## Next.js

약간의 정형화된 구조를 따르면 서버사이드 렌더링과 코드 스플리팅을 만족시킬 수 있다.

- 기본 특징

  - `./pages/*.js(.jsx)` 파일에 따라 브라우저 라우팅 경로를 결정(해당 경로로 SSR 요청)하고 코드 스플리팅을 자동 지원한다.

  - `./static`은 정적 자료의 제공을 위해서 사용되는 경로이다.

  - Hot code reloading(코드가 수정되어도 상태가 사라지지 않음) 기능을 지원한다.

  - import에 따른 트리구조로 번들링하고 client로 전달한다. 즉, 페이지에서 필요로 하지 않는 코드는 절대 로드가 되지 않는다.

  - 페이지 라우팅이 되는 컴포넌트에 static 메소드인 `getInitialProps`를 제공한다(\_app과 기본 컴포넌트들의 `getInitialProps`는 조금 상이하다).

- Webpack 및 babel

  - `./next.config.js` 파일을 통해 webpack 및 babel 설정이 가능하다.

  - Webpack loader의 추가는 지양된다. 클라이언트 쪽의 코드만 webpack으로 번들되기 때문인데, 따라서 유사 기능을 위한 babel 플로그인 사용을 권장한다(`./babelrc` 로 제어). 예를 들면, `@svgr/webpack` 대신 `babel-plugin-inline-react-svg`를 사용한다.

  - Dynamic import(분기에 따라 필요없ㅂ는 import일 경우 dynamic import를 통해 bundle size가 늘어나는 문제를 피할 수 있다) 가 제공된다(SSr에서도 동시 지원하기 때문에 Next.js가 제공하는 dynamic import를 사용).

    ```js
      import dynamic from 'next/dynamic'

      const DynamicComponent = dynamic(() => import('../components/hello))
    ```

* Global Setting

- `_app`의 오버라이드

  - global로 root가 되는 컴포넌트가 필요하다면, `./pages/_app.jsx`를 통해 구현이 가능하다(이는 `/next/app`을 import하여 상속받은 컴포넌트로 작성). app은 각 페이지 컴포넌트를 wrapping하는 용도로 쓰인다:
    - 페이지 레이아웃: 모든 페이지가 중복되는 레이아웃을 포함시키면, 중복하여 번들이 되기 때문에 계속 지속되어야 하는 layout의 경우 `_app`에서 정의
    - global state 유지
    - global error에 대한 핸들링

- `_document` - 서버 사이드에서 렌더링되는 부분은 `./pages/_document.jsx`를 통해 제어 가능하다. `_document`는 각 페이지 컴포넌트를 wrappingg 한다. - 주로 document 마크업과 서버에서의 CSS-in-JS 라이브러리들의 구현을 위해 사용된다(글로벌 스타일을 번들링하여 `<style>`로 삽입). - HTML의 `<head>`를 위해 `next/head`로 Head 컴포넌트를 지원하며 이를 통해 설정이 가능하다. - Head 컴포넌트가 중복될 경우, 내부 태그는 key를 통해 관리 가능하다(중첩될 경우를 위해)

        ```html
            <Head>
              <title>My page title</title>
              <meta
                name="viewport"
                content="initial-scale=1.0, width=device-width"
                key="viewport"
              />
            </Head>

            <Head>
              <meta
                name="viewport"
                content="initial-scale=1.2, width=device-width"
                key="viewport"
              />
            </Head>

            <!-- key로 viewport를 설정해두었기 때문에 두 번째 meta태그만 렌더링이 된다. -->
            <!-- title이나 meta태그는 Head의 직접적인 children이 되지 않으면 클라이언트 사이드에서 위의 추정은 불가하다. -->
        ```

  - getInitialProps

    - 컴포넌트의 초기상태를 설정하기 위한 Next.js만의 React lifecycle이다. 서버와 클라이언트 모두에서 작동하며, 순서는 다음과 같다.

    ```
      Resolution order

        - On the server:

        1. _app.getInitialProps
        2. page.getInitialProps
        3. _document.getInitialProps
        4. _app.render
        5. page.render
        6. _document.render

        - On the server with error:

        1. _document.getInitialProps
        2. _app.render
        3. page.render
        4. _document.render

        - On the client
        1. _app.getInitialProps
        2. page.getInitialProps
        3. _app.render
        4. page.render

        Render app and page and get the context of the page with collected side effects

    ```

    - 가장 첫 페이지 로딩 시 서버에서 실행되며, 이후 클라이언트에서의 라우팅에선 모두 클라이언트(브라우저) 환경에서 실행된다.
    - 변수로 context object를 받으며 다음과 같은 값들을 가진다:

    |          |               description               | serverOnly |
    | :------: | :-------------------------------------: | :--------: |
    | pathname |            현 section의 URL             |     X      |
    |  query   |  URL 객체로부터 파싱된 쿼리스트링 객체  |     X      |
    |  asPath  | (String) 브라우저에서 보여지는 실제 URL |     X      |
    |   req    |           HTTP request object           |     O      |
    |   res    |          HTTP response object           |     O      |
    |   err    |  렌더링 중 발생한 error 객체가 들어 옴  |     X      |

    - Object를 반환하면 각 키는 컴포넌트의 contructor에 props로 전달된다.

      - getInitialProps가 서버사이드에서 실행될 땐 JSON.Stringify와 유사하게 직렬화(serialize)하여 데이터를 반환한다. 그렇기에 date, map, set등은 사용할 수 없다.

    - 따라서 필요하다면 async/await을 적용하여 서버사이드에서 초기 데이터를 받아 props로 내려줄 수 있다.
    - 오직 pages의 컴포넌트에서만 사용이 가능하다.

  * Routing

    - Next.js는 어플리케이션 내에서 가능한 모든 라우트 목록을 전달하지 않는데, 그렇기 때문에 현재 페이지는 다른 페이지들에 대한 정보를 전혀 알지 못한다. 그 다음에 오는 라우트들은 앱의 확장성을 위해 모두 lazy-loaded 된다(lazy-load가 필요하지 않는 것들은 로드하지 않는다).
    - Next에선 `/next/link`를 통한 <Link> 컴포넌트 혹은 Router(`next/router`)를 통해 클라이언트 라우팅이 가능하다.

    - `<Link>`의 props

      - `href`: (string)pages 경로의 라우팅 위치와 query string.
      - `as` : (string)브라우저에서 표시될 route 모양
      - `prefetch`: (boolean)true일 경우 서버에 라우팅 될 때, 페이지에 필요한 데이터를 백그라운드에서 미리 요청(내부적으로 <Link rel="preload>를 사용)

      - `<Link>`는 스타일링 불가 직속 자식으로 `a`를 대신 사용하여 여기에 스타일하자.

    ```js
    import Router from "next/router";

    Router.push({
      pathname: "/about",
      query: { name: "Zeit" }
    });
    <!-- 위의 코드는 <Link>와 같은 동작을 수행한다 -->
    ```

    - Property/method

    |                                                    |                                                                                                               |
    | :------------------------------------------------: | :-----------------------------------------------------------------------------------------------------------: |
    |                       route                        |                                              (string) 현재 path                                               |
    |                      pathname                      |                                        query string을 제외한 현재 path                                        |
    |                       query                        |                                      파싱된 쿼리스트링 (default값은 {})                                       |
    |                       asPath                       |                                         브라우저에 보여지는 실제 path                                         |
    |                   prefetch(url)                    |               Link 태그없이 prefetch를 사용할 때 사용된다(componentDidMount에서 사용하면 적절)                |
    |                 push(url, as=url)                  |                                      pushState과 같다. 주어진 url을 이용                                      |
    |                replace(url, as=url)                |                                    replaceState과 같다. 주어진 url을 이용                                     |
    | beforePopState(cb=({url, as, options}) => boolean) | popstate 이벤트를 통과하기 전 인터셉터하여 boolean을 반환한다(false 반환 시 popstate 취소, true 반환 시 진행) |

        - pushState = history.pushState
        - replaceState = history.replaceState
        - popstate = window.popstate

- Router Event 등록/제거 및 events

  - `Router.events.on('routeChangeStart', handleFunction);`
  - `Router.events.off('routeChangeStart', handleFunction);`

- 이벤트 종류

  - `routeChangeStart(url)`: route 변경이 실행될 때 실행
  - `routeChangeComplete(url)`: route 변경이 완전히 실행되었을 때 실행
  - `routeChangeError(err, url)`: route 변경 중 에러가 발생했을 때 실행
  - `beforeHistoryChange(url)`: 브라우저의 history 객체에 변화를 주기 전 실행
  - `hasChangeStart(url)`: 해쉬뱅(window.hash) 값이 변하고 페이지는 변하지 않았을 때 실행
  - `hasChangeComplete(url)`: 해쉬뱅(window.hash) 값이 변했고 페이지는 변하지 않았을 때 실행

- Client side routing은 다음과 같이 실행 된다:
  1. component가 요청 됨
  2. 만약 component가 `getInitialProps` 메소드를 가지고 있다면, 데이터를 요청하고 이 때 에러가 발생한다면 `./pages/_error.js`가 렌더링 된다
  3. 1과 2가 완료되면, pushState와 새 컴포넌트가 렌더링 된다
  - Shallow Routing: 2번 과정을 생략하고 실행하는 경우(`Router.push(url, as, { shallow: true})`)
    - Shallow routing은 같은 페이지에서 route가 변경된 경우만 가능하다(그렇지 않을 경우 `getInitialProps`가 실행된다)
    - 컴포넌트에서 `pathname`, `query`, `asPath`등의 정보가 필요하다면 `import { withRouter } from 'next/router'` HOC를 사용

* Styling
  - 기본 built-in 스타일링 기법으로 styled-jsx를 지원, \_document.js 파일의 `getInitialProps`에서 flush (`import flush from 'styled-jsx/server`) 함수를 실행하고 이를 styles로 props로 내려주면 서버사이드에서 스타일링의 연산을 실행해준다(기본적으로 styled-jsx는 클라이언트에서만 작동)
  - `@zeit/css`, `@zeit/scss` 등을 통해 해당 스타일링도 사용할 수 있게 제공하며, 서버에서 먼저 연산이 필요하다면 \_document에 빌드한 파일을 전달하는 식으로 사용할 수 있다.

---

## Shallow Routing

같은 페이지에서의 이동이 일어날 때, getInitialProps가 다시 작동되는 것을 방지하기 위해서 이와 같은 방법을 사용할 수 있다:

```js
import { withRouter, WithRouterProps } from "next/router"

// ...

<Link href="/faq" shallow={router.asPath === "/faq"}>

// ...

export default withRouter(Footer)

```

위 코드의 뜻은, `"/faq"`의 위치에서는 또 다시 getInitialProps를 실행시키지 않는다는 것이다. 이로 인해 불필요한 낭비를 줄일 수 있다.

---

## mapped type 사용의 예

개발 과제에 nextjs를 적용 중:

```js

const Footer: NextFunctionComponent = ({ router }) => {
  console.log(router.asPath);
  return (
    <div className="Footer">
      <div className="Footer__vision">
        <span>세상을 더 지적으로 사람들을 더 친하게</span>
        <Link href="/faq" shallow={router.asPath === "/faq"}>

// more codes...

```

위의 상태에서는 router에 타입을 지정하라고 난리친다!

이를 해결할 수 있는 방법 중 하나는:

```js
// next/router에서 선언되어 있는 WithRouterProps의 타입 설정을 가져오는 것
import { withRouter, WithRouterProps } from "next/router";


// type에 적용시키기 위한 빈 type 객체의 선언
// interface를 이용한 type 선언에는 최대한 많은 타입(사용이 될 모든 타입)을 선언하는 것이 좋다
interface OwnProps {

}

type CombineProps = OwnProps & Required<WithRouterProps>

// 위와 같이 OwnProps에 <WithRouterProps>를 필요하게 끔 만들어서 OwnProps가 결국 next/router에서 선언된 타입들을 가지게 하는 것

// 이제 더 이상 빨간 줄이 보이지 않는다!
const Footer: NextFunctionComponent<CombineProps> = ({ router }) => {
  // ...
```

신기방기!
