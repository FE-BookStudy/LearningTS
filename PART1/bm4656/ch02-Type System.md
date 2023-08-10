# 1. 타입의 종류

- ‘타입’은 자바스크립트에서 다루는 값의 **형태**
- 형태란 값에 존재하는 속성과 메서드, 내장되어 있는 typeof 연산자가 설명하는 것을 의미

### 💡 타입스크립트의 기본적인 타입

- typescript는 javascript의 기본 원시 타입 7가지를 다음과 같이 간주한다.

```jsx
null; //null
undefined; //undefined
true; // boolean
('types'); //string
1217; //number
1217n; //bigint
Symbol('bmk'); //symbol
```

### 💡 타입스크립트의 타입 유추

- 타입스크립트는 계산된 초깃값을 갖는 변수의 타입을 유추할 수 있을 만큼 충분히 똑똑함!

```tsx
//타입 : string
let bestSong = Math.random() > 0.5 ? 'Chain of Fools' : 'Respect';
```

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/eef3ddb4-8639-417b-8dd5-9b8ecd093d36/Untitled.png)

- 타입스크립트가 bestSong 변수의 타입 string으로 유추함

### 💦 타입의 대문자 소문자?

- 자바스크립트에서의 객체와 원시 타입의 차이점
- 자바스크립트에서 Boolean이나 Number와 같은 객체는 각 원싯값을 감싸는 객체임!
- 타입스크립트에서 일반적으로 boolean이나 number처럼 소문자로 참조하는 것이 모범 사례✨

## 💡 타입 시스템

- 프로그래밍 언어가 프로그램에서 가질 수 있는 타입을 이해하는 방법에 대한 규칙 집합

> **타입 시스템의 기본적인 작동 방식**
>
> 1. 코드를 읽고 존재하는 모든 타입과 값을 이해한다.
> 2. 각 값이 초기 선언에서 가질 수 있는 타입을 확인한다.
> 3. 각 값이 추후 코드에서 어떻게 사용될 수 있는지 모든 방법을 확인한다.
> 4. 값의 사용법이 타입과 일치하지 않으면 사용자에게 오류를 표시한다.

### 🔆 타입 추론 과정

```tsx
let firstName = 'Jade';
firstName.length();
//Errors: This expression is not callable.
// Type 'Number' has no call signatures.
```

1. 코드를 읽고 firstName이라는 변수를 이해한다.
2. 초기값이 “Jade”이므로 firstName이 string 타입이라고 결론 짓는다.
3. firstName의 .length 멤버를 함수처럼 호출하는 코드를 확인한다.
4. string의 .length 멤버는 함수가 아닌 숫자라는 오류를 표시한다. **즉, 함수처럼 호출이 불가능하다.**

## 💡 오류 종류

- **구문 오류** : 타입스크립트가 자바스크립트로 변환되는 것을 **차단**한 경우
- **타입 오류** : 타입 검사기에 따라 일치하지 않는 것이 **감지**된 경우

### 🔆 구문 오류

- 타입스크립트가 **코드로 이해할 수 없는 잘못된 구문을 감지**할 때 발생
- 타입스크립트가 .ts → .js 로의 파일 생성을 차단
- tsc 설정에 따라 자바스크립트 코드를 얻을 수도 있지만 결과가 예상과 다를 수 있다.

```tsx
let let wat;
//Error: ',' expected.
```

> 타입스크립트는 구문 오류와는 상관없이 자바스크립트 코드를 출력하고자 최선을 다하지만, 내가 원하는 출력 결과가 아닐 수 있다. 따라서 출력된 자바스크립트 코드를 실행하기 전에 구문 오류를 수정하는 것이 좋다.

### 🔆 타입 오류

- 타입스크립트의 **타입 검사기가 프로그램의 타입에서 오류를 감지**했을 때 발생
- 오류가 발생했다고 .ts → .js 로의 변환을 차단하지는 않는다.
- 하지만 코드 실행 시의 무언가 충돌하거나 예기치 않게 작동할 수 있음을 나타낸다.

```tsx
console.blub('Hello TypeScript!');
//Error: Property 'blub' does not exist on type 'Console'.
```

- 즉, 타입 오류가 있어도 자바스크립트 코드를 출력할 수 있지만, 출력된 코드가 원하는대로 실행되지 않을 가능성이 있다는 신호를 타입오류로 알려주는 것이다.

# 2. 할당 가능성

### 💡 **할당 가능성**

- 타입스크립트에서 함수 호출이나 변수에 값을 제공할 수 있는지 여부를 확인하는 것
- 즉, 전달된 값이 예상된 타입으로 할당 가능한지 여부를 확인한다.

```tsx
let fruit = 'apple';
fruit = 'banana'; //동일한 타입의 다른 값 할당 OK

fruit = true; //다른 타입의 값 할당 ❌
//Error: Type 'boolean' is not assignable to type 'string'.
```

### 💡 할당 가능성 오류

- `Type...is not assignable to type...` 형태의 자주 접하는 오류
- 첫 번째 type은 코드에서 변수에 할당하려도 시도하는 값
- 두 번째 type은 값이 할당되는 변수

# 3. 타입 애너테이션

- 변수에 타입스크립트가 읽어야할 초깃값이 없는 경우
- 나중에 사용할 변수의 초기 타입을 파악하려고 시도하지 ❌
- 기본적으로 변수를 암묵적인 `any` 타입으로 간주 → 변수는 세상의 모든 것이 될 수 있음

