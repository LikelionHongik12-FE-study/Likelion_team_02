함수 -> 어떤 매개변수를 받고, 어떤 결과값을 반환하는지 파악하기
TS에서의 함수 -> 어떤 [타입의] 매개변수를 받고, 어떤 [타입의] 결과값을 반환하는지 파악하기

function func(a: number, b: number) {
  return a + b;
}
-------
화살표 함수의 타입을 정리하는 방법
const add = (a: number, b: number) => a + b;
-------
선택적 매개변수
function introduce(name = '이정환', tall?: number) {
  console.log(`name : ${name}`);
}

*if (typeof tall == "number") {} 로 감싸주면 tall에 빨간줄이 사라진다.
function introduce(name = '이정환', tall: number) {
  console.log(`name : ${name}`);
  console.log(`tall : ${tall + 10}`);
}
-------
...rest란?

function getSum(...rest) {

}
이 있을 때

getSum(1, 2, 3)이나
getSum(1, 2, 3, 4, 5) 같이 가변적인 길이의 매개변수를 받아서 본인 내에 [1, 2, 3] 이런 식으로 저장하는 기능이 ...rest다.

function getSum(...rest: number[]) {
  let sum = 0;
  rest.forEach((it) => (sum += it));

  return sum;
}

*참고
function getSum(...rest) { // Rest 매개 변수 'rest'에는 암묵적으로 'any[]' 형식이 포함된다.

}







함수 타입 표현식
const add = (a: number, b: number): number => a + b; 이거를

type Operation = (a: number, b:number) => number;
const add: Operation = (a, b) => a + b;

이렇게 일반화 느낌으로 할 수 있다는 것.

const add: Operation = (a, b) => a + b;
const sub: Operation = (a, b) => a - b;
...
이런 거에 범용적으로 가져다 쓸 수 있는 장점이 있다. 함수마다 하나하나 귀찮게 :number, :number 붙일 거 없이.

*참고
const add: Operation = (a, b) => a + b; 대신
type Operation = (a: number, b:number) => number; 의 오른쪽을 가져다 쓸 수 있다.
const add: (a: number, b:number) => number = (a, b) => a + b; 이렇게.
-------
호출 시그니처(콜 시그니처) 방식도 있다.

type Operation2 = {
  (a: number, b: number): number;
};

이런 형식.

*왜 객체 형식을 지니고 있느냐면 엄밀히 함수도 객체이기 때문이다.







이 파트는 어려운데 중요한 내용은 아니다.
함수 타입의 호환성 : 특정 함수 타입을 다른 함수 타입으로 취급해도 괜찮은가를 판단하는 것.
1. 반환값의 타입이 호환되는가
2. 매개변수의 타입이 호환되는가

기준1. 반환값이 호환되는가

type A = () => number;
type B = () => 10;

let a: A = () => 10; A는 number 반환
let b: B = () => 10; B는 number 리터럴 반환

a = b;
b = a; 이 말은 즉슨 반환값 타입을 보면, 타입 A를 타입 B로 취급하겠다는 말이다. 즉 number 타입을 number 리터럴 타입으로 취급하겠다는 것이다. 다운캐스팅이다. 그래서 안 되는 것이다. 반환값을 기준으로 다운캐스팅이라서.

기준2. 매개변수가 호환되는가

2-1. 매개변수의 개수가 같을 때

type C = (value: number) => void;
type D = (value: 10) => void;

let c: C = (value) => {};
let d: D = (value) => {};

c = d; D 타입을 C 타입으로 보겠다는 것. 업캐스팅인데 안 되네. 매개변수를 기준으로 판단할 때는 아까와 반대로 업캐스팅일 때 안 된다.
d = c;







2-2. 매개변수의 개수가 다를 때

-------
type Func1 = (a: number, b: number) => void;
type Func2 = (a: number) => void;

let func1: Func1 = (a, b) => {};
let func2: Func2 = (a) => {};

func1 = func2;
func2 = func1; -> 오류. Func1을 Func2로 보겠다는 거잖아. 그런데 Func1은 매개변수가 2개짜리인데 Func2는 1개니까 안 되는 거다.
-------
type Animal = {
  name: string;
}

type Dog = {
  name: string;
  color: string;
}

let animalFunc = (animal: Animal) => {
  console.log(animal.name);
}

let dogFunc = (dog: Dog) => {
  console.log(dog.name);
  console.log(dog.color);
};

animalFunc = dogFunc;
-> 이는 마치 이런 함수를 만드는 거다.
let testFunc = (animal: Animal) => {
  console.log(animal.name);
  console.log(animal.color);
}

이걸 보면 이해가 좀 될 거다.







함수 오버로딩 : 함수를 매개변수나 타입에 따라 여러가지 버전으로 정의하는 방법

1. 오버로드 시그니처 써주기(선언부인가?)
function func(a: number): void;
function func(a: number, b: number, c: number): void;

2. 구현 시그니처 작성하기(구현부)
function func(a: number, b?: number, c?: number) {
  if (typeof b === "number" && typeof c === "number") {
    console.log(a + b + c);
  }
  else {
    console.log(a * 20);
  }
}

