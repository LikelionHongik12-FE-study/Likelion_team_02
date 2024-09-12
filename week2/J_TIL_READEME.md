## TIL


**Union 타입**

**`A`이거나 또는 `B`인 경우를 타입으로 만들고 싶을 때**

```tsx
ClothingProduct | ShoeProduct
```

<aside>
🤔

**언제 Enum을 쓰고 언제 타입 별칭의 Union을 사용할까?**

**Enum을 사용한 경우**

```tsx
enum UserType {
  Admin = 'admin',
  User = 'user',
  Guest = 'guest',
}

const role = UserType.Admin;
console.log(role === UserType.Guest);

```

Enum은 자바스크립트 객체를 만들어서 그 객체를 사용

**➡️ 되도록 Enum의 목적에 맞는 경우라면 Enum 문법을 사용**

---

**타입 별칭**

```tsx
type UserType = 'admin' | 'user' | 'guest'

const role: UserType = 'admin';
console.log(role === 'guest');

```

오직 **타입스크립트에서만** 의미 있는 코드!

https://handhand.tistory.com/277

🔗 구글링 해봤을 때, 대부분의 경우는 enum보다 union type을 사용하는 듯 하다..

위 블로그에서 정리한 **enum의 문제점**은

1. **enum은 기본적으로 확장이 불가함**
    
    따라서 enum끼리 합치고 싶다면 union type을 이용해야함
    
2. **enum을 사용하면 value값에 해당하는 값을 직접 할당 불가**
    
    <img width="476" alt="스크린샷 2024-09-12 오후 2 58 52" src="https://github.com/user-attachments/assets/7d3f95d3-ca11-4201-8911-20fdc251b12e">

    
3. **enum의 불완전한 타입 불안정성**
4. **코드 사이즈의 증가**
</aside>