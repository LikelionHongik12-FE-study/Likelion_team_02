# 타입스크립트 seciton 5~9 정리

## Section 5. 함수 타입

```ts
function func(a: number, b: number): number {
  return a + b;
}
// 화살표함수
const add = (a: number, b: number): number => a + b;
```

타입스크립트에서는 매개변수의 타입과 반환 값의 타입으로 함수를 소개할 수 있다.  
반환값 타입은 생략가능

> 매개변수에 기본값이 설정되어있으면 타입이 자동으로 추론되므로 매개변수의 타입도 생략 가능

### 선택적 매개변수

```ts
function introduce(name = "이정환", tall?: number) {
  console.log(`name : ${name}`);
  console.log(`tall : ${tall}`);
}

introduce("이정환", 156);
// 매개변수 tall 생략
introduce("이정환");
```

매개변수의 이름 뒤에 물음표(?)를 붙여주면 선택적 매개변수 -> 함수 사용 시 해당 매개변수는 생략가능

> 선택적 매개변수는 undefined와 유니온 된 타입으로 추론 된다.

> 선택적 배개변수는 필수 매개변수의 앞에 위치할 수 없다.

```ts
function getSum(...rest: number[]) {
  let sum = 0;
  rest.forEach((it) => (sum += it));
  return sum;
}
```

자바스크립트처럼 rest파라미터로 여러개의 매개변수를 받을 수 있지만, 배열의 타입을 명시해야 한다.

### 함수 타입 표현식과 호출 시그니처

> 둘은 동일한 기능을 한다

### 함수 타입 표현식

```ts
type Operation = (a: number, b: number) => number;

const add: Operation = (a, b) => a + b;
const sub: Operation = (a, b) => a - b;
const multiply: Operation = (a, b) => a * b;
const divide: Operation = (a, b) => a / b;
```

변수 Operation의 타입을 함수 타입 표현식으로 정의한 함수 타입으로 정의

### 호출 시그니처

```ts
type Operation2 = {
  (a: number, b: number): number;
};

const add2: Operation2 = (a, b) => a + b;
const sub2: Operation2 = (a, b) => a - b;
const multiply2: Operation2 = (a, b) => a * b;
const divide2: Operation2 = (a, b) => a / b;
```

이런 방식으로도 함수의 타입 정의 가능

> 호출 시그니처 아래에 프로퍼티를 추가 정의하는 것도 가능. 이렇게 할 경우 함수이자 일반 객체를 의미하는 하이브리드 타입.

### 함수 타입의 호환성이란?

특정 함수 타입을 다른 함수 타입으로 마땅한지 판단하는 것

> 판단 기준 1. 두 함수의 반환값 타입이 호환되는가?  
> 판단 기준 2. 두 함수의 매개변수의 타입이 호환되는가?

### 기준 1: 반환값 타입의 호환

```ts
type A = () => number;
type B = () => 10;

let a: A = () => 10;
let b: B = () => 10;

a = b; // ✅
b = a; // ❌
```

반환값 타입은 다운캐스팅 불가

### 기준 2: 매개변수의 타입이 호환

2-1. 매개변수 개수 동일

```ts
type C = (value: number) => void;
type D = (value: 10) => void;

let c: C = (value) => {};
let d: D = (value) => {};

c = d; // ❌
d = c; // ✅
```

매개변수는 업캐스팅 불가 (C매개변수의 타입이 D매개변수 타입의 서브 타입 시 가능)

2-2. 매개변수의 개수 다를 때

```ts
type Func1 = (a: number, b: number) => void;
type Func2 = (a: number) => void;

let func1: Func1 = (a, b) => {};
let func2: Func2 = (a) => {};

func1 = func2; // ✅
func2 = func1; // ❌
```

### 함수 오버로딩

하나의 함수(이름 동일)를 매개변수의 개수나 타입에 따라 여러 버전으로 나타내는 것

