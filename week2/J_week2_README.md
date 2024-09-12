#  한 입 크기로 잘라먹는 타입스크립트 week2 😋

> ## 인터페이스

타입 별칭과 동일하게 이름을 지어주는 문법

``` typescript
interface Person {
  name: string;
  age: number;
}
```

    🤔 타입별칭과 다른점?

      타입 별칭에서는 Unoin이나 intesection 타입 정의 가능
      inteface에선 불가!

### 인터페이스 확장

인터페이스 확장이란 하나의 인터페이스를 다른 인터페이스들이 **상속받아 중복된 프로퍼티를 정의하지 않도록** 도와주는 문법

``` typescript
interface Animal {
  name: string;
  color: string;
}

interface Dog extends Animal {
  breed: string;
}

interface Cat extends Animal {
  isScratch: boolean;
}

interface Chicken extends Animal {
  isFly: boolean;
}
```


✚ 프로퍼티 재 정의 하기

상속받은 interface의 프로퍼티의 재 정의도 가능


```typescript
interface Animal {
  name: string;
  color: string;
}

interface Dog extends Animal {
  name: "doldol"; // 타입 재 정의
  breed: string;
}
````

        ⚠️
        한가지 주의할 점은 프로퍼티를 재 정의할 때 원본 타입을 A 재 정의된 타입을 B라고 하면 반드시 A가 B의 슈퍼 타입이 되도록 재정의 해야 함
        
<img width="401" alt="스크린샷 2024-09-11 오전 9 20 47" src="https://github.com/user-attachments/assets/c5a96b53-2076-4927-aab0-f44cca14e1d6">


### 인터페이스 선언 합침

타입별칭과 달리 동일한 스코프 내에 중복된 이름으로 선언 가능함

**타입별칭**
```typescript
type Person = {
  name: string;
};

type Person = { ❌
  age: number;
};
````

**interface**

```typescript
interface Person {
  name: string;
}

interface Person { // ✅
  age: number;
}
```


> ## 클래스

붕어빵이 객체라면 붕어빵 기계는 클래스


```typescript
class Student {
  // 필드
  name;
  age;
  grade;

  // 생성자
  constructor(name, grade, age) {
    this.name = name;
    this.grade = grade;
    this.age = age;
  }
}

const studentB = new Student("홍길동", "A+", 27);

console.log(studentB);
// Student { name: '홍길동', age: 27, grade: 'A+' }
````

### 타입스크립트의 클래스

- 타입스크립트에서는 클래스의 필드를 선언할 때 타입 주석으로 타입을 함께 정의해 주어야 함
- 타입스크립트의 클래스는 타입으로도 사용할 수 있음

```typescript
class Employee {
  (...)
}

const employeeC: Employee = {
  name: "",
  age: 0,
  position: "",
  work() {},
};
````

**상속**

타입스크립트에서 클래스의 상속을 이용할 때 파생 클래스에서 생성자를 정의 했다면<br>

1. 반드시 **super 메서드**를 호출해 슈퍼 클래스의 생성자를 호출해야 하며, 
2. 호출 위치는 **생성자의 최상단**이어야만 합니다.

### 접근제어자
- public<br>
어디서든지 이 프로퍼티에 접근할 수 있음
- private<br>
클래스 내부에서만 이 필드에 접근

- ptroected

> ## 제너릭



🤔 타입만 다르고 같은 기능을 하는 함수 중복 해결할 수 없을까?
```typescript
function func(value: any) {
  return value;
}

let num = func(10);
// any 타입

let str = func("string");
// any 타입
```

      ⚠️ any타입을 사용하면 런타임 오류를 방지하지 못함


### 제너릭

제네릭 함수는 <br>
두루두루 모든 타입의 값을 다 적용할 수 있는 그런 범용적인 함수이다 정도로 이해할 수 있음

1. 2개의 변수 타입 활용

```typescript
function swap<T, U>(a: T, b: U) {
  return [b, a];
}

const [a, b] = swap("1", 2);
````

2. 다양한 배열 타입을 인수로 받는 경우

```typescript
function returnFirstValue<T>(data: T[]) {
  return data[0];
}

let num = returnFirstValue([0, 1, 2]);
// number

let str = returnFirstValue([1, "hello", "mynameis"]);
// number | string
```

3. 타입 변수를 제한
```typescript
function getLength<T extends { length: number }>(data: T) {
  return data.length;
}

getLength("123");            // ✅

getLength([1, 2, 3]);        // ✅

getLength({ length: 1 });    // ✅

getLength(undefined);        // ❌

