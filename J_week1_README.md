#  한 입 크기로 잘라먹는 타입스크립트 😋


> ###  *타입스크립트* 란 ‼️

기존의 자바스크립트를 더 안전하게 사용할 수 있도록 고안된 언어

    🤔 왜 JS로 충분하지 않고 타입스크립트를 해야될까?
    
      프로그래머가 쉽고 빠르고 간결하게 웹브라우저에서만 사용 ➡️ js
      node.js 탄생 이후 ➡️ js가 웹브라우저 뿐만 아니라 범용적 사용
      따라서 안정적인 기능의 필요성 증가

---


  ### 타입스크립트의 동작 원리 🦾


👨‍👨‍👦‍👦  대다수의 프로그래밍 언어는 컴파일러를 통해 AST(추상문법트리)로 변환된 후, 바이트 코드로 변환됨



🙍‍♂️ *타입스크립트*도 다른언어와 마찬가지로 AST로 변환됨
  
  그러나 아래와 같은 차이점이 존재한다!

  
    1. AST를 바이트 코드로 변환하는 것이 아닌 코드 상에 오류가 없는 지 타입 검사를 실행 함 ✔️
    2. 코드 타입 오류 ⭕️  ➡️  컴파일 중단
      
        코드 오류 ❌ ➡️ 바이트 코드가 아닌 자바스크립트 코드로 변환 후 컴파일 종료


---



 ### 타입스크립트의 컴파일러 옵션 ⚙️


타입스크립트의 컴파일이 성공하게 되면 자바스크립트 코드로 변환이 됨
  <br>
    이 **컴파일 과정에서 얼마나 엄격하게 😈 타입 오류를 검사할 건지 등 결정하는 것 
 등**을 컴파일러 옵션이라고 함

1. ```include``` 옵션


    src폴더 아래에 있는 모든 타입스크립트 파일을 **동시에** 컴파일할 수 있음
    
        {
	      "include":["src"]
        }
    사용법은 터미널에서 tsc를 입력하면 됨



  2. ```target``` 옵션

       옵션에 설정한 버전에 맞추어 타입스크립트 코드를 자바스크립트 코드로 변환할 수 있음

          {
         "compilerOptions": {
          "target": "ES5"
          },
          "include": ["src"]
          }

      타입스크립트

      ```jsx
      const func = () => console.log("Hello");
      ```

      변환된 자바스크립트
      ```jsx
      const func = function(){
        console.log("Hello");
      }
      ```

3. ```module``` 옵션

    변환되는 자바스크립트 코드의 모듈 시스템을 설정하는 옵션

    ```jsx
    {
      "compilerOptions": {
        "target": "ESNext",
        "module": "ESNext"
      },
      "include": ["src"]
    }
    ```
    ESNext로 입력하면 최신문법으로 설정됨

4. ```outDir```   옵션

    원하는 새로운 위치에 자바스크립트 파일이 생성되게 해주는 옵션 

    ```jsx
    {
      "compilerOptions": {
        "target": "ESNext",
        "module": "ESNext",
        "outDir": "dist"
      },
      "include": ["src"]
    }
    ```
    위 코드는 dist라는 폴더에 자바스크립트 파일이 생성되게 함

5. ```strict``` 옵션

    타입 검사를 얼마나 엄격하게 할 지를 결정하는 옵션

    이 코드는 타입스크립트에서 아무런 오류가 발생하지 않지만
    ```jsx
    {
      "compilerOptions": {
        ...
        "strict": true
      },
      "include": ["src"]
    }
    ```

    이와 같이 strict 옵션을 설정할 경우 아래와 같은 오류가 발생함
    <img>
---

 ## 타입스크립트의 타입 🧩

 > **원시 타입**
  
    동시에 한 개의 값만 저장할 수 있는 타입

      EX)  ```boolean```
    
- ```number```

  number 타입은 자바스크립트에서 **숫자를 의미하는 모든 값을 포함**하는 타입으로,
  
  단순 정수 뿐만 아니라 소수, 음수, Infinity, NaN등의 특수한 숫자들도 포함

  ```typescript
  let str1: string = "hello";
  let str2: string = 'hello';
  let str3: string = `hello`;
  let str4: string = `hello ${str1}`;
  ```

- ```string```

  string 타입
  단순 쌍따옴표 문자열 뿐만 아니라 작은 따옴표, 백틱, 템플릿 리터럴로 만든 **모든 문자열을 포함**

    ```typescript
  let str1: string = "hello";
  let str2: string = 'hello';
  let str3: string = `hello`;
  let str4: string = `hello ${str1}`;
  ```

