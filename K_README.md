vscode에서 section1 폴더를 열고
터미널을 켜서 npm init을 하고
그냥 엔터키 쭉 쳐서 package.json 파일을 생성하네.
npm i @types/node를 하는데
이걸 해야지 내가 console.log를 찍든 JS를 쓰는 걸 알아먹나보다.
npm i typescript -g를 하면
앞으로 내 컴퓨터에서 타입스크립트를 쓸 수 있게 되나봐.
버전 체크는 tsc -v라네.
section1 폴더 아래 src 폴더를 만들고 그 아래 index.ts 파일 생성하고
console.log("Hello TypeScript");
const a:number = 1;
쓰기.
아까 설치한 tsc가 컴파일러라네.
tsc src/index.ts를 하니까 src 안에 index.js가 생기는데
이게 index.ts를 컴파일한 결과고
console.log("Hello TypeScript");
var a = 1;
내용은 이거다.
참고로 index.ts를 저장하지 않고 tsc하니까 index.js 내용도 비어있네 ㅋㅋ.
node src index.js 하니까
Hello TypeScript가 터미널에 써지네.
npm install ts-node -g를 하니까
아까처럼 tsc 하고서 node를 할 필요없이
ts-node src/index.ts 코드 하나로
Hello TypeScript 써지는 게 되네.

컴파일러 옵션 설정하기 :
tsc --init 하면 tsconfig.json이 생성된다.
tsconfig.json 내용 다 지우고 {} 남기고 하시네.
그 안에
{
  "include": ["src"]
}
를 쓰고, 터미널에 tsc만 입력해주면 include 해준 src 폴더 내 모든 파일이
컴파일 되는 거래.
{
  "compilerOptions": {
    "target": "ES5"
  },
  "include": ["src"]
}
를 쓰고 index.ts의 내용을 화살표 함수인
const func = () => console.log("Hello");
로 바꾸면 ES5에는 화살표 함수가 없었으므로
index.js의 내용이 화살표 함수가 아닌
var func = function () {
  return console.log("Hello");
};
로 나오게 된다.
자바스크립트 최신 버전을 의미하는 ESNext를 이용하면
const func = () => console.log("Hello");
로 잘 나온다.
include 옵션, target 옵션을 배워봤다.
target 옵션은 compilerOptions 안에 넣어서 쓰는구나
더 상세한 거라고 보는가보네.
tsconfig.js를
{
  "compilerOptions": {
    "target": "ES5",
    "module": "CommonJS"
  },
  "include": ["src"]
}
로 바꾸고
hello.ts를 하나 만들어서
export const hello = () => {
  console.log("hello");
};
로 써주고
index.js의 내용을
import { hello } from "./hello";

