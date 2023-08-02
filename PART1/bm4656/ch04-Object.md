## 객체 타입

> 객체 리터럴은 각자의 타입이 있는 키와 집합이다.

- null과 undifined를 제외한 모든 값은 그 값에 대한 실제 타입의 멤버 집합을 가진다.

### 별칭 객체 타입

- 각 객체 타입에 타입 별칭을 할당해 사용하는게 일반적이다.
- 대부분의 `interface`로 객체 타입을 설명하는 것을 선호하기는 한다.
- 이 둘은 대부분의 기능이 동일하다.

```tsx
type Poet = {
  born: number;
  name: string;
};
```

## 구조적 타이핑

- 타입스크립트의 타입 시스템은 구조적으로 타입화(Structurally typed)되어 있다.
- 구조적 타이핑은 상속 계층과 타입의 이름에 관계없이 객체의 내부 구조를 기준으로 타입 호환성을 결정한다.
- 객체가 어떤 속성과 메서드를 가지고 있는지를 기준으로 타입 호환성을 판단한다.
- 명시적으로 인터페이스나 클래스를 상속받지 않더라도, 같은 구조를 가진 객체는 서로 호환 가능하다고 간주한다.

```tsx
type Person = {
  name: string;
  age: number;
};

const person1: Person = { name: 'John', age: 30 };
const person2 = { name: 'Alice', age: 25 };

function printPerson(person: Person) {
  console.log(person.name, person.age);
}

printPerson(person1); // 정상 동작
printPerson(person2); // 정상 동작, 구조적 타이핑에 의해 호환됨
```

- 유연한 코드 작성이 가능하며, 재사용성과 유지 보수성을 높일 수 있다.
- 하지만 이러한 유연성 때문에 때로는 의도치 않은 호환성이 발생할 수 있으므로, 타입스크립트는 strict 모드 등을 통해 타입 체크를 강화할 수 있다.

> 자바스크립트는 **덕 타입,** 타입스크립트는 **구조적으로 타입화**

- 덕 타이핑 : 동적 타이핑의 한 종류로 객체의 변수 및 메서드의 집합이 객체의 타입을 결정하는 것
- 타입스크립트의 타입 검사기에서 구조적 타이핑은 정적 시스템이 타입을 검사하는 경우다.
- 덕 타이핑은 런타임에서 사용될 때까지 객체 타입을 검사하지 않는 것을 말한다.

### 사용 검사

- 객체 타입으로 애너테이션된 위치에 값을 제공할 때 TS는 해당 객체 타입에 할당할 수 있는 지 확인한다.
- 할당하는 값에는 객체의 필수 속성이 반드시 있어야한다. → 없으면 오류남

### 초과 속성 검사

- 변수가 객체 타입으로 선언되고, 초깃값에 객체 타입에서 정의된 것보다 많은 필드가 있다면 타입 오류가 발생한다.

```tsx
type Poet = {
  born: number;
  name: string;
};

const extraProperty: Poet = {
  activity: 'walking',
  //Error: Type '{ activity: string; born: number; name: string; }' is not assignable to type 'Poet'.
  // Object literal may only specify known properties, and 'activity' does not exist in type 'Poet'.
  born: 1935,
  name: 'Mary',
};
```

- 초과 속성 검사는 실수로 잘못된 속성을 객체에 할당하는 것을 방지하여 코드의 안정성을 높이는데 도움을 준다.
- 객체 타입으로 선언된 위치에서 생성되는 객체 리터럴에 대해서만 일어난다. → 기존 객체 리터럴을 제공하면 초과 속성 검사를 우회한다.

```tsx
const existingObject = {
  activity: 'walking',
  born: 1935,
  name: 'Mary',
};

const extraPropertyButOK: Poet = existingObject;
```

### 중첩된 객체 타입

- 타입 시스템에서 중첩된 객체를 나타낼 수 있다.
- 중첩된 객체 타입을 고유한 타입 이름으로 바꿔서 사용하면 코드와 오류 메시지를 더 읽기 쉽다.

### 선택적 속성

- 타입의 속성 애너테이션에서 `:` 앞에 `?`를 추가하여 나타낼 수 있다.
- ❗️선택적 속성과 undifined를 포함한 유니언 타입의 속성엔 차이가 있다!
- undifined는 그 값이 존재해야함!

## 객체 타입 유니언

- 타입스크립트 코드에서는 속성이 조금 다른, 하나 이상의 서로 다른 객체 타입이 될 수 있는 타입을 설명할 수 있어야 한다.
- 속성값을 기반으로 해당 객체 타입 간에 타입을 좁혀야할 수도 있다

### 유추된 객체 타입 유니언

- 변수에 여러 객체 타입 중 하나가 될 수 있는 초깃값이 주어지면 타입스크립트는 해당 타입을 객체 타입 유니언으로 유추한다.
- 객체 타입에 정의된 각각의 가능한 속성은 비록 초깃값이 없는 선택적(`?`) 타입이지만 각 객체 타입의 구성 요소로 주어진다.

```tsx
const poem =
  Math.random() > 0.5
    ? { name: 'The Double', pages: 7 }
    : { name: 'Her Kind', rhymes: true };

poem.name; // string
poem.pages; // number | undefined
poem.rhymes; // boolean | undefined
```

### 명시된 객체 타입 유니언

- 객체 타입의 조합을 명시하면 객체 타입을 더 명확히 정의할 수 있다.
- 코드를 조금 더 작성해야 하지만 객체 타입을 더 많이 제어할 수 있다.

