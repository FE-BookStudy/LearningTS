> 코드 정보에 입각한 추론 - Only TypeScript
>
> - **유니언 union** : 값에 허용된 타입을 두 개 이상의 가능한 타입으로 확장하는 것
> - **내로잉 narrowing** : 값에 허용된 타입이 하나 이상의 가능한 타입이 되지 않도록 좁히는 것

# 1. 유니언 타입

```jsx
let math =
  Math.random() > 0.5 // let math: string | undefined
    ? undefined
    : 'Hello~';
```

- math는 잠재적인 타입으로 `undefined` 나 `string`
- **‘이거 혹은 저거’와 같은 타입 → 유니언**
- `**|` (수직선) 연산자\*\* 사용
- 값이 정확히 어떤 타입인지 모르지만 **두 개 이상의 옵션 중 하나**라고 알고 있는 경우에 사용
- 유니언 타입 선언은 타입 애너테이션으로 타입 정의하는 모든 곳에서 사용 가능

## 💡 유니언 타입 선언

- 변수의 초깃값이 있더라도 변수에 대한 명시적 타입 애너테이션을 제공하는 것이 유용할 때 유니언 타입 사용!
- 어떨 때 유용한데? → 아래처럼 초기값은 null 이지만 thinker의 값으로 string의 값이 할당 가능함을 알려줄 때

```jsx
let thinker: string | null = null;

if (Math.random() > 0.5) {
  thinker = 'ME!'; //Ok
}
```

> 유니언 타입 선언의 순서는 중요 ❌, 앞 뒤 바껴도 똑같이 취급한다.

## 💡 유니언 속성

- 값이 유니언 타입일 때 **선언한 모든 가능한 타입에 존재하는 멤버 속성**에만 접근 가능하다.
- 유니언 외의 타입에 접근하면? 타입 검사 오류!
- 모든 유니언 타입에 존재하지 않는 속성에 대한 접근 제한 - ⚠️ 안전 조치!

```jsx
let person = Math.random() > 0.5 ? 'Emily' : 82;

person.toString();

person.toUpperCase();
// Error: Property 'toUpperCase' does not exist on type 'string | number'.
// Property 'toUpperCase' does not exist on type 'number'.

person.toFixed();
// Error: Property 'toFixed' does not exist on type 'string | number'.
// Property 'toFixed' does not exist on type 'string'.
```

- 유니언 타입으로 정의된 여러 타입 중 하나의 값의 속성 쓰고 싶다?
- **코드에서 보다 구체적인 타입 중 하나라는 것을 타입스크립트에 알려야한다! → 내로잉**

# 2. 내로잉

- 값이 정의, 선언 혹은 이전에 유추된 것보다 **더 구체적인 타입**임을 코드에서 유추하는 것
- **타입을 좁히는데 사용할 수 있는 논리적 검사** → **타입 가드(type guard)**

## 💡 값 할당을 통한 내로잉

- **변수에 직접 값을 할당**하면 타입스크립트는 변수의 타입을 할당된 값의 타입으로 좁힌다.
- **변수에 유니언 타입 명시되고 초깃값 주어질 때 값 할당 내로잉이 작동**

```jsx
let a: number | string;
a = 'apple';
//let a : number string = "apple" //축약가능

a.toUpperCase(); //Ok: string

a.toFixed();
//Error: Property 'toFixed' does not exist on type 'string'.
```

## 💡 조건 검사를 통한 내로잉

- 일반적으로 타입스크립트에 변수가 알려진 값과 같은지 확인하는 `if문` 을 통해 변수의 값을 좁히는 방법을 사용!

```jsx
// scientist : number | string의 타입
let scientist = Math.random() > 0.5 ? 'Franklin' : 51;

if (scientist === 'Franklin') {
  // scientist : string의 타입
  scientist.toUpperCase(); //Ok
}

// scientist : number | string의 타입
scientist.toUpperCase();
//Error : Property 'toUpperCase' does not exist on type 'string | number'.
//Property 'toUpperCase' does not exist on type 'number'.
```

- 조건부 로직으로 내로잉 할 때, 타입 검사 로직은 훌륭한 자바스크립트 코딩 패턴을 미러링해 구현

> 만약 변수가 여러 타입 중 하나?
>
> 일반적으로 필요한 타입과 관련된 검사 want!
>
> 타입스크립트는 강제로 코드를 안전하게 작성할 수 있도록 하는 고마운 언어

## 💡 typeof 검사를 통한 내로잉

- typeof 연산자를 사용해 타입을 좁힐 수도 있다.

```jsx
let researcher = Math.random() > 0.5 ? 'Franklin' : 51;

if (typeof researcher === 'string') {
  researcher.toUpperCase(); //Ok string
}

//! 논리적 부정, else문
if (!(typeof researcher === 'string')) {
  researcher.toFixed(); //Ok number
} else {
  researcher.toUpperCase(); //Ok string
}

//삼항 연산자
typeof researcher === 'string'
  ? researcher.toUpperCase() //Ok: string
  : researcher.toFixed(); //Ok: number
```

# 3. 리터럴 타입

- 좀 더 구체적인 버전의 원시 타입
- **원시 타입 값 중 어떤 것이 아닌 특정 원싯값**으로 알려진 타입

```jsx
const philosopher = 'Hypatia';
```

- 위 변수는 string 타입이 맞음
- 하지만 기술적으로 더 구체적인 "Hypatia" 타입이다.

> 각 원시 타입은 해당 타입이 가질 수 있는 가능한 모든 리터럴 값의 조합
>
> → 즉, 원시타입 == 해당 타입의 가능한 모든 리터럴 값의 집합

