## 한 입 크기로 잘라먹는 타입스크립트 week3

> ### SECTION 10

### 유틸리티 타입
     맵드 타입, 조건부 타입 등의 타입 조작 기능을 이용해 실무에서 자주 사용되는 유용한 타입들을 모아 놓은 것을 의미

----

### 맵드타입 기반 유틸리티 타입

📌 **Partial<T>**

부분적인 또는 일부분의 라는 뜻으로 특정 객체 타입의 모든 프로퍼티를 선택적 프로퍼티로 변환

```typescript
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
````
📌 **Required<T>**

특정 객체 타입의 모든 프로퍼티를 필수(선택적이지 않은) 프로퍼티로 변환

```typescript
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

📌 **Readonly<T>**

특정 객체 타입의 모든 프로퍼티를 읽기 전용 프로퍼티로 변환
```typescript
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

📌 **Pick<T, K>**

주어진 첫번째 제네릭 타입 T 내에서 Union 타입 K라는 프로퍼티에 대한 타입들을 뽑아낸다.
필요한 타입만 추출해서 원하는 타입을 만들 수 있음



```typescript
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


📌 **Omit<T, K>**

특정 객체 타입으로부터 특정 프로퍼티 만을 제거하는 타입

```typescript
const noTitlePost: Omit<Post, "title"> = {
  content: "",
  tags: [],
  thumbnailURL: "",
};
```

📌 **Record<K, T>**

Record 타입은 두 개의 제네릭 타입을 받을 수 있다.
첫번째 제네릭 타입 K는 key 값의 타입으로, 두번째 제네릭 타입 T은 값의 타입으로 갖는 타입을 리턴

```typescript
type Thumbnail = Record<
  "large" | "medium" | "small",
  { url: string }
>;
```

-----

## TIL

📌 **Exclude<T, U>**

첫번째 제네릭 타입 T 중 두번째 제네릭 타입 U와 겹치는 타입을 제외한 타입을 반환한다.

```typescript
type Exclude<T, U> = T extends U ? never : T;
```
타입 T가 타입 U를 상속하거나 동일 타입이라면 무시(never)하고 아닐 경우 타입 값을 리턴한다.

```typescript
type OnlyNumber = Exclude<string|number, string>;
// OnlyNumber는 number 타입이다.
```
다시 말해 두번째 제네릭 타입 U에 대해
첫번째 제네릭 타입 T가 할당 가능한 타입인지를 판단하고,
할당 가능한 타입을 제외한 나머지 타입들을 이용하여 타입을 정의한다.



📌 **Extract<T, U>**

첫번째 제네릭 타입 T에 대하여 제네릭 타입 U 중 할당 가능한 타입을 할당한다. Exclude와 반대되는 개념이다.



```typescript
type Extract<T, U> = T extends U ? T : never;
```
T 타입에서 U 타입과 겹치는 타입만을 추출한다.

```typescript
type Event = {
  id: string;
  title: string;
};

type Point = {
  target: string;
  amount: number;
};

type PointInfo = Extract<Event|Point, Point>;
// PointInfo 타입과 Point 타입은 동일한 타입이다.
```
Exclude 타입과는 다르게 유니온 타입 Event와 Point에 대하여 Point 할당 타입을 반환하기 때문에 PointInfo 타입은 Point 타입과 같아진다. 내가 원하는 프로퍼티로 구성된 타입이 이미 있을 때 사용할 수 있을거 같다.


📌 **NonNullable<T>**

주어진 제네릭 타입 T에서 null과 undefined를 제외한 타입을 구성한다.

```typescript
type NonNullable<T> = T extends null | undefined ? never : T;
```
null 혹은 undefined 타입이거나 상속한다면 무시하고, 아니라면 타입을 리턴한다.

```typescript
type NotNullType = NonNullable<string | number | undefined>;
// NotNullType은 string | number
```

📌 **ReturnType<T>**


유틸리티 타입 중에 제일 자주 봤던 타입이다.
ReturnType 타입은 주어진 제네릭 타입 T의 return type을 할당한다.

```typescript
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;
```

T는 (…args: any) => any를 상속한다. 모든 타입의 파라미터를 인자로 받고 결과값으로 모든 타입의 값을 리턴하기 때문에 사실상 모든 함수를 상속한다.<br/>
infer라는 키워드는 타입을 추론할 때 사용하는데, 추론된 타입 값을 R에 할당해준다.
      T extends (...args: any) => infer R ? R : any
      는 R 타입에 대해서 타입 추론이 가능하다면 R 타입을, 그렇지 않다면 any 타입을 반환한다. <br/>
      쉽게 말해서 함수의 리턴 타입을 반환한다.