func(1);
func(1, 2, 3);
이제 이 둘이 구별이 된다.

function func(a: number, b: number, c: number) {
  ~~~
}
이렇게 하는게 아니라 앞에 보여줬듯이 ?를 사용해서 하는 거다.







사용자 정의 타입 가드
 type Dog = {
  name: string;
  isBark: boolean;
 };

 type Cat = {
  name: string;
  isScratch: boolean;
 }

 type Animal = Dog | Cat;

 function warning(animal: Animal) {
  if ("isBark" in animal) {
    // 강아지
  }
  else if ("isScratch" in animal) {
    // 고양이
  }
 }

 가능하면 이렇게 코딩하지 말라고 했으니
 type Animal = Dog | Cat;
 이 뒷부분을

 function isDog(animal: Animal): animal is Dog {
  return (animal as Dog).isBark !== undefined; // 이 문장이 참이라 true 값 반환일 때면 animal is Dog를 반환하라는 것. 왜인지 : animal is Dog를 안 써주면 작동이 안 된다네.
 }

 function isCat(animal: Animal): animal is Cat {
  return (animal as Cat).isScratch !== undefined;
 }

 function warning(animal: Animal) {
  if (isDog(animal)) {
    // 강아지
  }
  else if (isCat(animal)) {
    // 고양이
  }
 }







섹션6. 인터페이스 : 타입에 이름을 지어주는 또 다른 문법

type A = {
  a: string;
  b: number;
}

interface A {
  a: string;
  b: number;
}

즉, 타입 별칭과 마찬가지로
const person: A = {
  name: "강민석",
  age: 27,
};
이렇게 사용할 수 있게끔 해주는 것이다.

interface는 타입 별칭에서는 제공하지 않는 상속이나 합침 등의 객체 타입을 다루는 기능을 제공해준다.

interface A {
  a: string;
  b?: number;
}
이렇게 선택적 프로퍼티로 설정해주는 것도 가능하고

interface A {
  readonly a: string;
  b: number;
}
를 해서
후에 person.name = "홍길동";
실행이 안 되도록 할 수도 있다.

const person: Person = {
  name: "강민석",
  sayHi: function () {
    console.log("Hi");
  },
};
이런 식으로 쓰고 싶다면
앞에 interface를
interface Person {
  readonly name: string;
  age?: number;
  sayHi: () => void;
}
로 해주면 된다.
다만 그냥 (): void;만 쓰면 안 되고 이름을 붙여줘야 하지.

만약 메서드의 오버로딩을 구현하고 싶으면
interface Person {
  readonly name: string;
  age?: number;
  sayHi: () => void;
  sayHi: (a:number, b:number) => void;
}
가 아닌
interface Person {
  readonly name: string;
  age?: number;
  sayHi(): void;
  sayHi(a:number, b:number): void;
}
이렇게 써야 오류가 안 난다.
-------
interface는
type Type1 = number | string | Person;
type Type2 = number & string & Person;
같은 게 안 된다.
저런 걸 하고 싶으면 타입 별칭 방식을 써야 한다.
-------
interface는
interface IPerson {
~~~
를 쓰는 사람들도 있다. 대문자 I로 시작하는 사람들이 있댄다.







타입 별칭과 달리 인터페이스에서만 사용 가능한 기능

interface Animal {
  name: string;
  color: string;
}

interface Dog {
  name: string;
  color: string;
  isBark: boolean;
}

Dog에 귀찮게 name, color를 다시금 써줘야 했다.

이때 extends, 즉 확장 또는 상속 기능을 아래와 같이 써주면 된다.

interface Dog extends Animal {
  isBark: boolean;
}
이렇게.

이제 예시로 Dog 객체를 하나 만들어 보면
const dog: Dog = {
  name: "",
  color: "",
  isBark: true,
};
이렇게 할 수 있다.
-------
만약 기존 interface의 항목을 수정하고 싶다면
interface Dog extends Animal {
  name: "Hello";
  isBark: boolean;
}
이런식으로 해주면 된다.

다만 "Hello"는 기존에 써져있던 string의 서브 타입이라 가능했던 것이란 점.
interface Dog extends Animal {
  name: number;
  isBark: boolean;
}
이런 식으로 하면 오류가 난다.
-------
다중 확장(상속)도 있다.

예를 들어 Dog 말고도
interface Cat extends Animal {
  isScratch: boolean;
}
이 있다고 하면

interface DogCat extends Dog, Cat {}
이 가능하다.

이 interface 객체를 만들어보면
const dogCat: DogCat = {
  name: "",
  color: "",
  isBark: true,
  isScratch: true,
};
이렇게 가능해진다.







선언 합침

type Person = {
  name: string;
}

type Person = {
  age: number;
}

타입 별칭은 중복이 불가능하지만

interface Person {
  name: string;
}

interface Person {
  age: number;
}

인터페이스는 가능하다.

interface Person {
  name: string;
  age: number;
}

같이 선언이 알아서 합쳐지는 방식이기 때문이다.

다만
interface Person {
  name: string;
}

interface Person {
  name: "hello";
  age: number;
}
이런 식으로는 하면 오류가 난다.
최소한 안의 같은 거는 같게 써줘야 한다.
여기서는 name: string이라고 똑같이 해줘야 한다.

interface Developer extends Person {
  name: "hello";
}
이것과는 아예 다른 개념이다.

선언 합침 기능이 왜 있냐?

interface Lib {
  a: number;
  b: number;
}

const lib: Lib = {
  a: 1,
  b: 2,
};

이렇게 있다고 치자.

const lib: Lib = {
  a: 1,
  b: 2,
  c: "hello",
};
로 하나 추가해주고 싶으면

interface Lib {
  a: number;
  b: number;
}

밑에

interface Lib {
  c: string;
}

이렇게 하나 더 써주면 되는 거다.
이걸 모듈 보강이라고 한다.




자바스크립트 클래스

let studentA = {
  name: "이정환",
  grade: "A+",
  age: 27,
  study() {
    console.log("열심히 공부함");
  },
  introduce() {
    console.log("안녕하세요!");
  },
};

let studentB = {
  name: "홍길동",
  grade: "B+",
  age: 27,
  study() {
    console.log("열심히 공부함");
  },
  introduce() {
    console.log("안녕하세요!");
  },
};

이렇게 100명 다 써주기는 힘들잖아. 붕어빵 틀을 만들자. 즉, 클래스를 만들자.

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
    console.log("열심히 공부함");
  }

  introduce() {
    console.log("안녕하세요!");
  }
}

