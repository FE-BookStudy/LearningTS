> 타입스크립트는 초기 배열에 어떤 데이터 타입이 있는 지 기억하고, 배열이 해당 데이터 타입에서 작동하도록 제한한다.

## 1. 배열 타입

- `type[]`

```tsx
let arrayOfNumber: number[];

arrayOfNumber = [1, 2, 3];
```

### 💡 배열과 함수 타입

- 배열 타입은 함수 타입에 무엇이 있는지 구별하는 괄호가 필요한 구문 컨테이너의 예

```tsx
//타입은 string 배열을 반환하는 함수
let createStrings: () => string[];

//타입은 각각의 string을 반환하는 함수 배열
let stringCreators: (()=> string))[];
```

### 💡 유니언 타입 배열

- 배열의 각 요소가 여러 선택 타입 중 하나일 수 있음을 나타내려면 유니온 타입을 사용한다.

```tsx
//타입은 string, 또는 number의 배열
let stringOrArrayOfNumbers: string | number[];

//타입은 각각 number 또는 string인 요소의 배열
let arrayOfStringOrNumber: (string | number)[];
```

### 💡 any 배열의 진화

- 초기 빈배열 → any[] 취급, 점점 타입 변경될 수 있다.
- 변수와 마찬가지로 배열이 any 타입이 되도록 허용하거나 일반적으로 any 타입을 사용하도록 허용하면 타입스크립트의 타입 검사 목적을 부분적으로 무효화한다.. 안좋아…

### 💡 다차원 배열

- 2차원 배열 or 배열의 배열은 두 개의 [] 대괄호를 갖는다.

```tsx
let arrayOfArraysOfNumbers: number[][];
//or
let arrayOfArraysOfNumbers: number[][];
```

## 2. 배열 멤버

- 타입스크립트는 배열의 멤버를 찾아서 해당 배열의 타입 요소를 되돌려주는 전형적인 인덱스 기반 접근 방식을 이해한다.

```tsx
const defenders = ['cl', 'di'];

//타입: string
const defender = defenders[0];

const nameAndDate = ['KIM', new Date(1999, 9, 9)];

//타입: string | Date
const who = nameAndDate[0];
```

### 💡 주의사항: 불안정한 멤버

- 배열은 타입 시스템에서 불안정한 소스다.
- 타입스크립트는 검색된 배열의 멤버가 존재하는 지 의도적으로 확인하지 않는다.

```tsx
function withElements(elements: string[]) {
  console.log(elements[9001].length); //타입 오류 없음 string 타입으로 간주됨.
}
withElements(["It's", 'Over']);
```

## 3. 스프레드와 나머지 매개변수

### 🔆 스프레드

- `…` 스프레드 연산자를 사용해 배열을 결합한다.

```tsx
//타입 string[]
const people = ['Nana', 'Momo', 'Luna'];

//타입 number[]
const peopleAge = [20, 21, 17];

//타입: (string | number)[]
const conjoined = [...people, ...peopleAge];
```

### 🔆 나머지 매개변수 스프레드

- 나머지 매개변수를 위한 인수로 사용되는 배열은 동일한 타입을 가져야한다. → 너무 당연함.

## 4. 튜플

> 고정된 크기의 배열을 나타내는 튜플 타입

```tsx
let tupleExample: [number, string];

tupleExample = [1, 'tuple'];
//크기와 각 인덱스의 타입이 동일해야만 타입 오류가 안남
```

### 💡 튜플 할당 가능성

- 가변 길이의 배열 타입은 튜플 타입에 할당할 수 없다.
- 나머지 매개변수로서의 튜플도 사용 가능하다.

### 💡 튜플 추론

- 타입스크립트는 배열이 생성되면 가변 길이의 배열로 취급한다.
  - 변수의 초깃값
  - 함수의 반환값
- 구체적인 튜플 타입이어야함을 두가지 방법으로 나타낸다.
  - 명시적 튜플 타입
  - const 어시션

### 🔆 명시적 튜플 타입

- 타입 애너테이션으로 튜플 타입을 선언할 수 있다.
- 함수가 튜플 타입을 반환한다고 선언 → 반환되는 배열 리터럴은 튜플로 간주한다.

```tsx
//반환 타입: [string, number]
function firstCharAndSizeExplicit(input: string): [string, number] {
  return [input[0], input.length];
}
// firstChar : string
//size : numbert
const [firstChar, size] = firstCharAndSizeExplicit('안녕 타스야');
```

### 🔆 const 어시션

- `as const` 연산자 : 타입스크립트에 타입을 유추할 때 읽기 전용이 가능한 값 형식을 사용하도록 한다.
- 배열 리터럴 뒤에 const 어시션을 사용하면 배열이 튜플로 처리되어야 함을 나타낸다.

```tsx
//타입: (string : number)[]
const unionArray = [1157, 'Tomoe'];

//타입: readonly [1157, "Tomee"]
const readonlyTuple = [1157, 'Tomoe'] as const;
```

- ❗️const 어시션은 해당 튜플이 읽기 전용이며 값 수정이 예상되는 곳에서 사용할 수 없음을 나타낸다.
- 읽기 전용 튜플은 함수 반환에 편리하다.
