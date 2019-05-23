# GraphQL을 알아보자!

참고한 공부 자료: [The Net Ninja의 youtube 영상](https://www.youtube.com/playlist?list=PL4cUxeGkcC9iK6Qhn-QLcXCXPQUov1U7f)

GraphQL은 강력한 쿼리 언어로, 유연하고 효과적으로 클라이언트와 서버가 데이터를 주고받을 수 있도록 해준다.

RESTFful 접근 방식을 살펴보면:

- 어떠한 책에 대한 서버 요청을 하고 싶다면:

  - domain.com/books/:id (title, genre, reviews, authorId 등을 포함하고 있다)

- 그 책의 작가에 대한 정보를 불러오고 싶으면, 위의 요청으로 받아온 authorId를 가지고 또 다른 요청을 보내야 한다:
  - domain.com/authors/:id

책에 대한 정보와 그 작가에 대한 정보를 가져오기 위해 2번의 요청이 이루어졌다. 구조가 깊어질 수록 그에 대한 요청을 따로 해야하기 때문에 효과적이지 않을 수 있다.

GraphQL 접근 방식을 살펴보면:

```js
{
  book(id: 123) {
    title
    genre
    reviews
    author {
      name
      bio
      books {
        name
      }
    }
  }
}
```

```js
// 불필요한 정보는 불러오지 않을 수 있다.
{
  book(id: 123) {
    title
    author {
      name
      books {
        name
      }
    }
  }
}
```

GraphQL에서는 한번의 요청으로 모든 관련된 정보를 가져올 수 있다.

**GraphQL query**

GraphQL query는 서버로 보내져서 해석되고 이행되는 문자열로, 클라이언트에는 JSON 형태로 반환된다. GraphQL query는 그 쿼리의 response를 반영한다. 그렇기에 데이터의 구조를 쉽게 예측할 수 있고, 이는 앱에 필요한 쿼리를 쉽게 작성할 수 있게 도와준다.

**Root query**

개별적인 쿼리 타입을 설정한 후, Root query에 다시 적용을 시켜주어야 쿼리 요청을 했을 때 원하는 값을 반환받을 수 있다.

```js
const BookType = new GraphQLObjectType({
  name: "Book",
  // fields가 함수로 선언되어야 하는 이유는:
  // 나중에 여러 가지의 타입이 생성되고, 서로에게 참조를 시켜야하는 상황이 오면,
  // 이 fields를 함수로 감싸주지 않으면 타입들이 서로에 대한 정보를 알 수가 없다
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    genre: { type: GraphQLString }
  })
});

const AuthorType = new GraphQLObjectType({
  name: "Author",
  // fields가 함수로 선언되어야 하는 이유는:
  // 나중에 여러 가지의 타입이 생성되고, 서로에게 참조를 시켜야하는 상황이 오면,
  // 이 fields를 함수로 감싸주지 않으면 타입들이 서로에 대한 정보를 알 수가 없다
  // 그 이유는, 코드의 실행 순서와 상관이 있기 때문인데, 함수로 선언된 부분이 먼저 실행된 뒤에 코드들이 읽어질 것이기 때문이다. 그렇기 때문에 함수로 선언이 되어있지 않으면, 선언 순서에 따라 undefined가 반환될 것이기에 fetch를 할 수가 없다.

  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt }
  })
});

const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  // 데이터에 접근하기 위한 start point
  fields: {
    // book이라는 query가 요청되면 아래의 정보를 반환할 것
    book: {
      type: BookType,
      // args -> 특정한 책을 불러오고 싶을 때, arguments를 같이 넘겨줘야(책의 id 등)할 때를 위한 설정
      args: { id: { type: GraphQLID } },
      // 얻고 싶은 데이터에 대한 코드를 작성하는 곳이 resolve
      // query 요청이 들어오면 이 resolve 함수가 반환될 것
      resolve(parent, args) {
        // code to get data from db / other source
        // lodash를 사용했지만, 나중에는 내가 직접 코드를 작성해보자
        return books.find(book => book.id === args.id);
        // return _.find(books, { id: args.id });
      }
    },
    author: {
      type: AuthorType,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        return authors.find(author => author.id === args.id);
      }
    }
  }
});
```

---

## Schema

---

## Types

GraphQLID를 사용하면, integer를 쿼리 요청으로 보내도 작동하게 된다. 그 이유는 integer로 들어온 값도 결국 string으로 변환되어 요청을 하기 때문이다.

**Type relations**

```js
const BookType = new GraphQLObjectType({
  name: "Book",
  // fields가 함수로 선언되어야 하는 이유는:
  // 나중에 여러 가지의 타입이 생성되고, 서로에게 참조를 시켜야하는 상황이 오면,
  // 이 fields를 함수로 감싸주지 않으면 타입들이 서로에 대한 정보를 알 수가 없다
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    genre: { type: GraphQLString }
  })
});

const AuthorType = new GraphQLObjectType({
  name: "Author",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt }
  })
});
```

위으 코드들은 서로에 대한 정보를 아직 알지 못하는데, 이 둘에게 관계를 설정해주려고 하면:

```js
var books = [
  { name: "Name of the Wind", genre: "Fantasy", id: "1", authorId: "1" },
  { name: "The Final Empire", genre: "Fantasy", id: "2", authorId: "2" },
  { name: "The Long Earth", genre: "Sci-Fi", id: "3", authorId: "3" }
];

const authors = [
  { name: "Patrick Rothfuss", age: 44, id: "1" },
  { name: "Brandon Sanderson", age: 42, id: "2" },
  { name: "Terry Pratchett", age: 66, id: "3" }
];

// 위와 같이 books array 내에 책에 연관된 작가의 id를 지정해주기 위해 authorId를 추가하고,
// 이를 BookType에 추가시켜주자

const BookType = new GraphQLObjectType({
  name: "Book",
  // fields가 함수로 선언되어야 하는 이유는:
  // 나중에 여러 가지의 타입이 생성되고, 서로에게 참조를 시켜야하는 상황이 오면,
  // 이 fields를 함수로 감싸주지 않으면 타입들이 서로에 대한 정보를 알 수가 없다
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    genre: { type: GraphQLString },
    author: {
      type: AuthorType,
      resolve(parent, arg) {
        // 이런 상황에서 parent 파라미터를 사용하게 되는데,
        // parent는 books array가 된다!
        console.log(parent);
        return authors.find(author => author.id === parent.authorId);
      }
    }
  })
});
```

---

## GraphQL lists

위의 예시에서는, 각 책에 대한 작가를 불러올 수 있도록 해주었다. 각 책에는 한 명의 작가만 존재하니까 `author:{type: AuthorType,`와 같이 type을 작성할 수 있었다. 하지만 작가가 어떠한 책을 가지고 있는지에 대한 쿼리 요청을 보낼 때는 한 권의 책이 아닌 여러 권의 책을 쓴 작가도 있을 것이기에 다른 타입 설정을 해주어야 한다:

```js
const AuthorType = new GraphQLObjectType({
  name: "Author",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt },
    books: {
      // const { GraphQLList } = graphql을 추가하자
      type: new GraphQLList(BookType),
      resolve(parent, args) {
        // parent는 authors array
        return books.filter(book => book.id === parent.id);
      }
    }
  })
```

---

## 하나의 데이터가 아닌 전체의 데이터를 가져오기

책 한권이 아닌, 모든 책의 리스트를 가져오고 싶으면:

```js
const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  // 데이터에 접근하기 위한 start point
  fields: {

  // ...

  // 더미 데이터인 books = [...]
    books: {
      type: new GraphQLList(BookType),
      resolve(parent, args) {
        return books;
      }
    }
```

---

## Mutations

Mutation은 데이터를 변형(mutate or change) 혹은 변화시키는 것을 말하는데, 이는 데이터를 추가, 삭제, 수정 등의 모든 행위가 mutation이다. GraphQL에서는 우리가 명백하게 mutation을 정의할 필요가 있다.

**Mutation의 정의**

뮤테이션은 schema 타입을 정의할 때와 매우 비슷하다:

```js
const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    addAuthor: {
      type: AuthorType,
      args: {
        name: { type: GraphQLString },
        age: { type: GraphQLInt }
      },
      resolve(parent, args) {
        // 우리가 상단에 import한 Author를 사용하여 mongoose에 데이터를 전해줄 것
        let author = new Author({
          // 위에 정의한 args
          name: args.name,
          age: args.age
        });
        // mongoose의 장점! 그냥 save method를 사용하여 데이터베이스에 저장할 수 있다
        // return을 해줘야지만 우리가 원하는 response를 반환받을 수 있다
        return author.save();
      }
    }
  }
});

// 그 후에 export를 해주어야 한다
module.exports = new GraphQLSchema({
  query: RootQuery,
  mutation: Mutation
});
```

위와 같이 정의한 뒤에, GraphiQL에서 데이터를 직접 mutate해보고 싶다면:

```js
mutation {
  // addAuthor의 파라미터로 내가 추가하고 싶은 정보를 넣어주고,
  addAuthor(name: "Joon", age: 31) {
    // 그에 대한 response로 어떠한 값을 받고 싶은지에 대한 정의
    name
    age
  }
}
```

---

## Updating resolve functions

Resolve 함수를 이용하여 컬렉션에 맞는 데이터를 불러오자. BookType의 resolve에 누군가가 root query에서 요청을 보낼 때, author와 관련된 정보도 받아올 수 있도록 만들자:

```js
const BookType = new GraphQLObjectType({
  name: "Book",

  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    genre: { type: GraphQLString },
    author: {
      type: AuthorType,
      resolve(parent, arg) {
        // book에 대한 쿼리 요청이 들어올 때, author에 대한 정보도 같이 불러오고 싶다
        // findById는 몽구스 라이브러리의 메소드
        return Author.findById(parent.author.id);
      }
    }
  })
});

// author type에서 book에 관한 정보를 가져오려 할 때

const AuthorType = new GraphQLObjectType({
  name: "Author",
  fields: () => ({
    id: { type: GraphQLID },
    name: { type: GraphQLString },
    age: { type: GraphQLInt },
    books: {
      type: new GraphQLList(BookType),
      resolve(parent, args) {
        return Book.find({
          authorId: parent.id
        });
      }
    }
  })
});

// 책 하나의 정보나 아니면 모든 책들의 정보를 불러올 필요가 있을 때

const RootQuery = new GraphQLObjectType({
  name: "RootQueryType",
  // 데이터에 접근하기 위한 start point
  fields: {
    // book이라는 query가 요청되면 아래의 정보를 반환할 것
    book: {
      type: BookType,
      // args -> 특정한 책을 불러오고 싶을 때, arguments를 같이 넘겨줘야(책의 id 등)할 때를 위한 설정
      args: { id: { type: GraphQLID } },
      // 얻고  싶은 데이터에 대한 코드를 작성하는 곳이 resolve
      // query 요청이 들어오면 이 resolve 함수가 반환될 것
      resolve(parent, args) {
        // code to get data from db / other source
        // 당연히 책의 id를 알아야 그 하나만의 정보를 가져올 수 있다
        // 특정한 book의 데이터를 가져오기 위해
        return Book.findById(args.id);
      }
    },
    author: {
      type: AuthorType,
      args: { id: { type: GraphQLID } },
      resolve(parent, args) {
        return Author.findById(args.id);
      }
    },
    books: {
      type: new GraphQLList(BookType),
      resolve(parent, args) {
        // 모든 책들의 정보
        // 빈 객체를 넘겨줄 경우, book에 포함된 모든 내용을 가져온다
        return Book.find({});
      }
    },
    authors: {
      type: new GraphQLList(AuthorType),
      resolve(parent, args) {
        return Author.find({});
      }
    }
  }
});
```

---

## Required fields (GraphQLNonNull)

만약 작가에 대한 프로필을 추가하는 mutation을 요청할 때, age를 넘겨주지 않는다고 해도 그대로 요청이 들어갈 것이다. 혹은 반대로, name을 넘겨주지 않고 age만 적는 상황도 발생할 수 있다. 이를 방지하기 위해 `GraphQLNonNull`을 적용시켜보자.

```js



const Mutation = new GraphQLObjectType({
  name: "Mutation",
  fields: {
    addAuthor: {
      type: AuthorType,
      args: {
        // new GraphQLNonNull을 적용하자!
        name: { type: new GraphQLNonNull(GraphQLString) },
        age: { type: new GraphQLNonNull(GraphQLInt) }
      },
```

---

## 프론트엔드의 구성: 아폴로 클라이언트 사용

GraphQL 쿼리 언어는 자바스크립트처럼 보이긴해도 자바스크립트가 아니기 때문에, 우리가 만들 앱과 GraphQL을 이어주기 위해서 Apollo를 사용하여 쿼리 요청을 서버에 보내게 할 것이다.

```js
import React from "react";
import ApolloClient from "apollo-boost";
import { ApolloProvider } from "react-apollo";

import BookList from "./components/BookList";

// apollo client setup

const client = new ApolloClient({
  uri: "http://localhost:4000/graphql"
});

function App() {
  return (
    <ApolloProvider client={client}>
      <div id="main">
        <h1>Reading List</h1>
        <BookList />
      </div>
    </ApolloProvider>
  );
}

export default App;
```

client라는 end point를 설정 후, provider에 prop으로 넘겨주고 이 provider 내에 다른 컴포넌트들을 포함시킨다.

---

## Making queries from React

쿼리를 만들어 준 뒤에 컴포넌트에 그 쿼리를 적용시킨 뒤 사용하자. 쿼리 언어는 자바스크립트가 아니기 때문에, parsing을 도와줄 무언가가 필요하다.

```js
// parsing을 위한 importing
import { gql } from "apollo-boost";

// 요청을 위한 코드
const getBooksQuery = gql`
  {
    books {
      name
      id
    }
  }
`;
```

위와 같이 작성한 코드를 컴포넌트에 적용시키려면:

```js
// react-apollo로 부터 graphql을 불러온 뒤
import { graphql } from "react-apollo";

// code...

// getBooksQuery와 BookList 컴포넌트를 연결해주면 된다
export default graphql(getBooksQuery)(BookList);
```

위의 상태로 요청을 보내면, CORS 에러가 발생할 것이기에 server에 조취를 취해줘야 한다. 서버 폴더 내에 `npm i cors` 후, app.js 파일에 가서:

```js
const cors = require("cors");

const app = express();

// allow cross-origin requests
app.use(cors());
```

위와 같이 코드를 작성하자.

그리고 클라이언트 사이드로 돌아와서, 위의 요청이 잘 들어왔는지 `console.log(this.props)`로 확인을 할 수 있다. 그리고 이 데이터의 구조가 어떻게 들어왔는지를 살펴보고 필요한 곳에 작성해서 쓰자!

```js
// for instance,
displayBooks() {
    const data = this.props.data;

    if (data.loading) {
      return <div>Loading books...</div>;
    } else {
      return data.books.map(book => {
        return <li key={book.id}>{book.name}</li>;
      });
    }
  }
```

---

## External query file

쿼리 요청이 많아지게 되면, 이 코드들을 한 파일에 모아놓고 이들을 import해서 사용하는 것이 코드의 품질 관리에 더욱 유리할 것이다.

```js
// 파일들에 따로 정의되었던 쿼리들을 한 곳에 모아서 export 하자

import { gql } from "apollo-boost";

const getBooksQuery = gql`
  {
    books {
      name
      id
    }
  }
`;

const getAuthorsQuery = gql`
  {
    authors {
      name
      id
    }
  }
`;

export { getAuthorsQuery, getBooksQuery };
```

한 파일에 하나 이상의 쿼리가 존재할 때는 `{compose}`를 import하여 파일과 쿼리를 묶어줄 수 있다.

```js
import { graphql, compose } from "react-apollo";

// code ...

// name을 선언해주는 이유는, 이름을 정의 안하고 하나의 쿼리 요청이 있을 때는 data라는 prop 값이 들어왔지만,
// 여러 개의 쿼리가 있을 때는 각각에 맞는 이름을 설정한 뒤
// 그에 맞게 데이터를 사용하면 된다
export default compose(
  graphql(getAuthorsQuery, { name: "getAuthorsQuery" }),
  graphql(addBookMutation, { name: "addBookMutation" })
)(AddBook);
```

어떻게 하면 addBookMutation을 사용하여 사용자가 서버에 내용을 저장할 수 있을까?

```js
// queries.js에 이렇게 작성했던 코드를
const addBookMutation = gql`
  mutation {
    addBook(name: "", genre: "", authorId: "") {
      name
      id
    }
  }
`;

// 아래와 같이 수정
// '!'은 변수가 꼭 필요하다는 표시
const addBookMutation = gql`
  mutation($name: String!, $genre: String!, $authorId: ID!) {
    addBook(name: $name, genre: $genre, authorId: $authorId) {
      name
      id
    }
  }
`;
```

만약 submit을 하는 경우에 mutation을 사용한다고 하면:

```js
// 밑과 같이 variables:{} 를 추가하여 데이터를 서버에 저장시킬 수 있다
submitForm = e => {
  const { name, genre, authorId } = this.state;
  e.preventDefault();

  this.props.addBookMutation({
    variables: {
      name,
      genre,
      authorId
    }
  });
};
```

---

## Re-fetching queries

지금은 새로운 책을 추가해도 상태가 바뀌지 않는다. 상태를 바꾸고 새로운 화면으로 업데이트하기 위해서는 새로 바뀐 데이터를 다시 받아오는 과정이 필요하다.

```js
// getBooksQuery를 추가로 import: 책의 리스트를 다시 업데이트 하기 위해
import {
  getAuthorsQuery,
  addBookMutation,
  getBooksQuery
} from "../queries/queries";

submitForm = e => {
  const { name, genre, authorId } = this.state;
  e.preventDefault();

  this.props.addBookMutation({
    variables: {
      name,
      genre,
      authorId
    },
    refetchQueries: [{ query: getBooksQuery }]
  });
};
```

---

## Making a single query

책 한권만의 데이터를 가져오고 싶다고 했을 때:

```js
// get a single book data
const getBookQuery = gql`
  query($id: ID) {
    book(id: $id) {
      id
      name
      genre
      author {
        id
        name
        age
        books {
          name
          id
        }
      }
    }
  }
`;
```

그리고 난 뒤에, BookDetails라는 컴포넌트를 만들어서 그 곳에서 책 한권의 정보를 보여줄 것인데:

```js
// code ...

export default graphql(getBookQuery)(BookDetails);
```

위와 같이 선언하면, prop으로 받는 특정한 책의 id를 모르기 때문에 아무 데이터도 가져올 수가 없다.

그렇기 때문에 export를 할 때:

```js
export default graphql(getBookQuery, {
  // prop이 업데이트 될 때마다 새로운 variable들로 업데이트 한다
  options: props => {
    return {
      variables: {
        id: props.bookId
      }
    };
  }
})(BookDetails);
```

---

## Apollo server를 이용한 GraphQL 학습

참고한 공부 자료: [Ben Awad의 youtube 영상](https://www.youtube.com/playlist?list=PLN3n1USn4xln0j_NN9k4j5hS1thsGibKi)

`npm add apollo-server graphql` 후, `src/index.js` 파일을 생성한 다음에 다음의 코드를 작성하자:

```js
const { ApolloServer, gql } = require("apollo-server");

// schema or typeDefs
// '!' = not null

const typeDefs = gql`
  type Query {
    hello: String!
  }
`;

const resolvers = {
  Query: {
    hello: () => "Hello World!"
  }
};

const server = new ApolloServer({ typeDefs, resolvers });

server.listen().then(({ url }) => console.log(`server started at ${url}`));
```

그 후, `node index`(폴더 경로 내에서)로 서버를 실행시키면 `localhost:4000`으로 접속할 수가 있다.

그 후에 쿼리 요청을 보내면서 이것저것을 해보자!

**type check**

Type checking의 종류에는:

- query vs. mutation
- objects
- arrays
- arguments

- crud: create(mutation), read(query), update(mutation), delete(mutation)

배열이 있을 때, 배열 안과 밖 모두에 null을 방지하고 싶을 때

```js
 type RegisterResponse {
    //  [] -> a type that is in an array
    // 안과 밖 모두에 '!'
    errors: [Error!]!
    user: User!
  }

```

---

**중복되는 파라미터의 작성**

밑의 코드와 같이 중복으로 사용되는 파라미터가 있다:

```js
  type Mutation {
    register(username: String!, password: String!, age: Int): RegisterResponse!

    login(username: String!, password: String!, age: Int): Boolean!
  }
```

이 경우에는 타입 정의에 밑에와 같은 코드를 작성해서 사용할 수 있다:

```js
  input UserInfo {
    username: String!
    password: String!
    age: Int
  }

  // 위의 UserInfo를 타입으로 넣어주면 된다

  type Mutation {
    register(userInfo: UserInfo!): RegisterResponse!

    login(userInfo: UserInfo): Boolean!
  }
```

뮤테이션에서는 코드가 작성된 순서대로 작동한다.

---

**resolver**

Resolver에는 네 가지의 파라미터가 들어올 수 있다. `parent, args, context, info`가 이 네 가지에 해당한다.

우선 **args**는:

```js
 type Mutation {
    register(userInfo: UserInfo): RegisterResponse!
    login(userInfo: UserInfo): String!
  }

  // Mutation에 login이 정의되어 있기에 밑에 args 대신 분해대입 하여 사용할 수 있다


 Mutation: {
    login: (parent, {userInfo: {username}}, context, info) => {
      return username
    },
```

그 후에, 이렇게 요청을 보낼 수 있다:

```js
mutation {
  login(userInfo: {username: "hey", password: "he12"})
}
```

**context**는 resolver들에 대한 접근을 가능하게 해주는데:

```js
// 서버를 정의해 주는 곳에 context를 추가
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req, res }) => ({ req, res })
});
```

그 후에:

```js
  Mutation: {
    login: (parent, { userInfo: { username } }, context, info) => {
      console.log(context);

  // console에 밑의 정보들이 나타날 것
  // { req:
  //  IncomingMessage {
  //    _readableState:
  //     ReadableState {
  //       objectMode: false,
  //       highWaterMark: 16384,
  //       buffer: [Object],
  //       length: 0,
  //       pipes: null,
  //       pipesCount: 0,
  //       flowing: true,
  //       ended: true,
  //       endEmitted: true,
  //       reading: false,
  //       sync: false,

```

resolver 내에서는:

```js
  Mutation: {
    login: async (parent, { userInfo: { username } }, context, info) => {
      // resolver 내에서는 많은 것들을 할 수 있는데,
      // 위에서 처럼 async를 사용하고 promise를 사용하는 등의 동작을 할 수 있다

      // to check the password
      // await checkPassword(password)
      return username;
```

**info**는 라이브러리들을 사용할 때 많이 사용되곤 한다...

**parent**가 정해지는 기준은, 밑의 코드를 예로 들었을 때:

```js
// resolvers의 맨 위에서 User라는 resolver를 작성했고,
// 이에 대한 parent는
const resolvers = {
  // User의 resolver를 작성
  User: {
    username: parent => "i am username"
  },

  // codes ...

  Mutation: {
    login: async (parent, { userInfo: { username } }, context, info) => {
      return username;
    },
    // 이곳에서 user가 User를 사용하여 먼저 실행이 되기 때문에,
    // parent는 이 User가 실행 된 register가 되고,
    // 이 안에 있는 user에 접근할 수가 있다
    register: () => ({
      errors: [
        {
          field: "username",
          message: "bad"
        },
        {
          field: "username2",
          message: "bad2"
        }
      ],
      user: {
        id: 1,
        username: "bob"
      }
    })
  }
};
```

**parent**는 많이 쓰이게 되는데:

```js
// 밑과 같이 필요한 경우에 사용할 수 있다
const resolvers = {
  // User의 resolver를 작성
  // 이렇게 선언된 User의 resolver는 다른 곳에서 사용된
  // User의 resolver를 덮어 쓸 것이다
  // 왜냐면, 이 User resolver는 User가 실행될 때마다 return 될 것이기에

  User: {
    // type User에도 추가시켜야 한다
    firstLetterOfUsername: parent => {
      return parent.username[0];
    }
  },
```

---
