# GraphQL을 알아보자!

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