```tsx
type PoemWithPages = {
  name: string;
  pages: number;
};
type PoemWithRhymes = {
  name: string;
  rhymes: boolean;
};

type Poem = PoemWithPages | PoemWithRhymes;

const poem: Poem =
  Math.random() > 0.5
    ? { name: 'The Double Image', pages: 7 }
    : { name: 'Her Kind', rhymes: true };
```

- 리터럴 타입이나 원시 타입 모두, 혹은 둘 중 하나로 이루어진 유니언 타입에서 모든 타입에 존재하지 않는 속성에 접근하기 위해 타입을 좁여야 하는 것처럼 객체 타입 유니언도 타입을 좁혀야 한다.

### 객체 타입 내로잉

- 타입 검사기가 유니언 타입 값에 특정 속성이 포함된 경우에만 코드 영역을 실행할 수 있음을 알게 되면, 값의 타입을 해당 속성을 포함하는 구성 요소로만 좁힌다.
- 즉, 코드에서 객체의 형태를 확인하고 타입 내로잉이 객체에 적용된다.

```tsx
if ('pages' in poem) {
  poem.pages; // Ok: poem은 PoemWithPages로 좁혀짐
} else {
  poem.rhymes; // Ok: poem은 PoemWithPages로 좁혀짐
}
```

- 타입스크립트 `if (poem.pages)` 와 같은 형식으로 참 여부를 확인하는 것을 허용하지 ❌
- **존재하지 않는 객체 속성에 접근 → 타입 가드처럼 작동하는 방식으로 사용되더라도 타입 오류!**

### 판별된 유니언

- 객체의 속성이 객체의 형태를 나타내도록 하는 것 → 판별된 유니언
- 판별값: 객체의 타입을 가리키는 속성
- TS는 코드에서 판별 속성을 사용해 타입 내로잉을 수행한다.

```tsx
type PoemWithPages = {
  name: string;
  pages: number;
  type: 'pages';
};
type PoemWithRhymes = {
  name: string;
  rhymes: boolean;
  type: 'rhymes';
};

//Poem 타입은 PoemWithPages 탸입 또는 PoemWithRhymes 타입 둘 다 될 수
//있는 객체를 설명하고 type 속성으로 어느 타입인지를 나타낸다.
type Poem = PoemWithPages | PoemWithRhymes;

const poem: Poem =
  Math.random() > 0.5
    ? { name: 'The Double Image', pages: 7, type: 'pages' }
    : { name: 'Her Kind', rhymes: true, type: 'rhymes' };

if (poem.type === 'pages') {
  console.log(`It's got pages: ${poem.pages}`); // Ok
} else {
  console.log(`It's rhymes: ${poem.rhymes}`);
}

poem.type; // 타입: 'pages' | 'rhymes'

poem.pages; //Error: Property 'pages' does not exist on type 'Poem'.
//Property 'pages' does not exist on type 'PoemWithRhymes'.
```

## 교차 타입

> 타입스크립트에서 교차 타입(Intersection Types)은 여러 타입을 하나로 결합하여 새로운 타입을 만드는 방법

- 자바스크립트의 런타임 `|` 연산자가 `&` 연산자에 대응하는 역할을 하는 것처럼, 타입스크립트에서도 `& 교차타입 (intersection type)`을 사용해 여러 타입을 동시에 나타낸다.
- 교차 타입은 일반적으로 여러 기존 객체 타입을 별칭 객체 타입으로 결합해 새로운 타입을 생성한다.

```tsx
type Artwork = {
  genre: string;
  name: string;
};
type Writing = {
  pages: number;
  name: string;
};

type WrittenArt = Artwork & Writing;

// {
//    genre: string;
//    name: string;
//    pages: number;
// }
```

- 교차 타입은 유니온 타입과 결합할 수 있다.

```tsx
type ShortPoem = { author: string } & (
  | { kigo: string; type: 'haiku' }
  | { meter: number; type: 'villanelle' }
);

// Ok
const morningGlory: ShortPoem = {
  author: 'Fukuda Chiyo-ni',
  kigo: 'Morning Glory',
  type: 'haiku',
};
```

- ShortPoem 타입은 항상 author 속성을 가지며 하나의 type 속성으로 판별된 유니언 타입이다.

### 교차 타입의 위헙성

**긴 할당 가능성 오류**

- 유니언 타입과 결합하는 것처럼 복잡한 교차 타입을 만들게 되면 할당 가능성 오류 메시지는 읽기 어려워진다.
- 타입을 일련의 별칭으로 된 객체 타입으로 분할하면 읽기가 훨씬 쉬워진다.

**never**

- 교차 타입은 잘못 사용하기 쉽고 불가능한 타입을 생성한다.
- 원시 타입의 값은 동시에 여러 타입이 될 수 없기 때문에 교차 타입의 구성 요소로 함께 결합할 수 없다.
- 두 개의 원시 타입을 함께 시도하면  `never` 타입이 된다.

```tsx
type NotPossible = number & string; // 타입: never
```

- never 키워드와 never 타입은 프로그래밍 언어에서 bottom 타입 또는 empty 타입을 뜻한다.
- bottom 타입은 값을 가질 수 없고 참조할 수 없는 변수 타입이므로 bottom 타입에 그 어떠한 타입도 제공할 수 없다. → 제공하면 오류!
- `never` 타입을 거의 사용하지 않지만 코드에서 불가능한 상태를 나타내기 위해 가끔 등장한다.