getLength(null);             // ❌
```


### 제너릭 클래스 만들기

제네릭 클래스를 사용해 여러 타입의 리스트를 생성할 수 있는 범용적을 클래스를 정의함

```typescript
class List<T> {
  constructor(private list: T[]) {}

  push(data: T) {
    this.list.push(data);
  }

  pop() {
    return this.list.pop();
  }

  print() {
    console.log(this.list);
  }
}

const numberList = new List([1, 2, 3]);
const stringList = new List(["1", "2"]);
```


> ## 타입 조작하기

기본 타입이나 별칭 또는 인터페이스로 만든 원래 존재하던 타입들을 <br>
상황에 따라 유동적으로 다른 타입으로 변환하는 타입스크립트의 강력하고도 독특한 기능

### 인덱스 액세스 타입

인덱스를 이용해 다른 타입내의 특정 프로퍼티의 타입을 추출하는 타입

```typescript
interface Post {
  title: string;
  content: string;
  author: {
    id: number;
    name: string;
    age: number; // 추가
  };
}


function printAuthorInfo(author: Post["author"]) { // 인덱스 액세스
  console.log(`${author.id} - ${author.name}`);
}

(...)
````

✚ 인덱스 중첩 사용
```typescript
interface Post {
  title: string;
  content: string;
  author: {
    id: number;
    name: string;
    age: number;
  };
}


function printAuthorInfo(author: Post["author"]['id']) {
	// author 매개변수의 타입은 number 타입이 됨
  console.log(`${author.id} - ${author.name}`);
}
````


### keyof연산자

```typescript
interface Person {
  name: string;
  age: number;
  location: string; // 추가
}

function getPropertyKey(person: Person, key: keyof Person) {
  return person[key];
}

const person: Person = {
  name: "이정환",
  age: 27,
};
````
keyof 연산자는 위와 같이 keyof 타입 형태로 사용하며<br>
타입의 모든 프로퍼티 key를 String Literal Union 타입으로 추출 <br> 
따라서 keyof Person의 결과값은 “name” | “age” | “location”이 됩니다.

      ⚠️  keyof 연산자는 오직 타입에만 적용할 수 있는 연산자 라는 점

따라서 아래의 코드는 오류가 발생한다

```typescript
(...)

function getPropertyKey(person: Person, key: keyof person) { // ❌
  return person[key];
}

const person: Person = {
  name: "이정환",
  age: 27,
};
```

☝️ typeof 연산자 이용하기

typeof 연산자는 자바스크립트에서 특정 값의 타입을 문자열로 반환하는 연산자<br> 
그러나 타입을 정의할 때 사용하면 특정 변수의 타입을 추론하는 기능으로 사용 가능


```typescript
(...)

function getPropertyKey(person: Person, key: keyof typeof person) {
  return person[key];
}

const person: Person = {
  name: "이정환",
  age: 27,
};
````

### 맵드 타입
기존의 객체 타입을 기반으로 새로운 객체 타입을 만드는 타입조작 기능

```typescript
 interface User {
  id: number;
  name: string;
  age: number;
}

type PartialUser = {
  [key in "id" | "name" | "age"]?: User[key];
};

(...)
````

      [key in “id” | “name” | “age”] 는 이 객체 타입은 key가 한번은 id, 한번은 name, 한번은 age가 된다는 뜻 입니다. 따라서 다음과 같이 3개의 프로퍼티를 갖는 객체 타입으로 정의됩니다.
      key가 “id” 일 때 → id : User[id] → id : number
      key가 “name”일 때 → name : User[user] → name : string
      key가 “age”일 때 → age : User[age] → age : number
    여기에 대 괄호 뒤에 선택적 프로퍼티를 의미하는 물음표(?) 키워드가 붙어있으므로 모든 프로퍼티가 선택적 프로퍼티가 되어 결론적으로 이 타입은 다음과 같은 타입이 됩니다


### 템플릿 리터럴 타입

```typescript
type Color = "red" | "black" | "green";
type Animal = "dog" | "cat" | "chicken";

type ColoredAnimal = `red-dog` | 'red-cat' | 'red-chicken' | 'black-dog' ... ;
```

위 코드를 템플릿 리터럴을 이용하여 아래와 같이 만들 수 있음
```typescript
type ColoredAnimal = `${Color}-${Animal}`;
````


> ## 조건부 타입

### 제너릭 조건부 타입

```typescript
type StringNumberSwitch<T> = T extends number ? string : number;

let varA: StringNumberSwitch<number>;
// string

let varB: StringNumberSwitch<string>;
// number
```