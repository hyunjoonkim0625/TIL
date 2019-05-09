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

[더 많은 사용의 예시](https://www.notion.so/Enum-Types-d1765ed16f5648d0a68fd7acbbbdeeef)

**Any** 타입은 변수가 어떤 타입을 가져야 할지 명확하지 않을 때 사용할 수 있겠다.

```js
let randomValue: any = 10;
randomValue = true;
randomValue = "Hello World";
```

하지만, 밑에와 같이 작성한 코드들에 대해서는 에러가 발생하지 않는다:

```js
let myVariable: any = 10;

console.log(myVariable.name);
myVariable();
myVariable.toUpperCase();
```

타입스크립트 3.0에서는 Unknown이라는 타입을 새로 내놓게 된다.

**Unknown** 타입은 any 타입과 거의 비슷하나 unknown 타입이 지정된 변수에는 접근하지 못한다. 이 뜻은 타입을 확정해주지 않으면 다른 곳에 할당할 수 없고, 사용할 수 없다.

```js
let myVariable: unknown = 10;

// 이 밑의 코드들에는 에러가 발생한다.
console.log(myVariable.name);
myVariable();
myVariable.toUpperCase();
```

이를 피하고 싶다면 아래와 같이 코드를 작성할 수 있다:

```js
// myVariable 변수는 스트링 취급을 받는다고 명해주는 것
(myVariable as string).toUpperCase()
```

**또한, 유저가 직접 타입의 성격을 작성하여 사용할 수도 있다**

```js
// name 속성이 object 안에 존재하는가에 대한 확인을 하는 함수
function hasName(obj: any): obj is { name: string } {
  return !!obj && typeof obj === "object" && "name" in obj;
}
```

**[더 자세한 예시](https://www.notion.so/Type-Guard-4601863eaa6c422e8b495e1bb4ba75eb)**

**한 변수에 여러 타입(union types)을 지정할 수도 있다**
Any 타입을 사용할 수도 있는데 왜 굳이 union 타입을 사용할까?

- Union 타입은 타입의 종류를 제한할 수 있는 반면에, any 타입은 어떠한 타입이던 접근할 수가 있다.
- Any 타입은 어떠한 intellisense의 도움을 받을 수 없다.

```js
let multiType: number | boolean;

multiType = 20;
multiType = true;
```

---

### Functions

함수를 작성하는데 있어서도 정적인 타입 체크가 가능하다.

```js
function add(num1: number, num2: number) {
  return num1 + num2;
}

add(5, 10);
add("5", 10); // Error: Argument of type '"5"' is not assignable to parameter of type 'number'.
```

어떤 값을 리턴할 것인지에 대한 명시도 가능하다.

```js
function add(num1: number, num2: number): number {
  return num1 + num2;
}
// 위의 함수는 number 타입을 리턴할 것이다
```

`?`를 사용하여 add 함수의 파라미터(num2)가 undefined여도 여전히 함수를 불러올 수 있다.

```js
function add(num1: number, num2?: number): number {
  return num1 + num2;
}
```

첫 번째 파라미터는 optional(`?`)로 지정될 수 없기에, 특정한 파라미터가 optional이 되길 원한다면, 파라미터의 선언 순서를 바꿔야 한다.

파라미터에 default value를 지정해 주면:

```js
function add(num1: number, num2: number = 10): number {
  if (num2) return num1 + num2;
  else return num1;
}

add(5, 10); // 함수가 15를 반환한다
add(5); // 파라미터로 5만 주어졌어도, num2의 default value가 10이기 때문에 여전히 15를 반환한다
```

---

### Interface

이런 간단한 함수라면, 크게 문제 될 것이 없지만:

```js
function fullName(person: { firstName: string, lastName: string }) {
  console.log(`${person.firstName} ${person.lastName}`);
}

let p = {
  firstName: "Bruce",
  lastName: "Wayne"
};

fullName(p); // Bruce Wayne
```

하지만, 많은 변수 혹은 함수들이 추가되고 이를 관리하는 데 있어서는 interface를 이용하여 타입 체킹을 해주는 것이 더욱 용이하다:

```js
interface Person {
  firstName: string;
  lastName: string;
}

function fullName(person: Person) {
  console.log(`${person.firstName} ${person.lastName}`);
}

let p = {
  firstName: "Bruce",
  lastName: "Wayne" // ?를 사용하여 optional로 적용시키는 것도 가능
};

fullName(p); // Bruce Wayne
```

위와 같이 interface를 따로 작성해주면, interface의 재사용이 더욱 쉬울 뿐만 아니라 이곳에서 일어나는 변경은 Person interfacre를 사용하는 모든 곳에 적용될 것이기 때문에 유지/보수에도 더욱 용이하다.

---

### Class

Employee라는 클래스를 생성해보자:

```js
class Employee {
  employeeName: string;

  // to initialize the employeeName
  constructor(name: string) {
    this.employeeName = name;
  }

  greet() {
    console.log(`Good Morning ${this.employeeName}`);
  }
}

let emp1 = new Employee("Joon");

console.log(emp1.employeeName); // Joon
emp1.greet(); // Good Morning Joon
```

위의 Employee 클래스를 상속받아 Manager라는 클래스를 생성해보자:

```js
// Employee를 상속받아서 Manager라는 클래스를 생성하자
class Manager extends Employee {
  constructor(managerName: string) {
    // Employee의 클래스 생성자를 사용하기 위해, super 키워드를 사용하자
    super(managerName);
  }

  delegateWork() {
    console.log(`Manager delegating tasks`);
  }
}

let m1 = new Manager("Joon Kim");

// Employee를 상속받았기 때문에 m1이 Employee의 클래스 속성들에 접근할 수가 있다
m1.delegateWork(); // Manager delegating tasks
m1.greet(); // Good Morning Joon Kim
console.log(m1.employeeName); // Joon Kim
```

---

### Access Modifiers

Access modifiers는 클래스의 속성과 메소드에 대한 접근을 구성하는 키워드(public, private, protected)라고 볼 수 있다.

Default 설정으로는 public 설정이 되어있으며, 이는 프로그램 내에서 자유롭게 클래스 내의 속성들이나 메소드들에 접근을 할 수 있음을 뜻한다. 아니면 `public` 키워드를 사용하여 설정해줄 수 있다.

```js
class Employee {
  public employeeName: string;

  // to initialize the employeeName
  constructor(name: string) {
    this.employeeName = name;
  }

  greet() {
    console.log(`Good Morning ${this.employeeName}`);
  }
}
```

속성에 `private` 키워드를 지정해주면, 클래스 내에서만 해당 속성에 접근할 수 있다.

```js
class Employee {
  private employeeName: string;

  // to initialize the employeeName
  constructor(name: string) {
    this.employeeName = name;
  }

  greet() {
    console.log(`Good Morning ${this.employeeName}`); // 에러 발생하지 않음
  }
}

let emp1 = new Employee("Joon");

console.log(emp1.employeeName); // Error: Property 'employeeName' is private and only accessible within class 'Employee'.

// Employee를 상속받아서 Manager라는 클래스를 생성하자
class Manager extends Employee {
  constructor(managerName: string) {
    // Employee의 클래스 생성자를 사용하기 위해, super 키워드를 사용하자
    super(managerName);
  }

  delegateWork() {
    console.log(`Manager delegating tasks ${this.employeeName}`); // Error: Property 'employeeName' is private and only accessible within class 'Employee'.
  }
}

```

상속받은 클래스 내에서 employeeName을 사용하고 싶다면 `protected` 키워드를 사용하자. 이는 여전히 클래스 밖에서의 접근은 방지하지만 클래스 내에서의 속성 접근은 허용한다.

---

# Mapped Type

공부 자료의 출처는 [이곳](https://mariusschulz.com/blog/typescript-2-8-mapped-type-modifiers)입니다.

2016년 12월에 타입스크립트 버전 2.1에서 mapped type이 처음으로 추가되었다. 버전 2.8에서는 mapped type이 속성에다가 특정한 modifier를 추가하거나 제거할 수 있는 기능이 생겼다. 이전까지는 속성에 modifier를 추가할 수만 있었지, 제거는 할 수 없었다.

## `?` 속성 수식어

타입 선언을 할 때, `?` 수식어를 속성 이름 뒤에 추가하면 객체타입의 어느 속성이든지를 선택적(optional)으로 만들 수 있다.

```js
interface TodoItem {
  description: string;
  priority?: "high" | "medium" | "low";
}
```

`?` 수식어가 존재하기 때문에, `TodoItem` 타입의 객체를 생성할 때 `priority` 속성은 좀 더 명확해질 것이다. 하지만, 꼭 그렇게 되어야 하는 것은 아니다:

```js
// 'prioirt' 속성을 3가지 중 하나의 값에 부여할 수 있다
const todo1: TodoItem = {
  description: "Mow the lawn",
  priority: "high"
};

// 또는 아예 사용하지 않을 수도 있다(optional이기 때문)
const todo2: TodoItem = {
  description: "Mow the lawn"
};

// 마지막으로 'undefined'로 지정할 수도 있다.
const todo3: TodoItem = {
  description: "Mow the lawn",
  priority: undefined
};
```

특정한 객체 타입의 특정한 속성을 optional로 설정하는 법을 알아보았는데, 제너릭 타입에도 `?`를 사용하는 법을 알아보자.

## Partial<T> Mapped Type

주어진 타입의 모든 속성들을 변형시키는 것은 mapped type을 사용하는 완벽한 예이다. Mapped type은 타입들을 위한 mapping 함수를 정의할 수 있게 해준다. 그 뜻은, 존재하는 타입의 모든 속성들을 가져오면서 그 속성들을 mapping 규칙을 이용해 변형시킨다. 그 다음에, 새로이 변형된 타입들에 대한 속성들을 만들게 된다.

제너릭 `Partial<T>` mapped type을 만든 뒤에 `?` 수식어를 타입 T의 모든 속성들에게 부여하자:

```js
type Partial<T> = {
  [P in keyof T]?: T[P]
}
```

`Partial<T>` 타입은 `keyof` 연산자를 사용하여 T가 정의하는 모든 키들의 속성을 결정한다. `Partial<T>`는 `[indexed access types](https://mariusschulz.com/blog/typescript-2-1-keyof-and-lookup-types#indexed-access-types)`인 `T[P]`를 사용하여 T안에 있는 각각의 P 속성의 타입을 알아본다.

만약 `Partial<T>`를 위의 TodoItem에 적용시킨다면, 결과는 두 개의 optional 속성들을 가지게 될 것이다:

```js
type PartialTodoItem = Partial<TodoItem>;
// {
//   description?: string | undefined;
//   priority?: "high" | "medium" | "low" | undefined;
// }
```

`Partial<T>`타입은 많은 어플리케이션들에서 유용한 것이 밝혀졌는데, 타입스크립트 팀이 lib.es5.d.ts 파일을 같이 npm 패키지에 포함시켜 배포하는 것도 그 이유 중 하나이다.

```js
/**
 * Make all properties in T optional
 */
type Partial<T> = {
  [P in keyof T]?: T[P];
};
```

## `?` Mapped Type 수식어를 제거하는 법

`?` 수식어를 제거하는 방법은 `?` 수식어 앞에 `-`를 붙여주면 된다.

```js
/**
 * Make all properties in T required
 */
type Required<T> = {
  [P in keyof T]-?: T[P]
};
```

`Required<T>`를 사용하면 TodoItem 타입의 모든 속성들이 필요로 되도록 할 수 있다:

```js
type RequiredTodoItem = Required<TodoItem>;
// {
//   description: string;
//   priority: "high" | "medium" | "low";
// }
```

위의 변형을 하고나면, `priority` 속성은 더 이상 optional이 아니게 된다.

## `?` Mapped Type 수식어를 추가하는 법

제거하는 것과는 반대로 `+?`와 같이 `+`를 붙여주면, `?`를 추가하는 것이 된다. 밑에와 같이 정의할 수도 있다:

```js
type Partial<T> = {
  [P in keyof T]+?: T[P];
};
```

`+`를 붙이는 것과 안 붙이는 것의 차이는 없지만, 인터페이스나 타입을 optional 만들 때 사용되어왔던 `?` 수식어를 사용하는 것이 추천된다.

## `readonly` 속성 수식어

`readonly` 수식어는 mapped type 내에서 속성들을 `read-only`로 만들기 위해 사용된다.

```js
type ReadonlyTodoItem = Readonly<TodoItem>;
// {
//   readonly description?: string | undefined;
//   readonly priority?: "high" | "medium" | "low" | undefined;
// }
```

컴파일러는 할당하려는 변수가 `read-only` 속성이라고 에러메세지를 띄울 것이다.

```js
const todo: ReadonlyTodoItem = {
  description: "Mow the lawn",
  priority: "high"
};

// Error: Cannot assign to 'priority'
// because it is a read-only property.
todo.priority = "medium";
```

## `readonly` Mapped 타입 수식어를 제거하는 법

`?` 수식어를 제거했을 때 처럼, `readonly` 수식어도 `-readonly`와 같은 식으로 제거할 수 있다. `Mutable<T>`라는 mapped 타입을 정의한 뒤에 readonly 수식어를 T로부터 정의된 모든 속성으로부터 제거해보자.

```js
type Mutable<T> = {
  -readonly[P in keyof T]: T[P];
};
```

이제 다음의 코드를 작성한다면, 컴파일러는 readonly 속성에 대한 에러를 만들지 않는다.

```js
const todo: Mutable<ReadonlyTodoItem> = {
  description: "Mow the lawn",
  priority: "high"
};

todo.priority = "medium";
```

## `readonly` Mapped 타입 수식어를 추가하는 법

`+?`처럼 `+readonly`와 같은 방식으로 수식어를 추가할 수 있다:

```js
type Readonly<T> = {
  +readonly [P in keyof T]: T[P];
};
```

하지만 `+readonly`를 사용하는 이점이 없기 때문에, `readonly`를 사용하여 수식어를 추가해주는 것만으로도 충분하다.

---

# keyof and Lookup Types

자바스크립트는 매우 동적인 언어이다. 그렇기에, 때때로 정적 타입 시스템에 존재하는 작동 방식들의 의미를 잡아내기가 어려울 수 있다. 간단한 `prop` 함수를 예로 들어보자.

```js
function prop(obj, key) {
  return obj[key];
}
```

이 함수는 객체와 키를 매개변수로 받고 그것에 상응하는 속성의 값을 반환한다. 객체에 대한 다른 속성들은 완전히 다른 타입들을 가질 수 있으며, 우리는 `obj`가 어떠한 형태인지도 알지 못한다.

그렇다면 타입스크립트에서는 어떻게 이 함수를 작성할 수 있을까? 시도해보자!

```js
function prop(obj: {}, key: string) {
  return obj[key];
}
```

이 두 개의 타입 주석을 추가하면, `obj`는 항상 객체가 되어야 하고 `key`도 반드시 문자열 값이 할당되어야 한다. 우리는 이 두 매개변수에 대한 값들에 제한을 걸어놓은 것이다. 하지만, 여전히 반환되는 타입은 `any`가 될 것이다.

```js
const todo = {
  id: 1,
  text: "Buy milk",
  due: new Date(2016, 11, 31)
};

const id = prop(todo, "id"); // any
const text = prop(todo, "text"); // any
const due = prop(todo, "due"); // any
```

더 많은 정보를 제공하지 않으면, 어떤 값이 key 매개변수에 전달이 되는지 알 수가 없기 때문에 타입스크립트는 `prop`함수를 위한 더 명확한 타입을 추론할 수가 없다. 그렇기 때문에 우리는 좀 더 많은 타입 정보를 타입스크립트에게 알려줄 필요가 있다.

## `keyof` 연산자

타입스크립트 2.1버전에서는 `keyof` 연산자가 소개되었다. 이 연산자는 주어진 타입에 따른 키들을 요청하는데, 이것이 이 연산자가 `index type query`라고도 불리는 이유기도 하다. 아래와 같은 `Todo` 인터페이스를 작성했다고 가정하자.

```js
interface Todo {
  id: number;
  text: string;
  due: Date;
}
```

모든 속성 키들을 대표하는 타입을 돌려받기 위해 `Todo` 타입에 `keyof` 연산자를 적용할 수 있는데, 이는 [string literal types](https://mariusschulz.com/blog/string-literal-types-in-typescript)의 집합이다.

```js
type TodoKeys = keyof Todo; // "id" | "text" | "due"
```

`keyof`를 사용하는 대신 직접 `"id" | "text" | "due"`를 써줄 수도 있지만, 이는 다루기 힘들거나 에러가 많이 날 수도 있고 유지하기도 쉽지 않을 것이다. 또한, 직접 작성하는 것은 오직 `Todo`의 타입만을 위한 문제해결이 될 것이다.

## Indexed Access Types

우리는 `keyof`을 사용하여 `prop`함수에 대한 타입 선언을 좀 더 발전시킬 수 있다. 우리는 더 이상 `key` 매개변수로 아무 문자열이나 받고 싶지 않다. 대신에, 우리는 전달되는 객체의 타입에 키가 실제로 존재하도록 해야한다.

```js
function prop<T, K extends keyof T>(obj: T, key: K) {
  return obj[key]
}
```

타입스크립트는 이제 `prop`함수의 반환 타입이 `T[K]`를 가질 것이라고 예상하게 된다. 이것이 바로 `indexed access type 또는 lookup type`이라고 불린다. 이것은 `T`타입의 `K`속성의 타입을 대표한다. 이제 `prop` 메소드를 사용하여 todo 속성들에 접근하면, 올바른 타입들이 표시될 것이다.

만약 `todo` 객체에 존재하지 않는 키 값을 전달하려고 하면 컴파일러가 에러를 표시할 것이다.

또 다른 예로는, 타입스크립트 컴파일러에 포함되어 있는 lib.es2017.object.d.ts 타입 선언 파일의 `Object.entries()` 메소드를 확인해볼 수 있다.

```js
interface ObjectConstructor {
  // ...
  entries<T extends { [key: string]: any }, K extends keyof T>(o: T): [keyof T, T[K]][];
  // ...
}
```

`entries` 메소드는 튜플의 배열을 반환하고, 각각이 속성 키와 그에 상응하는 값을 포함하고 있다.

---