## 💡 리터럴 할당 가능성

- 서로 다른 원시타입(ex, number과 string)이 서로 할당되지 못하듯이
- 같은 원시타입을 가지더라도 **서로 다른 리터럴 타입은 서로 할당할 수 없다!**

```tsx
let specificallyAda: 'Ada';

specificallyAda = 'Ada'; //ok

specificallyAda = 'Aron';
//Error: Type '"Aron"' is not assignable to type '"Ada"'

let someString = '';

specificallyAda = someString;
//Error: Type 'string' is not assignable to type '"Ada"'.
```

## 🔆 엄격한 null 검사

- 리터럴로 좁혀진 유니언의 힘은 타입스크립트에서 `strict null checking`라고 부르는 타입 시스템 영역, **“잠재적으로 정의되지 않은 undefined 값”으로 작업할 때 강력!**
- 타입스크립트는 **“십억 달러의 실수”**를 바로잡고자 strict null checking을 사용

### 💡 십억 달러의 실수

- 다른 타입이 필요한 위치에서 null 값을 사용하도록 허용하는 많은 타입 시스템을 가리키는 업계 용어
- 엄격한 null 검사가 없는 경우 아래 코드가 허용됨

```tsx
const firstName: string = null;
```

- 타입스크립트 컴파일러 옵션 중 `strictNullChecks` : 엄격한 null 검사 활성화 여부 결정
- `"strictNullChecks": true`설정 시, 모든 데이터 타입은 `null`, `undefined`를 할당 받을 수 없다.

---

- `"strictNullChecks": false` 설정 시

```tsx
let nameMaybe = Math.random() > 0.5 ? 'Tony' : undefined;

nameMaybe.toLowerCase();
//Potential runtime error: Cannot read property 'toLowerCase' of undefined.
```

- 위 코드의 타입은 완벽히 안전하다고 간주된다,
- 하지만 nameMaybe 변수가 .toLowerCase에 접근 시 undefined가 되는 것 자체가 잘못된 것!
- `"strictNullChecks": true` 설정 시

```tsx
let nameMaybe = Math.random() > 0.5 ? 'Tony' : undefined;

nameMaybe.toLowerCase();
//Error: Object is possibly 'undefined'.
```

- **엄격한 null 검사를 활성화해야만 코드가 null 또는 undefined 값으로 인한 오류로부터 안전한지 여부를 쉽게 파악할 수 있다!**

> 타입스크립트 모범사례는 엄격한 null 검사를 활성화하는 것!

## 💡 참 검사를 통한 내로잉

- 자바스크립트에서 **참** 또는 **truthy는 falsy**로 정의된 값을 제외한 모든 값
- **falsy로 정의된 값 :** `false, 0, -0, 0n, “”, null, undefined, NaN`
- 타입스크립트는 잠재적인 값 중 truthy로 확인된 **일부만** 변수의 타입을 좁힐 수 있다.

```tsx
let geneticist = Math.random() > 0.5 ? 'Babara' : undefined;

if (geneticist) {
  geneticist.toLowerCase(); //Ok: string
}

geneticist.toLowerCase();
//Error: Object is possibly 'undefined'.
```

- 위 코드에서 geneticist의 타입은 string | undefined이다.
- undefined는 항상 falsy이므로 위 if 문의 코드블록에서 string 타입이 되어야한다.

```tsx
geneticist && geneticist.toUpperCase(); //Ok: string | undefined
geneticist?.toUpperCase(); //Ok: string | undefined
```

- `&&`,`?`연산자로 참 여부 확인 가능
  - but `string  | undefined` 값에 대해 알고 있는 것이 falsy라면 그것이 빈 문자열인지 undefined인지는 알 수 없음

```tsx
let biologist = Math.random() > 0.5 && 'Rachel';

if (biologist) {
  biologist; //타입 string
} else {
  biologist; //타입 false | string
}
```

## 💡 초깃값이 없는 변수

- 자바스크립트 초깃값이 없는 변수 → undefined
- 타입스크립트도 값이 할당될 때까지 변수가 undefined임을 이해함!

```tsx
let me: string;
me?.length;
//Error: Variable 'me' is used before being assigned.
me = 'kim';
me.length; //Ok
```

- 변수 타입에 undefined가 포함되어 있다면?

```tsx
let me: string | undefined;
me?.length; //Ok
me = 'kim';
me.length; //Ok
```

# 4. 타입 별칭

- 재사용하는 타입에 더 쉬운 이름을 할당하는 방법 type alias
- `type 새로운 이름 = 타입`
- 타입 별칭은 PascalCase로 지정

```tsx
type MyName = ...;
```

- 타입 시스템의 ‘복붙’처럼 작동

```tsx
type door = boolean | number | string | null | undefined;

let suzume: door;
let souta: door;
let daizin: door;
```

- 훨씬 읽기 쉽다.
- 타입이 복잡해질 때 사용할 수 있는 편리한 기능!

## 🔆 type alias 는 javaScript가 ❌

- 타입스크립트의 타입 시스템에만 존재
- .js → .ts 시 사라지는 코드!
- 런타임 코드에서 참조 불가, **개발 시**에만 존재

```tsx
type door = boolean | number | string | null | undefined;

console.log(door);
//Error: 'door' only refers to a type, but is being used as a value here.
```

## 🔆 타입 별칭 결합

- 타입 별칭은 다른 타입 별칭을 참조할 수 있다.

```tsx
type Id = number | string;
type IdMaybe = Id | undefined | null;
//순서는 바뀌어도 상관 없다. IdMaybe 앞에 Id가 와도 OK
```