```ts
// 버전들(함수 선언부) -> 오버로드 시그니쳐
function func(a: number): void;
function func(a: number, b: number, c: number): void;

// 실제 구현부 -> 구현 시그니쳐
function func(a: number, b?: number, c?: number) {
  if (typeof b === "number" && typeof c === "number") {
    console.log(a + b + c);
  } else {
    console.log(a * 20);
  }
}

func(1); // ✅ 버전 1 - 오버로드 시그니쳐
func(1, 2); // ❌
func(1, 2, 3); // ✅ 버전 3 - 오버로드 시그니쳐
```

모든 오버로드 시그니처와 호환되도록 만들어야 한다. 따라서 위 매개변수 b, c는 선택적 매개변수로 만들어야 한다.

### 사용자 정의 타입 가드

```ts
type Dog = {
  name: string;
  isBark: boolean;
};

type Cat = {
  name: string;
  isScratch: boolean;
};

type Animal = Dog | Cat;

// Dog 타입인지 확인하는 타입 가드
function isDog(animal: Animal): animal is Dog {
  return (animal as Dog).isBark !== undefined;
}

// Cat 타입인지 확인하는 타입가드
function isCat(animal: Animal): animal is Cat {
  return (animal as Cat).isScratch !== undefined;
}

/**
 * function warning(animal: Animal) {
  if ("isBark" in animal) {
    console.log(animal.isBark ? "짖습니다" : "안짖어요");
  } else if ("isScratch" in animal) {
    console.log(animal.isScratch ? "할큅니다" : "안할퀴어요");
  }
} */

function warning(animal: Animal) {
  if (isDog(animal)) {
    console.log(animal.isBark ? "짖습니다" : "안짖어요");
  } else {
    console.log(animal.isScratch ? "할큅니다" : "안할퀴어요");
  }
}
```

타입가드로 동작하는 함수를 직접 정의하여 사용함으로써 in 연산자의 사용을 피할 수 있다.

> in 연산자는 타입의 프로퍼티가 수정되는 경우 타입 가드로 작동이 어려워지기 때문에 사용 X

## Section 6. 인터페이스

### 인터페이스란

타입 별칭과 동일하게 타입에 이름을 지어주는 문법이다.

```ts
interface Person {
  name: string;
  age: number;
}

const person: Person = {
  name: "이정환",
  age: 27,
};
```

- 선택적 프로퍼티 가능
- 읽기 전용 프로퍼티 가능
- 메서드 타입 정의 가능
- 메서도 오버로딩 (함수 타입 표현식으로는 불가능, 호출 시그니처로는 가능)
- 하이브리드 타입 가능
  > 인터페이스끼리의 Union, Intersection 불가능

### 인터페이스 확장

하나의 인터페이스를 다른 인터페이스에서 상속받아 중복된 프로퍼티를 정의하지 않도록 도와주는 문법

```ts
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

const dog: Dog = {
  name: "돌돌이",
  color: "brown",
  breed: "진도",
};
```

"interface 타입이름 extends 확장*할*타입이름 형태"로 extends 뒤에 확장할 타입의 이름을 정의하면 해당 타입에 정의된 모든 프로퍼티를 다 가지고 오게 된다.

- 프로퍼티를 재정의 하는 것이 가능하다. (서브타입으로만 가능)
- 타입 별칭으로 선언된 타입을 확장하는 것도 가능하다.
- 여러개의 인터페이스를 확장하는 것도 가능하다.

```ts
// 여러개 인터페이스 확장
interface DogCat extends Dog, Cat {}

const dogCat: DogCat = {
  name: "",
  color: "",
  breed: "",
  isScratch: true,
};
```

### 선언 합침

동일한 이름으로 선언된 인터페이스는 하나로 합쳐지게 된다.

```ts
interface Person {
  name: string;
}

interface Person {
  age: number;
}

const person: Person = {
  name: "이정환",
  age: 27,
};
```

> 동일한 이름의 프로퍼티를 서로 다른 타입으로 정의하면 오류 발생 (충돌)

## Section 7. 클래스

### 자바스크립트에서의 클래스

