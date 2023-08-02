## 1. 함수 매개변수

- 함수는 변수와 마찬가지로 타입스크립트를 사용하면 타입 애너테이션으로 함수 매개변수의 타입을 선언할 수 있다.

```tsx
function sing(song: string) {
  console.log(`Singing: ${song}!`);
}
```

- 암시적 any 타입인 매개변수를 타입스크립트의 오류로 설정하는 구성 옵션 시 적절한 타입 애너테이션을 달아주어야 한다.

### 💡 필수 매개변수

- `In JS`→ 인수의 수와 상관없이 함수 호출 가능하다.
- `In TS` → 함수에 선언된 모든 매개변수가 필수이다.
- 함수가 잘못된 수의 인수로 호출되면, 타입스크립트는 **타입 오류의 형태**로 이의를 제기한다.

```tsx
function singTwo(first: string, second: string) {
  console.log(`${first} / ${second}`);
}

singTwo('I will Survie', 'Higher Love'); // Ok

singTwo('1', '2', '3');
//Error: Expected 2 arguments, but got 3.
```

### 💡 선택적 매개변수

- `In JS` → 함수 매개변수가 제공되지 않으면 함수 내부의 인숫값을 `undefined` 기본 설정
- `In TS` → 타입 오류 발생!
- 선택적 매개변수 : 타입 애너테이션의 `:` 앞에 `?`를 추가하면 된다.
- 선택적 매개변수에는 항상 `| undefined`가 유니언 타입으로 추가되어 있다.

```tsx
function announceSong(song: string, singer?: string) {
  console.log(`Song: ${song}`);

  if (singer) {
    console.log(`Singer: ${singer}`);
  }
}

announceSong('Greensleeves'); // Ok
announceSong('Greensleeves', undefined); // Ok
announceSong('Greensleeves', 'Sia'); // Ok
```

- 선택적 매개변수는 항상 암묵적으로 undefined가 될 수 있다.
- 코드에서 singer는 string | undefined 타입으로 시작한 후에 if 문에 따라 string 타입으로 좁혀진다.
- 선택적 매개변수는 `| undefined`를 포함하는 유니언 타입 매개변수와는 다르다.
  - `?`으로 표시된 선택적 매개변수가 아닌 매개변수는 값이 명시적으로 `undefined`일지라도 항상 제공되어야 한다!
- 함수에서 사용되는 모든 선택적 매개변수는 **마지막 매개변수**여야 한다.
- 필수 매개변수 전에 선택적 매개변수를 위치시키면 다음과 같이 타입스크립트 **구문 오류**가 발생!

### 💡 기본 매개변수

- `In JS` → 선택적 매개변수를 선언할 때 `=`와 같이 값이 포함된 기본값을 제공할 수 있다.
- 즉, 선택적 매개변수에는 기본적으로 값이 제공 → 해당 타입스크립트 타입에는 암묵적으로 함수 내부에 `| undefined` 유니언 타입이 추가된다.
- `In TS`
  - 함수의 매개변수에 대해 인수를 누락하거나 `undefined` 인수를 사용해서 호출하는 것을 여전히 허용한다.
  - 매개변수에 기본값이 있고 타입 애너테이션이 없는 경우, 타입스크립트는 해당 기본값을 기반으로 매개변수 타입을 유추한다.

```tsx
function rateSong(song: string, rating = 0) {
  console.log(`${song} gets ${rating}/5 stars!`);
}

rateSong('Photograph');
rateSong('Set Fire to Rain', 5);
rateSong('Set Fire to the Rain', undefined);

rateSong('HI', '100');
//Error: Argument of type 'string' is not assignable to parameter of type 'number'.
```

- rateSong 함수에서 rating은 number 타입으로 유추되지만, 함수를 호출하는 코드에서는 선택적 number | undefined가 된다.

### 💡 나머지 매개변수

`In JS`

- 일부 함수는 임의의 수의 인수로 호출 가능
- `...` 스프레드 연산자
  - 함수 선언의 마지막 매개변수에 위치
  - 해당 매개변수에서 시작해 함수에 전달된 '나머지(rest)' 인수가 모두 단일 배열에 저장되어야 함을 나타냄

`In TS`

- 타입스크립트는 이러한 나머지 매개변수의 타입을 일반 매개변수와 유사하게 선언할 수 있다.
- 단 인수 배열을 나타내기 위해 끝에 `[]` 구문이 추가된다는 점만 다르다.