- ```boolean```

  rue 또는 false만 이 타입에 해당

  ```typescript
    let bool1 : boolean = true;
    let bool2 : boolean = false;
  ```

- ```null```

  null 타입은 오직 null 값만 포함하는 타입
  ```typescript
  let null1: null = null;
  ```

- ```undefined```

  역시 null 타입과 마찬가지로 오직 하나의 값 undefined만 포함하는 타입
  ```typescript
  let unde1: undefined = undefined;
  ```
  ---


#### 🤔 다른 타입의 변수에 초깃값으로 null을 넣어주고 싶다면? 💭
    컴파일러 옵션으로 ```"strictNullChecks": false```를 사용하면

    number 타입의 변수에  null을 임시로 넣을 수 있게 설정 가능! 

<br>

 > **리터럴 타입**

  딱 하나의 값만 포함하는 타입 ☝️

     let numA: 10 = 10; 
위 코드에서는 변수 numA의 타입을 숫자 10으로 설정한다

이후에 numA는 숫자 10 이외의 값은 저장할 수 없다‼️
<br>


> **배열 타입**

 정의 방법
 
 1. ```배열요소타입[]```
  
        
        let numArr: number[] = [1, 2, 3]
2. **다양한 타입 요소**를 갖는 배열 타입 정의

       let multiArr: (number | string)[] = [1, "hello"];

    위 코드는 number 혹은 string 타입일 것을 의미!

3. **다차원 배열 타입 정의** 

    [ ]를 연달아 작성해준다
      ```typescript
              let doubleArr : number[][] = [
                [1, 2, 3], 
                [4, 5],
              ];
      ```


> **튜플 타입**

  자바스크립트에는 없는 타입스크립트의 특수한 타입으로 *길이와 타입이 고정된* 배열

  컴파일 해 보면 결국 튜플은 자바스크립트 배열로 변환됨


> **객체 타입**

1. object로 정의

    ➡️ 타입스크립트의 object 타입은 단순 값이 객체임을 표현하는 것 외에는 아무런 정보도 제공하지 않음

2. **객체 리터럴 타입** ⭐️

    괄호를 열고 객체가 갖는 프로퍼티를 직접 나열해 만드는 타입
```typescript
        let user: {
            id: number;
            name: string;
          } = {
            id: 1,
            name: "이정환",
          };

        user.id
```
  ----

  ## 타입 별칭 💫

  ```type 타입_이름``` = 타입 형태로 타입을 정의


## 인덱스 시그니처  💬
   객체가 <key, vlaue>형식이며<br>
  key와 value의 타입을 정확하게 명시해야하는 경우 사용

```[key : string] : string``` 은 인덱스 시그니쳐 문법으로<br>
 이 객체 타입에는 key가 string 타입이고 value가 string 타입인 모든 프로퍼티를 포함된다 라는 의미
```typescript
    type CountryNumberCodes = {
         [key: string]: number;
    };
````

> ⚠️ key 타입은 string, number, symbol, Template literal타입만 가능하다. <br>다음과 같은 코드는 에러를 발생시킴! 👿 <br>
```typescript
    type userType = {
      [key: string | boolean]: string; // 에러발생
    }