hello();
로 해주면
tsc를 했을 때 index.js의 내용이
"use strict"
...
로 독특하게 컴파일 된다
requiresk export처럼 ES Module System이 아니라
Common.js Module System으로 된 거다. 설정을 그렇게 했으므로.
ESNext로 하시면
import { hello } from "./hello";
hello();
가 된다. 즉, ES 모듈 시스템을 사용하는 자바스크립트 코드로 변환된 거다.
{
  "compilerOptions": {
    "target": "ES5",
    "module": "CommonJS",
    "outDir": "dist"
  },
  "include": ["src"]
}
로 해주면 dist라는 폴더가 src와 같은 레벨에 생성되고
그 dist 폴더 안에 컴파일된 .js 파일들을 생성해준다.
hello.ts를
export const hello = (message) => {
  console.log("hello " + message);
};
로 매개변수를 추가해줬는데
{
  "compilerOptions": {
    "target": "ES5",
    "module": "CommonJS",
    "outDir": "dist",
    "strict": true
  },
  "include": ["src"]
}
로 tsconfig.json으로 바꿔주면
타입검사를 엄격하게 하겠다는 뜻이므로 message에 빨간 줄이 그여진다.
index.ts와 hello.ts 내용을 모두
const a = 1;로 써주면
TS는 모듈을 같은 글로벌 공간으로 생각하기 때문에 겹친다고 오류가 뜬다.
해결은 1. const a = 1; export {}; 같이 빈 것을 import나 export 키워드를
억지로 넣어주는 것. 독립된 모듈로 인식되도록 하는 것.
2. "moduleDetection": "force"를 tsconfig.json에 넣어주는 것.
2번 적용이 안 되면 컨트롤 쉬프트 p -> TS 서버 다시 시작 클릭.
* "module": "ESNext"로 하고 ts-node src/index.ts를 하면 오류가 뜬다.
package.json에 "type": "module"을 써준다.
그리고, tsconfig.json에 가서
{
  "compilerOptions": {
    "target": "ES5",
    "module": "CommonJS",
    "outDir": "dist",
    "strict": false,
    "moduleDetection": "force"
  },
  "ts-node": {
    "esm": true
  },
  "include": ["src"]
}
로 해준다.
왜 그래도 안 되냐 나는..?
24년 이후 수강생은 "skipLibCheck": true를
앞으로 생성하는 모든 tsconfig.json에
{
  "compilerOptions": {
    "target": "ESNext",
    "module": "ESNext",
    "outDir": "dist",
    "strict": false,
    "moduleDetection": "force",
    "skipLibCheck": true
  },
  "ts-node": {
    "esm": true
  },
  "include": ["src"]
}
이렇게 써주래서 써줬는데도 안 되네..
저 skip~ 옵션에 대한 설명 : 
@types 버전이 20버전 이상으로 업데이트되면서 특정 라이브러리에서 타입 검사 오류가 발생하고 있습니다.
따라서 tsconfig.json에 compilerOption 내부에 skibLibCheck 옵션을 추가 해 주시기 바랍니다
( 또 앞으로 생성하게 되는 모든 위 옵션은 타입 정의 파일(.d.ts 확장자를 갖는 파일을 의미합니다 우리 수업에서는 나중에 배웁니다)의 타입 검사를 생략하는 옵션입니다.
보통 타입 정의 파일은 라이브러리에서 사용하는데 가끔 라이브러리의 타입 정의 파일에서 타입 오류가 발생하는 일이 발생할 수 있습니다.
따라서 해당 옵션을 true로 설정하셔서 불 필요한 타입 정의 파일의 타입 검사를 생략하도록 설정해주시기 바랍니다.

section2 폴더 열고 npm init 엔터 엔터 엔터 ... 노드 제이에스 패키지 초기화란다 이게.
let num1: number = 123; 이렇게 쓰는 걸 type annotation이라고 한댄다.
1. 원시자료형
1-1. number 자료형
특이한거는 let num1: number = 123; 뿐 아니라
let num2: number = Infinity;
let num3: number = -Infinity;
let num4: number = NaN; (Not A Number 라네)
같은 거도 된대.
num1 = "hello";
num1.toUpperCase()
는 안 되고
num1.toFixed()는 된대.
1-2. string 타입
"hello", 'hello', `hello`, `hello ${num1}` - 템플릿 리터럴 형식
모두 string.
1-3. boolean
true, false
1-4. null
null
1-5. undefined
undefined.
* 잠깐 빈 값을 넣고 싶은데 TS는 let numA: number = null; 같은게 안 된다
이때 tsconfing.json에서 "strictNullChecks": false를 추가해주면 된다.
null을 엄격하게 검사하지 않겠다는 것이다. 기본으로는 켜져있다.
무슨 소리냐면 더 상위인 "strict" 옵션이 true니까 같이 켜져있던 것.
1-6. 리터럴 타입. 리터럴이란 '값'을 의미한대.
let numA: 10 = 10;이라 하면
이후에 numA = 12;라 했을 때 오류가 뜨겠지.

* 배열
let numArr: number[] = [1, 2, 3];
let strArr: string[] = ["hello", "im", "winterlood"];
let boolArr: Array<boolean> = [true, false, true]; 제네릭 문법 형식이다 이건.
요소의 타입이 다양하다면
let multArr (number | string) [] = [1, "hello"];
다차원 배열은
let doubleArr: number[][] = {
  [1, 2, 3],
  [4, 5],
};
* 튜플 : 길이와 타입이 고정된 배열
let tup1: [number, number] = [1, 2];
tup1 = [1, 2, 3]; 불가
tup1 = ["a", "b"]; 불가
선언을 길이 타입을 저렇게 해놨으니까.
참고로 결국 자바스크립트로 컴파일된 것을 보면
let tup1 = [1, 2];로 배열로 된다.
* let tup1: [number, number] = [1, 2]; 다음에
tup1.push (1);
tup1.pop();
tup1.pop();
tup1.pop();
을 해도 오류가 안 생긴다
얘가 이걸 결국은 배열로 보기 때문에 [number, number]로
길이를 2로 해놨어도 3개로 만드는 것에 문제를 못 느끼는 것.
따라서 푸시 팝 쓸 때는 컴퓨터가 못 알아줄 수 있으니 주의를 하며 쓰자.
* 튜플을 언제 쓰는가.
const users: [string, number][] = [
  ["이정환", 1], 
  ["이아무개", 2],
  ["김아무개", 3],
  [4, "박아무개"],
];
에서 순서를 바꿔쓴 [4, "박아무개"] 같은 걸 찾아내려고 쓴다.





Sec. 3 Chap. 5 타입 추론

TS는 타입 추론을 한다.
let a = 10;
let b ="hello";
let c = {
  id: 1, 
  name: "이정환",
  profile: {
    nickname: "winterlood",
  },
  urls: ["https://winterlood.com"],
};
c처럼 복잡하게 해도 알아서 초기값을 기준으로
let c: {
  id: numbe;, 
  name: string;
  profile: {
    nickname: string;
  },
  urls: string[];
}
으로 추론해준다.
let { id, name, profile } = c;라는 구조 분해 할당을 해줘도 profile에 커서를 올려보면
let profile: {
    nickname: string;
}
이라고 잘 추론해준다.

function func() {
  return "hello";
}
도
function func(): string으로 잘 해준다.

function func(message = "hello") {
  return "hello";
}
처럼 디폴트 매개변수가 있어도 message에 커서를 올리면
(parameter) message: string으로 잘 해준다.

암기할 필요는 없댄다.

특이한 경우가 있다. ant 타입의 진화다.
let d; -> (암묵적) any 타입.
d = 10; 얘한테 커서를 올리면 number로 나오고
d = "hello" 얘한테 커서를 올리면 string으로 나온다.

const num = 10;이라 하면 const num: 10이라고 리터럴 타입으로 추론된다. let이 아닌 const를 썼기에.
const보다 let이 범용적인 number로 추론해주잖아. 그거를 타입 넓히기 라고 한다.





Sec. 3 Chap. 6 타입 단언

Sec. 3 Chap. 7 타입 좁히기
조건문 등을 이용해 넓은타입에서 좁은타입으로 타입을 상황에 따라 좁히는 방법을 말한다.
function func(value: number | string | Date) {
  if (typeof value === "number") { ---> 이 부분을 타입 가드라고 한댄다.
    console.log(value.toFixed());
  } else if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else if (typeof value === "object") {
    console.log(value.getTime());
  }
}
다만 여기서 object 같은 경우는 조건문에서 null을 반환할 수 있어서
function func(value: number | string | Date | null) {
  if (typeof value === "number") { ---> 이 부분을 타입 가드라고 한댄다.
    console.log(value.toFixed());
  } else if (typeof value === "string") {
    console.log(value.toUpperCase());
  } else if (typeof value === "object") {
    console.log(value.getTime());
  }
}
이라고 쓰면 console.log(value.getTime()); 여기가 "'value'"가 'null'일 수 있다."는 오류메시지를 띄우게 된다.
이때는  } else if (typeof value === "object") { 이거를   } else if (value instanceof Date) { 로 해주면 된다.
value가 instanceof 오른쪽의 객체인가?를 묻는 기능의 문장이다.
instanceof의 오른쪽에는 내장 클래스만 올 수 있나봐. 내가 생성한 객체는 안 돼.
예를 들어 내가
type Person = {
  name: string;
  age: number;
};로 하나 만들었다면 } else if (value instanceof Person) { 은 안 되고
  } else if (value && "age" in value) { 이렇게 in을 써줘야 한다.





Sec. 3 Chap. 8 서로소 유니온 타입 : 교집합이 없는 타입들로만 만든 유니온 타입을 말한다.

type Admin = {
  name: string;
  kickCount: number;
}

type Member = {
  name: string;
  point: number;
}

type Guest = {
  name: string;
  visitCount: number;
}

이 3가지 타입을 유니온한 타입은
type User = Admin | Member | Guest;
이다.

Admin이면 {name}님 현재까지 {kickCount}명 강퇴했습니다.
Member면 {name}님 현재까지 {point}모았습니다
Guest면 {name}님 현재까지 {visitCount}번 방문하셨습니다
를 출력하는 함수를 하나 작성해보자. 아래 코드다.

function login(user: User) {
  if ("kickCount" in user) {
    // admin 타입
    console.log(`$(user.name)님 현재까지 $(user.kickCount)명 강퇴했습니다.`);
  } else if ("point" in user) {
    // member 타입
    console.log(`$(user.name)님 현재까지 $(user.point)모았습니다`);
  } else if ("point" in user) {
    // guest 타입
    console.log(`$(user.name)님 현재까지 $(user.visitCount)번 방문하셨습니다`);
  }
}

이 코드를 "// admin 타입" 등 주석 없이 다른 사람이 이해하지 못하는 문제가 있다고 한다.

type Admin = {
  tag: "ADMIN";
  name: string;
  kickCount: number;
}

type Member = {
  tag: "MEMBER";
  name: string;
  point: number;
}

type Guest = {
  tag: "GUEST";
  name: string;
  visitCount: number;
}

이렇게 겹치지 않는 것을 넣어줘봤다. 이제 Admin, Member, Guest라는 스트링 리터럴 타입의 교집합은 없게 된다. 이들의 교집합은 never 타입이다.

이제
function login(user: User) {
  switch (user.tag) {
    case "ADMIN": {
      console.log(`$(user.name)님 현재까지 $(user.kickCount)명 강퇴했습니다.`);
      break;
    }
    case "MEMBER": {
      console.log(`$(user.name)님 현재까지 $(user.point)모았습니다`);
      break;
    }
    case "GUEST": {
      console.log(`$(user.name)님 현재까지 $(user.visitCount)번 방문하셨습니다`);
      break;
    }
  }
}
특정 타입으로 잘 좁혀진다.