```ts
class Student {
  // 필드
  name;
  grade;
  age;

  // 생성자
  constructor(name, grade, age) {
    this.name = name;
    this.grade = grade;
    this.age = age;
  }

  // 메서드
  study() {
    console.log("열심히 공부 함");
  }

  introduce() {
    // this 객체 사용 메서드
    console.log(`안녕하세요 ${this.name} 입니다!`);
  }
}

// 인스턴스(객체) 생성
let studentB = new Student("홍길동", "A+", 27);

studentB.study(); // 열심히 공부 함
studentB.introduce(); // 안녕하세요 홍길동 입니다!
```
클래스는 동일한 모양의 객체를 더 쉽게 생성하도록 도와주는 문법이다.
- 클래스로 만든 객체를 인스턴스라고 한다.
- 객체가 갖는 프로퍼티를 클래스의 필드라고 한다.
- 생성자는 메서드이며, 객체를 생성하는 함수 역할을 한다.
- this 객체는 현재 객체를 가리키며, 이를 이용해 객체의 프로퍼티 값을 가져올 수 있다.
### 상속
클래스는 상속이 가능하다.
```ts
class StudentDeveloper extends Student {
  // 필드
  favoriteSkill;

  // 생성자
  constructor(name, grade, age, favoriteSkill) {
    super(name, grade, age);
    this.favoriteSkill = favoriteSkill;
  }

  // 메서드
  programming() {
    console.log(`${this.favoriteSkill}로 프로그래밍 함`);
  }
}
```
> 상속받는 클래스의 생성자 내에서는 super라는 메서드를 호출해야 한다. super 함수는 슈퍼 클래스(상속하는 클래스)의 생성자를 호출하는 역할을 한다.
### 타입스크립트의 클래스
```ts
class Employee {
  // 필드
  name: string = "";
  age: number = 0;
  position: string = "";

  // 생성자
  constructor(name: string, age: number, position: string) {
    this.name = name;
    this.age = age;
    this.position = position;
  }

  // 메서드
  work() {
    console.log("일함");
  }
}
```
- 클래스의 필드를 선언할 때 타입을 함께 정의해야 한다. (미지정 시 any 타입으로 간주 -> 오류 발생 가능)  
- 필드 선언 시 초기값을 명시하거나 생성자에서 초기화 가능하다.
- 선택적 프로퍼티 가능
- 클래스는 타입으로도 사용 가능하다.
- js에서와 마찬가지로 상속 가능
### 접근 제어자
클래스의 특정 필드나 메서드를 접근할 수 있는 범위를 설정하는 기능
```ts
class Employee {
  // 필드
  private name: string; // private 접근 제어자 설정
  protected age: number;
  public position: string;

  ...

  // 메서드
  work() {
    console.log(`${this.name}이 일함`); // 여기서는 접근 가능
  }
}

class ExecutiveOfficer extends Employee {
 // 메서드
  func() {
    this.name; // ❌ 오류 
    this.age; // ✅ 가능
  }
}

const employee = new Employee("이정환", 27, "devloper");

employee.name = "홍길동"; // ❌ 오류
employee.age = 30; // ❌ 오류
employee.position = "디자이너";
```
- public: 어디서든지 접근 가능
- private: 클래스 내부에서만 접근 가능
- protected: 클래스 내부와 파생 클래스에서 접근 가능
```ts
class Employee {
  // 생성자
  constructor(
    private name: string,
    protected age: number,
    public position: string
  ) {}

  // 메서드
  work() {
    console.log(`${this.name} 일함`);
  }
}
```
> 이와 같이 접근 제어자는 생성자의 매개변수에서 설정 가능하며 필드 선언, 생성자 함수 내의 변수 값 할당 과정은 생략이 가능하다.  
### 인터페이스를 구현하는 클래스
인터페이스가 클래스의 설계도 역할을 할 수 있다.
```ts
/**
 * 인터페이스와 클래스
 */

interface CharacterInterface {
  name: string;
  moveSpeed: number;
  move(): void;
}

// implements 구문 사용
class Character implements CharacterInterface {
  constructor(
    public name: string,
    public moveSpeed: number,
    private extra: string
  ) {}

  move(): void {
    console.log(`${this.moveSpeed} 속도로 이동!`);
  }
}
```
## Section 8. 제네릭
제네릭이란 함수나 인터페이스, 타입 별칭, 클래스 등을 다양한 타입과 함께 동작하도록 만들어 주는 기능. 타입 변수를 사용함으로써 가능함.
### 제네릭 함수
```ts
function func<T>(value: T): T {
  return value;
}

let num = func(10);
// number 타입
```
모든 타입의 값을 다 적용할 수 있는 범용적인 함수
> 제네릭의 뜻은 '일반적인', '포괄적인'
### 타입 변수 응용
1. 2개의 타입 변수
```ts
function swap<T, U>(a: T, b: U) {
  return [b, a];
}

const [a, b] = swap("1", 2);
```
2. 배열 타입을 인수로 받는 제네릭 함수
```ts
function returnFirstValue<T>(data: T[]) {
  return data[0];
}

let num = returnFirstValue([0, 1, 2]);
// number

let str = returnFirstValue([1, "hello", "mynameis"]);
// number | string
```
3. 반환값의 타입을 배열의 첫번째 요소의 타입이 되도록
```ts
function returnFirstValue<T>(data: [T, ...unknown[]]) {
  return data[0];
}

let str = returnFirstValue([1, "hello", "mynameis"]);
// number
```
4. 타입 변수를 제한
```ts
function getLength<T extends { length: number }>(data: T) {
  return data.length;
}

getLength("123");            // ✅

getLength([1, 2, 3]);        // ✅

getLength({ length: 1 });    // ✅

getLength(undefined);        // ❌

getLength(null);             // ❌
```
### Map 메서드 타입 정의하기
map 함수를 직접 구현할 수 있다.
```ts
const arr = [1, 2, 3];

function map<T, U>(arr: T[], callback: (item: T) => U): U[] {
  let result = [];
  for (let i = 0; i < arr.length; i++) {
    result.push(callback(arr[i]));
  }
  return result;
}

map(arr, (it) => it.toString());
// string[] 타입의 배열을 반환
// 결과 : ["1", "2", "3"]
```
### ForEach 메서드 타입 정의하기
forEach 함수를 직접 구현할 수 있다.
```ts
function forEach<T>(arr: T[], callback: (item: T) => void) {
  for (let i = 0; i < arr.length; i++) {
    callback(arr[i]);
  }
}
```
### 제네릭 인터페이스
제네릭은 인터페이스에도 적용 가능하다.
```ts
interface KeyPair<K, V> {
  key: K;
  value: V;
}
let keyPair: KeyPair<string, number> = {
  key: "key",
  value: 0,
};

let keyPair2: KeyPair<boolean, string[]> = {
  key: true,
  value: ["1"],
};
```
> 제네릭 인터페이스는 제네릭 함수와는 달리 변수의 타입으로 정의할 때 반드시 꺽쇠와 함께 타입 변수에 할당할 타입을 명시해주어야 한다.
```ts
interface Map<V> {
  [key: string]: V;
}

let stringMap: Map<string> = {
  key: "value",
};

let booleanMap: Map<boolean> = {
  key: true,
};
```
인덱스 시그니쳐와 함께 사용 가능하다.
```ts
type Map2<V> = {
  [key: string]: V;
};

let stringMap2: Map2<string> = {
  key: "string",
};
```
타입 별칭에도 제네릭을 적용할 수 있다.
### 제네릭 클래스
```ts
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
클래스의 이름 뒤에 타입 변수를 선언하면 제네릭 클래스가 된다.
### Promise 사용하기
Promise는 제네릭 클래스로 구현되어 있다. 따라서 새로운 Promise를 생성할 때 타입 변수에 할당할 타입을 직접 설정해 주면 해당 타입이 resolve 결과값의 타입이 된다.
```ts
const promise = new Promise<number>((resolve, reject) => {
  setTimeout(() => {
    // 결과값 : 20
    resolve(20);
  }, 3000);
});

promise.then((response) => {
  // response는 number 타입
  console.log(response);
});

promise.catch((error) => {
  if (typeof error === "string") {
    console.log(error);
  }
});
```
reject 함수에 인수로 전달하는 값 즉 실패의 결과값 타입은 정의할 수 없다.
## Section 9. 타입 조작하기
