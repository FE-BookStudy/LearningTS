# Interface

> 내장 타입 형태가 아니라 직접 만드는 타입.

- 인터페이스는 연관된 이름으로 객체 형태를 설명하는 또 다른 방법이다.
- 별칭으로 된 객체 타입과 여러 면에서 유사하지만 일반적으로 더 읽기 쉬운 오류 메시지, 더 빠른 컴파일러 성능, 클래스와의 더 나은 상호 운용성을 위해 선호된다.

## 1. 타입 별칭 vs 인터페이스

- 타입 별칭

```tsx
type Poet = {
  born: number;
  name: string;
};
```

- 인터페이스

```tsx
interface Poet {
  born: number;
  name: string;
}
```

- 둘은 거의 동일하다.
- 할당 가능성 검사와 오류 메시지도 거의 동일하게 동작한다.

> `세미콜론(;)`을 선호하는 타입스크립트 개발자들 → 인터페이스 뒤가 아닌 타입 별칭 뒤에 세미콜론. 이 기본 설정은 세미콜론을 사용해 변수를 선언하는 것과 세미콜론 없이 클래스 또는 함수를 선언하는 것의 차이를 반영함

### 💦 그러면 **인터페이스와 타입 별칭의 차이점은?**

- 인터페이스는 속성 증가를 위해 병합할 수 있다.
  - 이 기능은 내장된 전역 인터페이스 또는 npm 패키지와 같은 외부 코드를 사용할 때 특히 유용하다.
- 인터페이스는 클래스가 선언된 구조의 타입을 확인하는 데 사용할 수 있지만 타입 별칭은 사용할 수 없다.
- 일반적으로 인터페이스에서 타입스크립트 타입 검사기가 더 빨리 작동한다.
  - 인터페이스는 타입 별칭이 하는 것처럼 새로운 객체 리터럴의 동적인 복사 붙여넣기보다 내부적으로 더 쉽게 캐시할 수 있는 명명된 타입을 선언한다.
- 인터페이스는 이름 없는 객체 리터럴의 별칭이 아닌 명명된 객체로 간주되므로 어려운 특이 케이스에서 나타나는 오류 메시지릴 비교적 쉽게 읽을 수 있다.

> 마지막 두가지 이유와 **일관성을 유지**하기 위해 기본적으로 별칭 객체 형태에 대한 인터페이스를 사용하는 것을 추천한다. 타입 별칭의 유니언 타입과 같은 기능이 필요할 때까지는 인터페이스를 사용하는 것이 좋다.

## 2. 속성 타입

### 🔆 선택적 속성

- 타입 애너테이션 `:` 앞에 `?`를 사용해 인터페이스의 속성이 선택적 속성임을 나타낼 수 있다.

```tsx
interface Book {
  author?: string;
  pages: number;
}

// Ok
const ok: Book = {
  author: 'Rita Dove',
  pages: 80,
};
const missing: Book = {
  pages: 80,
};
```

- undefiend를 포함한 유니언 타입의 선택적 속성과 일반 속성 사이의 차이점과 관련된 주의 사항은 객체 타입뿐만 아니라 인터페이스에도 적용된다.

### 🔆 읽기 전용 속성

- 타입스크립트는 속성 이름 앞에 readonly 키워드를 추가해 다른 값으로 설정될 수 없음을 나타낸다.
- 읽을 수는 있지만 재할당이 불가능하다.

```tsx
interface Page {
  readonly text: string;
}

function read(page: Page) {
  //OK: text 속성을 수정하지 않고 읽음
  console.log(page.text);

  page.text += '!';
  //Error:Cannot assign to 'text' because it is a read-only property.
}
```

- readonly 제한자는 타입 시스템에만 존재하며 인터페이스에서만 사용할 수 있다.
- readonly 제한자는 객체의 인터페이스를 선언하는 위치에서만 사용되고 실제 객체에는 적용되지 않는다.

```tsx
const pageIsh = {
  text: 'Hello, world!',
};

// Ok: pageIsh는 Page 객체가 아니라 text가 있는, 유추된 객체 타입이다.
pageIsh.text += '!';

// Ok: pageIsh의 더 구체적인 버전인 Page를 읽는다.
read(pageIsh);
```

- readonly 인터페이스 멤버는 코드 영역에서 객체를 의도치 않게 수정하는 것을 막는 편리한 방법이다.
- 그러나 단지 타입스크립트 타입 검사기를 사용해 개발 중에 그 속성이 수정되지 못하도록 보호하는 역할을 할 뿐 출력 코드에는 존재하지 않는다.

### 🔆 함수와 메서드

- 타입스크립트에서 인터페이스 멤버를 함수로 선언하는 두 가지 방법

> 💡 자바스크립트에서 객체를 함수로 선언하는 방법과 동일하다.
>
> - **메서드 구문** : 인터페이스 멤버를 member(): void와 같이 객체의 멤버로 호출되는 함수로 선언
> - **속성 구문** : 인터페이스의 멤버를 member: () => void와 같이 독립 함수와 동일하게 선언

```tsx
interface HasBothFunctionTypes {
  property: () => string;
  method(): string;
}

const hasBoth: HasBothFunctionTypes = {
  property: () => '',
  method() {
    return '';
  },
};

hasBoth.property(); // Ok
hasBoth.method(); // Ok
```

- 선택적 속성 키워드 `?` 도 사용 가능하다.
- 메서드와 속성 선언은 대부분 서로 교환하여 사용할 수 있다.

**메서드와 속성의 차이점은?**

- 메서드는 readonly로 선언할 수 없지만 속성은 가능하다.
- 인터페이스 병합은 메서드와 속성을 다르게 처리한다.
- 타입에서 수행되는 일부 작업은 메서드와 속성을 다르게 처리한다.

