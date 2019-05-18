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

---

## Schema
