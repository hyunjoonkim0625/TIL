# 타입스크립트

공부 내용의 출처들은: - [TypeScript Cookbook](https://www.notion.so/TypeScript-Cookbook-727820ac0d4544498ebe732e940816f4) - [자바스크립트 개발자를 위한 타입스크립트](https://ahnheejong.gitbook.io/ts-for-jsdev/)

---

## 2019. 5. 3

### 타입스크립트란?

타입스크립트는 프로그래밍 언어로, 타입스크립트 언어 문법에 맞춰 작성된 소스 코드를 자바스크립트 런타임 환경에서 실행 가능한 코드로 변환시켜 준다. 이렇게 변환시켜주는 행위를 **컴파일**이라고 한다.

컴파일을 해주는 프로그램을 **컴파일러 (타입스크립트 컴파일러)**라고 부른다. 이렇게 컴파일을 해서 사용해야하기 때문에 타입스크립트를 **compiled language**라고 한다.

컴파일 타임(Compile Time)은 타입스크립트 언어의 문법에 맞춰 작성된 소스코드를 자바스크립트 런타임 환경에서 실행 가능한 코드로 변환시켜 주는 행위를 하는 시점(시간)이다.

자바스크립트의 경우는 별도의 컴파일 과정 없이 바로 실행이 가능하기 때문에 **interpreted language**라고 한다. 이는 전통적인 컴파일 언어와는 다르다.

### 타입이 있는 자바스크립트

먼저 타입이 있는 자바스크립트란 단어는 보다 정확히는 **정적 타입 시스템(static type system)을 도입한 자바스크립트**라는 뜻이다. 정적 타입 시스템이 있는 언어, 즉 정적 타입 언어(statically typed language)에서는 프로그램의 예상 동작을 타입을 통해 나타내고, 그 예상에 걸맞게 동작할 여부를 타입 검사기(type checker)를 통해 실행 전에 확인할 수 있다.

---

## 2019. 5. 4

### 컴파일러 옵션들(Compiler options)

JSON 파일을 만든 후, 그 안에 설정들을 작성한 뒤 적용시킬 수 있다.

### 자바스크립트의 상위집합

모든 자바스크립트 코드는 타입스크립트 코그이다. 때문에 타입스크립트 컴파일러는 확장자만 바꾸면 자바스크립트 코드를 이해한다. 이러한 접근은 아래와 같은 장점을 가지게 된다:

- 기존 자바스크립트 코드베이스의 마이그레이션에 드는 비용이 작다
- 완만한 러닝 커브를 가진다
- 서드파티 자바스크립트 패키지의 사용이 상대적으로 수월하다

---

## 2019. 5. 5

### [초보를 위한 자바스크립트 튜토리얼 영상](https://www.youtube.com/watch?v=WBPrJSw7yQA)

ts 파일을 js 파일로 컴파일한 뒤에 ts 파일에 생기는 에러메세지(블록스코프 화 된 변수를 다시 선언할 수 없다)는 파일이 script로 취급받기 때문인데(모듈 취급을 받지 않음), 이를 해결하기 위해서는 export 구문을 작성해주어야 한다.

ts 파일에 변경이 일어난 뒤, 자동으로 컴파일을 시키고싶으면 `tsc file_name --watch`를 터미널에 입력해줄 수 있다.

### Variable Declarations

자바스크립트는 let과 const를 사용하지 않는다면 블락 레벨 스코프 변수를 작성할 수가 없다. let은 재대입은 가능하지만 재선언을 할 수 없다(값을 지정하지 않고 `let sum;`과 같이 변수를 작성할 수 있다). const는 값과 함께 선언이 되어야 하고, 한 번 선언이 되면 재대입이나 재선언이 불가능하다.

그렇기에 변수를 선언할 때 그 변수에 변경이 일어나지 않을 것이 확신되면 const로 변수를 선언하고, 그 외의 경우에는 let으로 변수를 선언하자.

### Variable Types

타입스크립트에서의 basci types라고 하면:

- ECMAScript 표준에 따른 기본 자료형 6가지

  - Boolean
  - Number
  - String
  - Null
  - Undefined
  - Symbol (ECMAScript 6에 추가)
  - Array : object

- 프로그래밍을 도울 타입들
  - Any
  - Void
  - Never
  - Enum
  - Tuple : object
  - Unknown

타입스크립트에서 변수에 대한 타입 선언은 어떻게?

```ts
let isBeginner = true;

// 위와 같이 작성된 코드가 있다면 아래와 같이 타입을 선언해줄 수 있다.
let isBeginner: boolean = true;
```

변수에 대한 타입 체크를 하는 이유는 정적 타입 체크를 위한 것인데, 이는 string으로 타입이 설정된 변수에 number를 재대입을 하려고 하면 코드에디터에서 바로 확인이 가능하다. 이로 인해 사람의 실수를 바로 체크할 수가 있다.

`null`과 `undefined`는 모든 타입들의 sub-type의 역할을 하며, 이 뜻은 boolean, number 그리고 string 타입의 값으로 `null`과 `undefined`를 할당할 수 있다.

**프로그래밍을 도와주는 타입들**

**Array** 타입을 변수에 할당하고 싶으면, `let list1: number[] = [1, 2, 3]`과 같이 작성할 수 있다. 또는 `let list2: Array<number> = [1, 2, 3]`과 같이 작성할 수도 있다.

**Tuple** 타입 활용의 예는, `let person1: [string, number] = ["Chris", 22]`과 같이 작성할 수가 있다. 이 상황에서는 오직 하나의 스트링 값과 하나의 숫자 값만이 person1 변수에 할당될 수 있다. 선언된 순서도 일치해야 한다.

**Enum** 타입 활용의 예는:

```js
enum Color {
  Red, // 0
  Green, // 1
  Blue // 2
}

let c: Color = Color.Green;

console.log(c);   // 1을 출력
```

만약, 값의 변화를 주려고 한다면:

```js
enum Color {
  Red = 5, // 5
  Green, // 6
  Blue // 7
}

let c: Color = Color.Green;

console.log(c);   // 6을 출력
```

**Any** 타입은 변수가 어떤 타입을 가져야 할지 명확하지 않을 때 사용할 수 있겠다.

```js
let randomValue: any = 10;
randomValue = true;
randomValue = "Hello World";
```
