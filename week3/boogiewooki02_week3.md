# 타입스크립트 seciton 10~12 정리

## Section 10. 조건부 타입
[조건부 타입]은 extends와 삼항 연산자를 이용해 조건에 따라 각각 다른 타입을 정의하도록 돕는 문법입니다.
```ts
type A = number extends string ? number : string;
```
> 위 조건부 타입의 조건식 number extends string은 number 타입이 string 타입의 서브타입이 아니기 때문에 거짓이 되고 그 결과 타입 A는 string 타입이 됩니다.

(예제)
```ts
type ObjA = {
  a: number;
};

type ObjB = {
  a: number;
  b: number;
};

type B = ObjB extends ObjA ? number : string;
```
> ObjB는 ObjA의 서브 타입 이므로 조건식이 참이되어 B는 number 타입이 됩니다.
### 재네릭 조건부 타입
```ts
type StringNumberSwitch<T> = T extends number ? string : number;

let varA: StringNumberSwitch<number>;
// string

let varB: StringNumberSwitch<string>;
// number
```
- varA는 T에 number 타입을 할당합니다. 그 결과 조건식이 참이 되어 string 타입이 됩니다.
- varB는 T에 string 타입을 할당합니다. 그 결과 조건식이 거짓이 되어 number 타입이 됩니다.
### 분산적인 조건부 타입
조건부 타입의 타입 변수에 Union 타입을 할당하면 분산적인 조건부 타입이 됩니다.
```ts
type StringNumberSwitch<T> = T extends number ? string : number;

let c: StringNumberSwitch<number | string>;
// string | number
```
> 타입 변수에 할당한 Union 타입 내부의 모든 타입이 분리됩니다. 따라서 StringNuberSwitch<number | string> 타입은 다음과 같이 분산됩니다.
```ts
StringNumberSwitch<number>
StringNumberSwitch<string>
```
결과 : number | string
### infer
infer는 조건부 타입 내에서 특정 타입을 추론하는 문법입니다.  
infer는 다음과 같이 특정 함수 타입에서 반환값의 타입만 추출하는 특수한 조건부 타입인 ReturnType을 만들 때 이용할 수 있습니다.
```ts
type ReturnType<T> = T extends () => infer R ? R : never;

type FuncA = () => string;

type FuncB = () => number;

type A = ReturnType<FuncA>;
// string

type B = ReturnType<FuncB>;
// number
```
> 조건식 T extends () => infer R에서 infer R은 이 조건식을 참이 되도록 만들 수 있는 최적의 R 타입을 추론하라는 의미입니다.