// 클래스를 이용해서 만든 객체 : 인스턴스
// 스튜던트 인스턴스를 만들어보자.
let studentB = new Student("이정환", "A+", 27);
console.log(studentB); // 이건 Student { name: '이정환', grade: 'A+', age: 27 }이 출력되네.
studentB.study();
studentB.introduce();
-------
  introduce() {
    console.log("안녕하세요!");
  }
이거를
  introduce() {
    console.log(`안녕하세요 ${this.name}입니다!`);
  }
로 해도 좋겠지. this를 이용해서.
-------
상속(확장) 예시

class StudentDeveloper extends Student {
  favoriteSkill;

  constructor(name, grade, age, favoriteSkill) {
    super(name, grade, age);
    this.favoriteSkill = favoriteSkill;
  }

  programming() {
    console.log(`${this.favoriteSkill}로 프로그래밍함`);
  }
}







const employee = {
  name: "이",
  age: 27,
  position: "developer",
  work() {
    console.log("일함");
  },
};
라는 객체를 만들고 싶으면

class Employee {
  // 필드
  name: string; // 타입 안 써주면 any로 된다. any는 지양하자고 했다.
  age: number;
  position: string;
}
이 코드는 빨간줄이 뜨는데 이를 해결하려면

class Employee {
  name?: string;
  age?: number;
  position?: string;
}
이렇게 ?를 사용하든지

class Employee {
  name: string = "";
  age: number = 0;
  position: string = "";
}
처럼 기본값을 주어야 한다.

class Employee {
  name?: string;
  age?: number;
  position?: string;

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
아니면 생성자를 만들어주든지.
-------
객체 생성은
const employeeB = new Employee("김", 27, "개발자");
로 해도 되고

const employeeC: Employee = {
  name: "",
  age: 0,
  position: "",
  work() {},
};
이렇게 해도 된다. 마치 타입 마냥 생각 하면 된다 클래스를.
-------
상속(확장), 즉 파생클래스 만들 때는
생성자 안에 super(부모 클래스의 속성값들); 쓰는 거 까먹지 말고.







class Employee {
  // 필드
  private name: string;
  protected age: number;
  public position: string;

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

name은 private이니까 코드 뒤에서
const employee = new Employee("이", 27, "developer");
employee.name = "홍"; 같은게 안 된다. 알고있잖아 C++ 덕에.

Employee 클래스를 상속받은 어떤 클래스에 메서드가 하나 있다고 하자.
func() {
  this.age;
  this.name;
}
을 하면 age는 protected라서 파생 클래스에서 사용 가능하지만 name은 여지 없다.







인터페이스와 클래스 함께 사용하기

interface CharacterInterface {
  name: string;
  moveSpeed: number;
  move(): void;
}

class Character implements CharacterInterface {
  name: string;
  moveSpeed: number;

  constructor(name: string, moveSpeed: number) {
    this.name = name;
    this.moveSpeed = moveSpeed;
  }

  move(): void {
    console.log(`${this.moveSpeed} 속도로 이동!`);
  }
}
이런 식으로 앞에다 인터페이스로 미리 알려두고 클래스를 짜는? 방식도 있댄다.
그렇구나 하고 넘기자.





제네릭 함수

function func<T>(value: T): T {
  return value;
}
이렇게 해주면

let num = func(10);
let bool = func(true);
let str = func("string");
이렇게 했을 때

num.toUpperCase();
같은게 문제없이 잘 된다.
-------
let arr = func<[number, number, number]>([1, 2, 3, 4]);
이렇게 명시적으로 해줄 수도 있다.

let arr = func([1, 2, 3] as [number, number, number]);
이렇게 단언으로 해줄 수도 있겠지만.