```



## Enum
**말 그대로 관련있는 상수들의 집합**

자바스크립트에는 존재하지 않고 <br>
오직 타입스크립트에서만 사용할 수 있는 특별한 타입

### ✨ 문자열 열거형

    enum Role {
      ADMIN,
      USER,
      GUEST,
    }

    enum Language {
      korean = "ko",
      english = "en",
    }

    const user1 = {
      name: "이정환",
      role: Role.ADMIN, // 0
      language: Language.korean,// "ko"
    };

👍 **문자열 enum을 사용하면**<br> user1.language 같은 프로퍼티에 실수로 “ko”라고 적었어야 할 것을 오타가 발생해 “kos”로 적거나<br> 또는 순간적으로 헷갈려 “KO-kr” 처럼 적는 **실수를 방지**할 수 있다!


> **any 타입**

  타입 검사를 받지 않는 타입
      
      🙅 위험성 때문에 어쩔 수 없는 경우를 제외하고는 any 타입을 사용하지 않는것을 강력히 권장
<br>

> **UnKnown 타입**

unknown 타입의 변수는 어떤 타입의 값이든 다 저장할 수 있다

그러나 *any 타입과 달리* <br>
unknown 타입은 **any 타입 외의 어떤 타입에도 할당할 수 없다!**<br>
반면 any는 never를 제외한 모든것에 할당 가능하다

      😋 any와 unknown의 차이점 요약
      
      1. unknown 타입은 any를 제외한 다른 타입에 할당할 수 없음
      2. unknown 타입은 타입좁히기 없이는 메서드 호출, 인스턴스 생성,프로퍼티 접근을 할 수 없음

> **void 타입**

아무런 값도 없음을 의미<br>
보통은 다음과 같이 아무런 값도 반환하지 않는 함수의 반환값 타입을 정의할 때 사용


> **never 타입**

불가능을 의미하는 타입<br>
보통 다음과 같이 함수가 어떠한 값도 반환할 수 없는 상황일 때 해당 함수의 반환값 타입을 정의할 때 사용

      💭 모든 값을 나타내는 any도 never에 속할 수는 없다.

타입 시스템 중에는 **그 어떤 것도 불가능하다는 타입이 필요**<br>
불가능이라는 뜻은 ts 에서는 다음과 같이 의미한다고 한다.

- 어떤 값도 가질 수 없는 빈 타입
- 제네릭 및 함수에서 허용되지 않는 파라미터
- 호환 되지 않는 타입 교차
- 빈 유니언 타입 (유니언 했지만 아무것도 안되는 경우)

그럼 결론적으로 위 의미를 나타내기 위해 never 타입을 쓰게 될 것


## 타입스크립트 이해하기 🧠
### 타입은 집합이다!

<img width="653" alt="스크린샷 2024-09-05 오후 5 14 31" src="https://github.com/user-attachments/assets/327bb445-450b-4faf-b94e-16f36f868b1c">
그림처럼 여러개의 숫자 값들을 묶어 놓은 집합을 <br>
타입스크립트에서는 number 타입이라고 부른다

<br>

🤔 그렇다면 **오직 하나의 값만 포함하는 타입인 Number Literal 타입**은 어떤 집합 일까?
<img width="633" alt="스크린샷 2024-09-05 오후 5 17 11" src="https://github.com/user-attachments/assets/5911fedf-942f-4f7f-8d98-f4b6ccb67fc3">
이 경우에는 딱 하나의 값만 포함하는 아주 작은 집합이라고 볼 수 있다

그리고 이 20 이라는 타입에 속하는 요소인 숫자 20은 <br>
Number 타입이라는 거대한 집합에도 속하는 값<br>
결국 ***모든 Number Literal 타입은 Number 타입이라는 거대한 집합에 포함되는 부분 집합***으로 볼 수 있다!

<img width="641" alt="스크린샷 2024-09-05 오후 5 19 24" src="https://github.com/user-attachments/assets/e4fbd6be-119f-432c-9152-e7c5eff53236">

### 타입 호환성 
구조적 서브타이핑(structural subtyping)에 기반한다 <br>
구조적 타이핑은 멤버만을 기준으로 타입을 연관시킨다

이는 명목적 타이핑(nominal typing)과 대조된다

<img width="625" alt="스크린샷 2024-09-05 오후 5 22 50" src="https://github.com/user-attachments/assets/951abe97-bdd5-45fb-8736-c800a18904a2">

아래의 코드는 문제가 발생하지 않지만,

<img width="634" alt="스크린샷 2024-09-05 오후 5 23 31" src="https://github.com/user-attachments/assets/eae3e5a8-cf16-4180-84d0-2ace103904d5">

반대로  더 큰 타입의 값을 더 작은 타입의 값으로 취급하는 것은 안되기 때문에 오류가 난다

<img width="636" alt="스크린샷 2024-09-05 오후 5 23 47" src="https://github.com/user-attachments/assets/16caf1a4-854d-4e5f-a3b0-ce4f4a406452">

    💡 따라서 타입스크립트에서는 이렇게 슈퍼타입의 값을 서브타입의 값으로 취급하는것을 허용하지 않음
    하지만 반대로는 허용함!!

## 타입 계층도
<img width="929" alt="스크린샷 2024-09-05 오후 5 26 54" src="https://github.com/user-attachments/assets/28cd371f-5f4a-412c-82d5-7122114e5bd0">

## 객체 타입의 호환성 { }
모든 객체 타입은 각각 다른 객체 타입들과 슈퍼-서브 타입 관계를 갖는다<br>
따라서 **업 캐스팅은 허용**하고 **다운 캐스팅은 허용**하지 않는다!

``` jsx
type Animal = {
  name: string;
  color: string;
};