```tsx
function singAllTheSongs(singer: string, ...songs: string[]) {
  for (const song of songs) {
    console.log(`${song}, by ${singer}`);
  }
}

singAllTheSongs('Alicia Keys'); // Ok
singAllTheSongs('lady Gaga', 'Bad Romance', 'Just Dance', 'Poker Face'); // Ok
```

- singAllTheSongs는 songs 나머지 매개변수에 대해 0개 이상의 string 타입 인수를 사용할 수 있다.

## 2. 반환 타입

- 타입스크립트는 지각적(perceptive)이다.
- 함수가 반환할 수 있는 가능한 모든 값을 이해하면 함수가 반환하는 타입을 알 수 있다.

```tsx
// 타입: (songs: string[], index: number) => string | undefined
function getSongAt(songs: string[], index: number) {
  return index < songs.length ? songs[index] : undefined;
}
```

### 💡 명시적 반환 타입

- 함수에서 반환 타입을 명시적으로 선언하는 방식이 유용할 떄가 있다.

> WHEN?
>
> - 가능한 반환값이 많은 함수에서 항상 동일한 타입의 값을 반환하도록 강제한다.
> - 타입스크립트는 재귀 함수의 반환 타입을 통해 유추하는 것을 거부한다.
> - 수백 개 이상의 타입스크립트 파일이 있는 매우 큰 프로젝트에서 타입스크립트 타입 검사 속도를 높일 수 있다.

```tsx
//함수 선언 반환 타입 애너테이션
function singSongRecursive(songs: string[], count = 0): number {
  return songs.length ? singSongRecursive(songs.slice(1), count + 1) : count;
}

//화살표 함수
const singSongRecursive = (songs: string[], count = 0): number => {
  return songs.length ? singSongRecursive(songs.slice(1), count + 1) : count;
};
```

- 함수의 반환문이 함수의 반환 타입으로 할당할 수 없는 값을 반환하는 경우 타입스크립트는 **할당 가능성 오류**를 표시한다.

## 3. 함수 타입

- `In JS` → 함수를 값으로 전달할 수 있다.
- 즉, 함수를 가지기 위한 매개변수 또는 변수의 타입을 선언하는 방법이 필요하다.
- 함수 타입은 콜백 매개변수(함수로 호출되는 매개변수)를 설명하는 데 자주 사용된다.

```tsx
let nothingInGiveString: () => string;

let inputAndOutput: (songs: string[], count?: number) => number;
```

### 💡 함수 타입 괄호

- 유니언 타입의 애너테이션에서 함수 반환 위치를 나타내거나 유니언 타입을 감싸는 부분을 표시할 때 괄호를 사용한다.

```tsx
// 타입은 string | undefined 유니언을 반환하는 함수
let returnStringOrUndefined: () => string | undefined;

// 타입은 undefined나 string을 반환하는 함수
let maybeReturnString: (() => string) | undefined;
```

### 💡 매개변수 타입 추론

- 매개변수로 사용되는 인라인 함수를 포함하여 작성한 모든 함수에 대해 매개변수를 선언해야 한다면 번거롭다.
- 타입스크립트는 선언된 타입의 위치에 제공된 함수의 매개변수 타입을 유추할 수 있다.

```tsx
let singer: (song: string) => string;

singer = function (song) {
  // song: string의 타입
  return `Singer: ${song.toUpperCase()}!`; // Ok string으로 예측!
};

const songs = ['Call Me', 'Jolene', 'The Chain'];
// song: string
// index: number
songs.forEach((song, index) => {
  console.log(`${song} is at index ${index}`);
});
//함수를 매개변수로 갖는 함수에 인수로 전달된 함수. 해당 매개변수 타입도 잘 유추함
```

### 💡 함수 타입 별칭

- 타입 별칭은 특히 함수 타입에 유용하다
- 타입 별칭을 이용하면 반복적으로 작성하는 매개변수와 반환 타입을 갖는 코드 공간을 많이 절약할 수 있다.

```tsx
type StringToNumber = (input: string) => number;

let stringToNumber: StringToNumber;

stringToNumber = (input) => input.length;
```

## 4. 그 외 반환 타입

### 🔆 void 반환 타입

- return 문이 없는 함수이거나 값을 반환하지 않는 return 문을 가진 함수일 경우 함수는 어떤 값도 반환하지 않는다.
- `void` 키워드를 사용해 반환 값이 없는 함수의 반환 타입을 확인할 수 있다.
- 함수 타입 선언 시 void를 사용하면 함수에서 반환되는 모든 값은 무시된다.

### 💦 void는 undefined와 다르다.