> 본 책에서는 기본함수가 this를 참조할 수 있다는 것을 알고 있다면 메서드(일반적으로 클래스의 인스턴스에서 사용). 반대의 경우 속성함수를 사용하라고 추천.

### 💡 호출 시그니처

### 💡 인덱스 시그니처

### 💡 중첩 인터페이스

- 객체 타입이 다른 객체 타입의 속성으로 중첩될 수 있는 것처럼 인터페이스 타입도 자체 인터페이스 타입 혹은 객체 타입을 속성으로 가질 수 있다.

```tsx
interface Novel {
  author: {
    name: string;
  };
  setting: Setting;
}

interface Setting {
  place: string;
  year: number;
}

let myNovel: Novel;

// Ok
myNovel = {
  author: {
    name: 'Jane Austen',
  },
  setting: {
    place: 'England',
    year: 1812,
  },
};
```

## 3. 인터페이스 확장

- 타입스크립트는 인터페이스가 다른 인터페이스의 모든 멤버를 복사해서 선언할 수 있는 확장된 인터페이스를 허용한다.
- 확장할 인터페이스의 이름 뒤에 `extends` 키워드를 추가!

```tsx
interface Writing {
  title: string;
}

interface Novella extends Writing {
  pages: number;
}

let myNovella: Novella = {
  pages: 195,
  title: 'Ethan Frome',
};
```

- 인터페이스 확장은 프로젝트의 한 엔티티 타입이 다른 엔티티의 모든 멤버를 포함하는 상위 집합을 나타내는 실용적인 방법이다.
- 여러 인터페이스 관계를 나타내기 위해 동일한 코드를 반복 입력하는 작업을 피할 수 있다.

### 💡 재정의된 속성

- 파생 인터페이스는 다른 타입으로 속성을 다시 선언해 기본 인터페이스의 속성을 재정의(override) 하거나 대체할 수 있다.
- 타입스크립트의 타입 검사기는 재정의된 속성이 기본 속성에 할당되어 있도록 강요한다.

```tsx
interface WithNullableName {
  name: string | null;
}

interface WithNonNullableName extends WithNullableName {
  name: string; //or null도 됨
}

interface WithNumericName extends WithNonNullableName {
  //Error: Interface 'WithNumericName' incorrectly
  //extends interface 'WithNonNullableName'.
  //Types of property 'name' are incompatible.
  //Type 'string | number' is not assignable to type 'string'.
  //Type 'number' is not assignable to type 'string'.
  name: string | number;
}
```

- 속성을 재선언하는 대부분의 파생 인터페이스는 해당 속성을 유니언 타입의 더 구체적이 하위 집합으로 만들거나 속성을 기본 인터페이스의 타입에서 확장된 타입으로 만들기 위해 사용한다.

### 💡 다중 인터페이스 확장

- 타입스크립트의 인터페이스은 여러 개의 다른 인터페이스를 확장해서 선언할 수 있다.
- 파생 인터페이스 이름에 있는 extends 키워드 뒤에 쉼표로 인터페이스 이름을 구분해 사용한다.

```tsx
interface GivesNumber {
  giveNumber(): number;
}

interface GivesString {
  giveString(): string;
}

interface GivesBothAndEither extends GivesNumber, GivesString {
  giveEither(): number | string;
}

function useGivesBoth(instance: GivesBothAndEither) {
  instance.giveEither(); // 타입: number | string
  instance.giveNumber(); // 타입: number
  instance.giveString(); // 타입: string
}
```

- 여러 인터페이스를 확장하는 방식으로 인터페이스를 사용하면 코드 중복을 줄이고 다른 코드 영역에서 객체의 형태를 더 쉽게 재사용할 수 있다.

## 4. 인터페이스 병합

- 인터페이스의 중요한 특징 중 하나는 서로 **병합**하는 능력이다.
- 두 개의 인터페이스가 동일한 이름으로 동일한 스코프에 선언된 경우, 선언된 모든 필드를 포함하는 더 큰 인터페이스가 코드에 추가된다.

```tsx
interface Merged {
  fromFirst: string;
}

interface Merged {
  fromSecond: number;
}

// 다음과 같음:
// interface Merged {
// fromFirst: string;
// fromSecond: number;
// }
```

- 인터페이스가 여러 곳에 선언되면 코드를 이해하기가 어려워지므로 가능하면 인터페이스 병합을 사용하지 않는 것이 좋다.(일반적으로 사용하지 않음)
- 그러나 외부 패키지 또는 Window 같은 내장된 전역 인터페이스를 보강하는데 특히 유용하다.

### 💡 이름이 충돌되는 멤버

- 병합된 인터페이스는 타입이 다른 동일한 이름의 속성을 여러 번 선언할 수 없다.
- 속성이 이미 인터페이스에 선언되어 있다면 나중에 병합된 인터페이스에도 동일한 타입을 사용해야 한다.

```tsx
interface MergedProperties {
  same: (input: boolean) => string;
  different: (input: string) => string;
}
interface MergedProperties {
  same: (input: boolean) => string;
  different: (input: number) => string;
  //Error: Subsequent property declarations must have the same type.
  //Property 'different' must be of type '(input: string) => string',
  //but here has type '(input: number) => string'.
}
```

- 하지만 병합된 인터페이스느 동일한 이름과 다른 시그니처를 가진 메서드는 정의할 수 있다.
  - 메서드에 대한 함수 오버로드가 발생!

```tsx
nterface MergedMethods {
  different(input: string): string;
}

interface MergedMethods {
  different(input: number): string; // Ok
}
```