따라서 A 타입을 계산할 때의 위 코드의 흐름은 다음과 같습니다.
1. 타입 변수 T에 함수 타입 FuncA가 할당됩니다.
2. T는 () ⇒ string 이 됩니다.
3. 조건부 타입의 조건식은 다음 형태가 됩니다  ```() ⇒ string extends () ⇒ infer R ? R : never```
4. 조건식을 참으로 만드는 R 타입을 추론 합니다. 그 결과 R은 string이 됩니다.
5. 추론이 가능하면 이 조건식을 참으로 판단합니다 따라서 결과는 string이 됩니다. 만약 추론이 불가능 하다면 조건식을 거짓으로 판단합니다.
## Sectin 11. 유틸리티 타입
유틸리티 타입이란 제네릭, 맵드 타입, 조건부 타입 등의 타입 조작 기능을 이용해 실무에서 자주 사용되는 유용한 타입들을 모아 놓은 것을 의미합니다.
### 맵드 타입을 기반으로 만들어진 유틸리티 타입(6가지)
1. `Partial<T>`  
특정 객체 타입의 모든 프로퍼티를 선택적 프로퍼티로 변환합니다. 따라서 기존 객체 타입에 정의된 프로퍼티들 중 일부분만 사용할 수 있도록 도와주는 타입입니다.
```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

const draft: Partial<Post> = {
  title: "제목 나중에 짓자",
  content: "초안...",
};
```
> Partial<T> 타입은 타입 변수 T로 전달한 객체 타입의 모든 프로퍼티를 다 선택적 프로퍼티로 변환합니다. 따라서 Partial<Post> 타입은 모든 프로퍼티가 선택적 프로퍼티가 된 Post 타입과 같습니다.
2. `Required<T>`  
특정 객체 타입의 모든 프로퍼티를 필수(선택적이지 않은) 프로퍼티로 변환합니다.
```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const withThumbnailPost: Required<Post> = { // ❌
  title: "한입 타스 후기",
  tags: ["ts"],
  content: "",
  // thumbnailURL: "https://...",
};
```
> Required<Post>는 Post 타입의 모든 프로퍼티가 필수 프로퍼티로 변환된 객체 타입입니다. 따라서 위 코드처럼 thumbnailURL 프로퍼티를 생략하면 이제 오류가 발생하게 됩니다.
3. `Readonly<T>`  
특정 객체 타입의 모든 프로퍼티를 읽기 전용 프로퍼티로 변환합니다.
```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const readonlyPost: Readonly<Post> = {
  title: "보호된 게시글입니다.",
  tags: [],
  content: "",
};

readonlyPost.content = '해킹당함'; // ❌
```
> Readonly<Post>는 Post 타입의 모든 프로퍼티를 readonly(읽기 전용) 프로퍼티로 변환합니다. 따라서 점표기법을 이용해 특정 프로퍼티의 값을 수정하려고 하면 오류를 발생시킵니다.
4. `Pick<T, K>`  
특정 객체 타입으로부터 특정 프로퍼티 만을 골라내는 그런 타입입니다. 예를 들어 Pick 타입에 T가 name, age가 있는 객체 타입이고 K가 name 이라면 결과는 name만 존재하는 객체 타입이 됩니다.
```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const legacyPost: Pick<Post, "title" | "content"> = {
  title: "",
  content: "",
};
// 추출된 타입 : { title : string; content : string }
```
> 변수 legacyPost의 타입으로 Pick<Post, "title" | "content">을 정의했습니다. 따라서 이때 타입변수 T에는 Post가 타입변수 K에는 “title” | “content” 이 각각 할당됩니다. 그럼 Post 타입으로부터 “title”과 “content” 프로퍼티만 쏙 뽑아낸 객체 타입이 됩니다.
5. `Omit<T, K>`  
특정 객체 타입으로부터 특정 프로퍼티 만을 제거하는 타입입니다. 예를 들어 Omit 타입에 T가 name, age가 있는 객체 타입이고 K가 name 이라면 결과는 name을 제외하고 age 프로퍼티만 존재하는 객체 타입이 됩니다.
```ts
interface Post {
  title: string;
  tags: string[];
  content: string;
  thumbnailURL?: string;
}

(...)

const noTitlePost: Omit<Post, "title"> = {
  content: "",
  tags: [],
  thumbnailURL: "",
};
```
6. `Record<K ,V>`  
K에는 어떤 프로퍼티들이 있을지 String Literal Union 타입을 할당하고 V에는 프로퍼티의 값 타입을 할당합니다. 중복 코드의 발생을 막을 수 있습니다.
```ts
type Thumbnail = Record<
  "large" | "medium" | "small",
  { url: string }
>;
```
### 조건부 타입을 기반으로 만들어진 타입(3가지)
1. `Exclude<T, K>`  
T로부터 K를 제거하는 타입입니다.
```ts
type A = Exclude<string | boolean, string>;
// boolean
```
2. `Extract<T, K>`  
T로 부터 U를 추출하는 타입입니다.
```ts
type B = Extract<string | boolean, boolean>;
// boolean
```
3. `ReturnType<T>`  
ReturnType은 타입변수 T에 할당된 함수 타입의 반환값 타입을 추출하는 타입입니다.
```ts
type ReturnType<T extends (...args: any) => any> = T extends (
  ...agrs: any
) => infer R
  ? R
  : never;

function funcA() {
  return "hello";
}

function funcB() {
  return 10;
}

type ReturnA = ReturnType<typeof funcA>;
// string

type ReturnB = ReturnType<typeof funcB>;
// number
```
## Section 12. 리액트와 타입스크립트