- `In JS` → 함수에 실젯값이 반환되지 않는다 == 기본이 모두 undefined 반환
- void는 함수의 반환 타입이 무시된다는 것을 의미한다.
- undefined는 반환되는 리터럴 값이다.

```tsx
function returnsVoid() {
  return;
}

let lazyValue: string | undefined;

lazyValue = returnsVoid();
//Error: Type '() => void' is not assignable to type 'string'.
```

💦 undefiend와 void를 구분해서 사용하면 매우 유용

- 특히 void를 반환하도록 선언된 타입 위치에 전달된 함수가 반한된 모든 값을 무시하도록 설정할 때 유용하다.
- saveRecords 함수의 records.push(record)는 number(배열의 `.push`에서 반환된 값)를 반환하지만, 여전히 newRecords.forEach에 전달된 화살표 함수에 대한 반환값이 허용된다.

```tsx
const records: string[] = [];

function saveRecords(newRecords: string[]) {
  //Array<string>.forEach(callbackfn: (value: string, index: number, array: string[]) => void, thisArg?: any): void
  //Array<string>.push(...items: string[]): number
  newRecords.forEach((record) => records.push(record));
}

saveRecords(['21', 'Come On Over', 'The Bodyguard']);
```

- void 타입은 자바스크립트가 아닌 함수의 반환 타입을 선언하는 데 사용하는 타입스크립트 키워드다.
- **void 타입은 함수의 반환값이 자체적으로 반환될 수 있는 값도 아니고, 사용하기 위한 것도 아니라는 표시를 기억해야 한다.**

### 🔆 never 반환 타입

- never 반환 함수는 (의도적으로) 항상 오류를 발생시키거나 무한 루프를 실행하는 함수다.
- 함수가 절대 반환하지 않도록 의도하려면 명시적 `: never` 타입 애너테이션을 추가해 해당 함수를 호출한 후 모든 코드가 실행되지 않음을 나타낸다.

```tsx
function fail(message: string): never {
  throw new Error(`Invariat failure: ${message}.`);
}
//fail 함수는 오류만 발생시키므로 param의 타입을 string으로 좁혀서
//타입스크립트의 제어 흐름 분석을 도와준다.
function workWithUnsageParam(param: unknown) {
  if (typeof param !== 'string') {
    fail(`param should be a string, not ${typeof param}`);
  }

  // 여기에서 param의 타입은 string으로 알려진다.
  param.toUpperCase();
}
```

> 💦 `never`는 `void`와 다르다. **void**는 아무것도 반환하지 않는 함수를 위한 것이고, **never**는 절대 반환하지 않는 함수를 위한 것이다.

## 5. 함수 오버로드

- 함수 오버로드(Function Overloads): 같은 이름의 함수에 대해 서로 다른 시그니처를 정의하는 기능이다.
- 하나의 최종 `구현 시그니처`와 그 함수의 본문 앞에 서로 다른 버전의 함수 이름, 매개변수, 반환 타입(오버로드 시그니처)을 여러 번 선언한다.
- 오버로드된 함수 호출에 대해 구문 오류를 생성할지 여부를 결정할 때 타입스크립트는 함수의 오버로드 시그니처만 확인한다.
- 구현 시그니처는 함수의 내부 로직에서만 사용된다.

```tsx
// 오버로드 시그니처
function createDate(timestamp: number): Date;
function createDate(month: number, day: number, year: number): Date;

// 구현 시그니처
function createDate(monthOrTimeStamp: number, day?: number, year?: number) {
  return day === undefined || year === undefined
    ? new Date(monthOrTimeStamp)
    : new Date(year, monthOrTimeStamp, day);
}

createDate(554356800); // Ok
createDate(7, 27, 1987); // Ok
```

> 💦 함수 오버로드는 복잡하고 설명하기 어려운 함수 타입에 사용하는 최후의 수단이다. 함수를 단순하게 유지하고 가능하면 함수 오버로드를 사용하지 않는 것이 좋다.

### 💡 호출 시그니처 호환성

- 함수의 오버로드 시그니처에 있는 반환 타입과 각 매개변수는 구현 시그니처에 있는 동일한 인덱스의 매개변수에 할당할 수 있어야 한다.
- 즉, 구현 시그니처는 모든 오버로드 시그니처와 호환되어야 한다.

```tsx
function format(data: string): string;
function format(data: string, needle: string, haystack: string): string;

function format(getData: () => string): string;
//Error: This overload signature is not compatible with its implementation
//signature.

function format(data: string, needle?: string, haystack?: string) {
  return needle && haystack ? data.replace(needle, haystack) : data;
}
```