type Dog = {
  name: string;
  color: string;
  breed: string;
};

let animal: Animal = {
  name: "기린",
  color: "yellow",
};


let dog: Dog = {
  name: "돌돌이",
  color: "brown",
  breed: "진도",
};

animal = dog; // ✅ OK
dog = animal; // ❌ NO

```

➡️
어떤 객체가 Dog 타입에 포함된다면 무조건 Animal 타입에도 포함된다<br>
그러나 반대로 Animal 타입에 포함되는 모든 객체가 Dog 타입에 포함되는것은 아님 <br>
따라서 **결국 Animal은 Dog의 슈퍼타입**이다


## 대수 타입
> ### Union 타입 ( ```|``` )

<img width="489" alt="스크린샷 2024-09-05 오후 5 36 36" src="https://github.com/user-attachments/assets/c6bb2c13-b4eb-427f-bf38-9fb980ec31f2">
    // 합집합 타입 - Union 타입
    let a: string | number; 

<br>

💡 **Union 타입으로 배열 타입 정의하기** <br>
        ```let arr: (number | string | boolean)[] = [1, "hello", true];```

> ### intersection 타입 ( ```&``` )
<img width="490" alt="스크린샷 2024-09-05 오후 5 36 25" src="https://github.com/user-attachments/assets/c3321bf3-51c1-4ee9-9c5f-bf6a92cf6c82">

      let variable: number & string; 
  
-> 서로 교집합을 공유하지 않는 서로소 집합이므로 never 타입으로 추론된다


## 타입 좁히기
> ### instanceof 타입가드
```instancof```는 비교연산자로, 해당하는 변수가 사용하고 있는 객체 체인을 검사한다<br>
쉽게 말해서, 해당하는 변수의 클래스와 비교해서 true/false를 반환해준다고 생각하면 됨!

```jsx

        function func(value: number | string | Date | null) {
        if (typeof value === "number") {
          console.log(value.toFixed());
        } else if (typeof value === "string") {
          console.log(value.toUpperCase());
        } else if (value instanceof Date) {
          console.log(value.getTime());
        }
      }
```

😭 그러나 Instanceof는 내장 클래스 또는 직접 만든 클래스에만 사용이 가능하다 <br> 따라서 우리가 직접 만든 타입에는 사용할 수 없다

> ### in 타입 가드
in 연산자는 우리가 직접 만든 타입과 함께 사용할 수 있다

```jsx
      type Person = {
        name: string;
        age: number;
      };

      function func(value: number | string | Date | null | Person) {
        if (typeof value === "number") {
          console.log(value.toFixed());
        } else if (typeof value === "string") {
          console.log(value.toUpperCase());
        } else if (value instanceof Date) {
          console.log(value.getTime());
        } else if (value && "age" in value) {
          console.log(`${value.name}은 ${value.age}살 입니다`)
        }
      }
```


## 함수 타입

### 함수 타입 표현식
함수 타입을 타입 별칭과 함께 별도로 정의할 수 있다

        type Add = (a: number, b: number) => number;
        const add: Add = (a, b) => a + b;   


### 호출 시그니쳐
호출 시그니쳐(Call Signature)는 함수 타입 표현식과 동일하게 함수의 타입을 별도로 정의하는 방식입니다.


```jsx
type Operation2 = {
(a: number, b: number): number;
};
const add2: Operation2 = (a, b) => a + b;
const sub2: Operation2 = (a, b) => a - b;
const multiply2: Operation2 = (a, b) => a * b;
const divide2: Operation2 = (a, b) => a / b;
```

자바스크립트에서는 함수도 객체이기 때문에, <br>
위 코드처럼 객체를 정의하듯 함수의 타입을 별도로 정의할 수 있음


### 함수 오버로딩
하나의 함수를 매개변수의 개수나 타입에 따라 다르게 동작하도록 만드는 문법

```typescript
// 버전들 -> 오버로드 시그니쳐
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

func(1);        // ✅ 버전 1 - 오버로드 시그니쳐
func(1, 2);     // ❌ 
func(1, 2, 3);  // ✅ 버전 3 - 오버로드 시그니쳐
```
구현 시그니쳐의 매개변수 타입은 모든 오버로드 시그니쳐와 호환되도록 만들어야 한다!