### 💦  초기 타입을 유추할 수 없는 변수, 진화하는 any

- 특정 타입을 강제하는 대신 새로운 값이 할당될 때마다 변수 타입에 대한 이해를 발전시킨다.

```tsx
let rocker; //타입: any

rocker = 'Joan Jett'; //타입: string

rocker.toUpperCase(); //OK

rocker = 19.99; //타입: number
rocker.toPrecision(1); //OK

rocker.toUpperCase();
//Error: Property 'toUpperCase' does not exist on type 'number'.
```

### 💡 타입 애너테이션

- 초깃값을 할당하지 않고도 변수의 타입을 선언할 수 있는 구문
- 변수 이름 뒤에 콜론(:)과 타입 이름을 기재
- 타입을 즉시 유추할 수 있는 변수에도 사용 가능
- **타입스크립트에만 존재**
  - 런타임 코드에 영향 ❌
  - 유효한 자바스크립트 구문도 ❌

```tsx
let rocker: string;
rocker = 'Joan Jett';
```

> 타입 시스템에만 존재하는 것은 컴파일된 자바스크립트로 복사되지 않고 어떠한 영향도 주지 않는다!

### 💡 불필요한 타입 애너테이션

- 타입 애너테이션은 타입스크립트가 자체적으로 수집할 수 없는 정보를 제공할 때 사용한다.
- 변수의 타입을 타입스크립트가 이미 유추할 수 있는 경우, 불필요하게 타입 애너테이션을 사용할 필요가 없다.

```tsx
let str: string = 'apple'; //타입 시스템은 변경되지 ❌
```

- _아무것도 변하지 않는 변수에는 타입 애너테이션을 추가하지 않는 것 추천_

# 4. 타입 형태

- 타입스크립트는 변수에 할당된 값이 원래 타입과 일치하는지 확인하는 것 이상을 수행한다.
- 타입스크립트는 객체에 어떤 멤버 속성이 존재하는 지 알고 있다.
- 변수의 속성에 접근할 때 타입스크립트는 접근하려는 속성이 해당 변수의 타입에 존재하는 지 확인한다.

```tsx
let rapper = 'Queen Latifah';
rapper.length; //ok

rapper.push('!'); // string 타입에서 자동하는지 알 수 없는 작업은 허용 ❌
//Error: Property 'push' does not exist on type 'string'.
```

- 타입스크립트는 객체의 형태에 대한 이해를 바탕으로 할당 가능성뿐만 아니라 객체 사용과 관련된 문제도 알려준다.

```tsx
let fruit = {
  first: 'apple',
  second: 'banana',
};

fruit.third;
//Error: Property 'third' does not exist on type '{ first: string; second: string; }'.
```

## 💡 모듈

### 🔆 ECMA 스크립트 모듈(ESM)

- 자바스크립트의 서로 다른 파일에 작성된 코드를 공유하는 방법
- ECMA 2015에 추가된 파일 간 가져오고 `import` 내보내는 `export` 구문의 표준화

```jsx
import { value } from './values';

export const doubled = value * 2;
```

> **모듈 : export 또는 import가 있는 파일**
>
> **스크립트: 모듈이 아닌 모든 파일**

- 타입스크립트는 최신 모듈 파일을 기존 파일과 함께 실행 가능
- 모듈 파일에서 선언된 것 → export로 내보내지 않는 한 그 파일 내에서만 사용가능
- 한 모듈에서 다른 파일의 선언된 변수와 같은 이름으로 선언된 변수는 다른 파일의 변수를 가져오지 않는 한 이름 충돌이 ❌

```tsx
//a.ts - 모듈
export const named = 'apple';

//b.ts - 모듈
export const named = 'apple';

//c.ts - **가져온 변수 named와 정의된 변수 named의 이름 충돌**
import { named } from '/a';
//Error: Import declaration conflicts with local declaration of "named"

export const named = 'apple';
//Error: Individual declarations in merged declaration
//'named' must be all exported or all local
```

- 파일이 **스크립트**인 경우 타입스크립트는 해당 파일을 **전역 스코프**로 간주하므로 모든 스크립트가 파일의 내용에 접근할 수 있다.
- 즉, 스크립트 파일에서 선언된 변수는 다른 스크립트 파일에 선언된 변수와 같은 이름을 가질 수 없다.

```jsx
//a.ts - 일반 스크립트
const named = 'apple';
//Error: Cannot redeclare block-scoped variable 'named'

//b.ts - 일반 스크립트
const named = 'apple';
//Error: Cannot redeclare block-scoped variable 'named'
```

- 따라서 타입스크립트에서 `Cannot redeclare...` 라는 오류는 export나 import문을 추가하지 않아서일 수 있다.
- ECMA 스크립트 사양에 따라 export나 import문 없이 파일을 모듈로 만들어야 한다면?

```jsx
const named = 'apple';

//export {}; 추가해 강제로 모듈이 되도록 만듬
export {};
```

> ⚠️
>
> 타입스크립트는 CommonJS 같은 이전 모듈 사용법의 import, export 형태는 인식하지 못한다. 일반적으로 CommonJS 스타일의 require 함수 반환 값을 any 타입으로 인식한다.
