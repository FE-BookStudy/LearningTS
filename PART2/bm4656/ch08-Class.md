# 클래스

- 타입스크립트의 타입 검사기 이외의 트랜스파일러의 강조의 예시로 클래스가 자주 등장하였다.

## 1. 클래스 메서드

- 타입스크립트는 독립 함수를 이해하는 것과 동일한 방식으로 메서드를 이해한다.
- 매개변수 → 타입이나 기본값 지정 없을 시 any
- 메서드 호출 시 허용 가능한 수의 인수 필요, 재귀함수 이외 대부분 반환 타입 유추 가능

```tsx
class Greeter {
  greet(name: string) {
    console.log(`${name}, do you stuff!`);
  }
}
new Greeter().greet('bomin'); //Ok
new Greeter().greet();
//Error: Expected 1 arguments, but got 0
```

- 클래스 생성자도 전형적인 메서드처럼 취급된다.

```tsx
class Greeter {
  constructor(name: string) {
    console.log(`${name}, do you stuff!`);
  }
}
new Greeter('bomin'); //Ok
new Greeter();
//Error: Expected 1 arguments, but got 0
```

## 2. 클래스 속성

- 타입스크립트에서 클래스 속성을 읽고 쓰려면 클래스에 명시적으로 선언해야 한다.
- `클래스 속성 이름 : 타입;`

```tsx
class FieldTrip {
  destination: string;

  constructor(destination: string) {
    this.destination = destination; //Ok
    this.nonexistent = destination;
    //Error: Property 'nonexistent' does not exist on type 'FieldTrip'.
    //클래스 속성'nonexistent'을 선언하지 않았기 떄문에 할당이 허용되지 않음.
  }
}
```

- 클래스 속성을 명시적으로 선언하면?
  - 타입스크립트가 클래스 인스턴스에서 무엇이 허용되고, 허용되지 않는가를 빠르게 이해할 수 있다.
  - 클래스 인스턴스에 존재하지 않는 멤버에 접근 시 타입 오류!

### 💡 함수 속성

- `In JS` 클래스의 멤버를 호출 가능한 함수로 선언하는 두가지 구문

1. 메서드 접근 방식

- 함수를 클래스 프로토타입에 할당
- 모든 클래스 인스턴스는 동일한 함수 정의 사용

```tsx
class WithMethod {
  myMethod() {}
}
```

2. 값이 함수인 속성을 선언하는 방식

- 클래스의 인스턴스당 새로운 함수가 생성
- 항상 클래스 인스턴스를 가리켜야하는 화살표 함수에서 this 스코프를 사용하면 클래스 인스턴스 당 새로운 함수를 생성하는 시간과 메모리 비용 측면에서 유용

```tsx
class WithProperty {
  myProperty: () => {}; // ()=>void 타입
}
```

- 함수 속성은 클래스 멤버로 할당된 값이고, 그 값은 함수다.

```tsx
class WithPropertyParameters {
  takesParameters = (input: boolean) => (input ? 'yes' : 'No');
}

const instance = new WithPropertyParameters();

instance.takesParameters(true); //Ok
instance.takesParameters(123);
//Error : Argument of type 'number' is not assignable to parameter of type 'boolean'.
```

### 💡 초기화 검사

- 엄격한 컴파일러 설정이 활성화 된 상태에서 `undefined` 타입으로 선언된 각 속성이 생성자에서 할당되었는지 확인한다.
- 엄격한 초기화 검사 → 클래스 속성에 값을 할당하지 않는 실수 예방

```tsx
class WithValue {
  immediate = 0;
  later: number;
  mayBeUndefiend: number | undefined;
  unused: number;
  //Error: Property 'unused' has no initializer and
  //is not definitely assigned in the constructor.

  constructor() {
    this.later = 1;
  }
}
```

- 엄격한 초기화 검사가 없다면?
  - 타입 시스템이 undifined 값에 접근 할 수 없다고 말해도 접근은 할 수 있다.
  - 하지만 컴파일 결과 자바스크립트 런타임 시 문제 발생!

```tsx
class MissingInitializer {
  property: string;
}

new MissingInitializer().property.length;
//TypeError: Cannot read property 'length' of undifined.
```

### 💦 확실하게 할당된 속성

- 엄격한 초기화 검사를 적용하면 안되는 속성인 경우 이름 뒤에 `!` 추가해 검사를 비활성화하도록 설정할 수 있다.
- 이러면 타입스크립트에 속성이 처음 사용되기 전에 undifined 값이 할당된다.

```tsx
class ActivitiesQueue {
  pending!: string[]; // '!'를 사용하여 나중에 반드시 할당된다고 컴파일러에게 알려준다.

  initialize(pending: string[]) {
    this.pending = pending; // 초기화 메서드를 통해 pending 속성에 값을 할당
  }

  next() {
    return this.pending.pop(); // pending 배열에서 마지막 요소를 제거하고 반환
  }
}

const activities = new ActivitiesQueue(); // ActivitiesQueue 클래스의 인스턴스를 생성

activities.initialize(['eat', 'sleep', 'learn']); // initialize 메서드를 사용하여 pending 배열을 초기화
activities.next(); // pending 배열의 마지막 요소('learn')를 제거하고 반환
```

- ActivitiesQueue 클래스는 생성자와는 별도로 여러 번 다시 초기화될 수 있으므로 pending 속성은 !와 함께 할당되어야한다.

> 🌱 ! 어서션을 추가하고 속성에 대한 타입 안정성을 줄이는 대신 클래스를 리팩터링해서 더이상 어서션이 필요하지 않도록 하자!

### 💡 선택적 속성

- 클래스 또한 속성 이름 뒤에 `?`를 추가해 속성을 옵션으로 선언할 수 있다.
- 선택적 속성은 `| undefined` 를 포함하는 유니언 타입과 거의 동일하게 작동한다.
- 엄격한 초기화 검사와 관계없이 클래스 생성자에서 할당하지 않아도 된다.

### 💡 읽기 전용 속성

- 속성 이름 뒤에 `readonly` 키워드를 추가해 읽기 전용으로 만들 수 있다.
- `readonly` 키워드는 타입 시스템에서만 존재 → JS 컴파일 시 삭제
- 선언된 위치 또는 생성자에서 초깃값만 할당할 수 있다.
- 나머지에선 다 읽을 수 만 있고 쓸 수 없다.
- `readonly`는 클래스의 속성을 보호하고 불변성을 유지하기 위해 사용될 수 있으며, 코드의 안정성과 가독성을 향상시키는 데 도움이 될 수 있다.

```tsx
class Person {
  readonly name: string; // 읽기 전용 속성

  constructor(name: string) {
    this.name = name; // 생성자에서 초기화
  }

  changeName(newName: string) {
    // this.name = newName; // 오류! 읽기 전용 속성이므로 수정 불가능
    console.log('Cannot change name.');
  }
}

const person = new Person('Alice');
console.log(person.name); // "Alice"

person.name = 'Bob'; // 오류! 읽기 전용 속성이므로 수정 불가능
person.changeName('Carol'); // "Cannot change name."
```

- 원시 타입의 초깃값을 갖는 readonly로 선언된 속성은 좀 다르다던데?
- 값의 타입이 가능한 한 좁혀진 리터럴 타입으로 유추되기 떄문!
  - 값이 나중에 변하지 않는다는 걸 알기에 더 공격적인 초기 타입 내로잉!

```tsx
class RandomQuote {
  readonly explicit: string = 'Home is';
  readonly implucit = 'Home is';

  constructor() {
    if (Math.random() > 0.5) {
      this.explicit = 'We start';
      this.implucit = 'We start';
      //Error: Type '"We start"' is not assignable to type '"Home is"'.
    }
  }
}

const quote = new RandomQuote();

quote.explicit; //타입: string
quote.implucit; //타입: "Home is"
```

## 3. 타입으로서의 클래스

- 타입 시스템에서 클래스는 클래스 선언이 런타임 값(클래스 자체)과 타입 애너테이션에서 사용할 수 있는 타입을 모두 생성한다.

```tsx
class Teacher {
  sayHello() {
    console.log('Take chance!');
  }
}

let teacher: Teacher;

teacher = new Teacher();
teacher = 'wahoo!';
//Error: Type 'string' is not assignable to type 'Teacher'.
```

- 타입스크립트는 클래스의 동일한 멤버를 모두 포함하는 모든 객체 타입을 클래스에 할당할 수 있는 것으로 간주한다.
  - 타입스크립트의 구조적 타이핑이 선언되는 방식이 아니라 객체의 형태만 고려하기 때문.

```tsx
class SchoolBus {
  getAbilities() {
    return ['magic', 'shapeshifting'];
  }
}

function withSchoolBus(bus: SchoolBus) {
  console.log(bus.getAbilities());
}

withSchoolBus(new SchoolBus()); //ok

//ok
withSchoolBus({
  getAbilities: () => ['trans'],
});

withSchoolBus({
  getAbilities: () => 123,
});
//Error: Type 'number' is not assignable to type 'string[]'.
```

## 6. 추상 클래스

- 메서드의 구현을 선언하지 않고, 대신 하위 클래스가 해당 메서드를 제공할 것을 **예상**하고 기본 클래스를 만드는 방법
- 추상화하려는 클래스 이름과 메서드 앞에 타입스크립트 `abstract` 키워드 추가
- 이러한 추상화 메서드 선언은 추상화 기본 클래스에서 메서드의 본문을 제공하는 것을 건너뛰고, 대신 인터페이스와 동일한 방식으로 선언된다.

```tsx
abstract class School {
  readonly name: string;

  constructor(name: string) {
    this.name = name;
  }

  abstract getStudentTypes(): string[];
}

class Preschool extends School {
  getStudentTypes() {
    return ['preschooler'];
  }
}

class Absence extends School {}
//Error: Non-abstract class 'Absence'
//does not implement all abstract members of 'School'
```

- School 클래스와 getStudentTypes 메서드는 abstract로 표시된다.
- 그러므로 하위 클래스인 Preschool과 Absence는 getStudentTypes를 구현해야 한다.

```tsx
let school: School;

school = new Preschool('hi'); //Ok

school = new School('what');
//Error: Cannot create an instance of an abstract class.
```

- 구현이 존재한다고 가정할 수 있는 일부 메서드에 대한 정의가 없기 때문에 추상 클래스를 직접 인스턴스화할 수 없다. → 추상 클래스가 아닌 클래스만 인스턴스화할 수 있다.
- 따라서 추상 클래스는 클래스의 세부 사항이 채워질 거라 예상되는 프레임워크에서 자주 사용된다.

## 7. 멤버 접근성

`In JS`

- 클래스 멤버 이름 앞에 `#`을 추가해 private 클래스 멤버로 지정한다.
- private 클래스 멤버는 해당 클래스 인스턴스에서만 접근 가능
- 자바스크립트 런타임 → 클래스 외부 코드 영역에서 private 메서드나 속성에 접근 시 오류 발생시켜 프라이버시 강화

`In TS`

- 타입스크립트의 클래스 지원은 자바스크립트늬 `#`보다 먼저 만들어졌다.
- private 클래스 멤버를 지원하지만 타입시스템에만 존재하는 클래스 메서드와 속성이 있다.
- 타입스크립트의 멤버 접근성(가시성)은 클래스 멤버의 선언 이름 앞에 다음 키워드 하나를 추가해 만든다.
  | public(기본값) | 모든 곳에서 누구나 접근 가능                 |
  | -------------- | -------------------------------------------- |
  | protected      | 클래스 내부 또는 하위 클래스에서만 접근 가능 |
  | private        | 클래스 내부에서만 접근 가능                  |
- 순수하게 타입 시스템 내에 존재하며, 코드가 자바스크립트로 컴파일되면 다른 모든 타입 시스템 구문과 함께 키워드도 제거된다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/718f0ea2-f20e-4146-bdf6-033cb6e53250/Untitled.png)

- protected 또는 private으로 선언된 타입스크립트 클래스 멤버는 명시적으로 또는 암묵적으로 public으로 선언된 것처럼 동일한 자바스크립트 코드로 컴파일된다.
- 자바스크립트 런타임에서는 # private 필드만 진정한 private이다.

### 정적 필드 제한자

- 자바스크립트는 `static` 키워드를 사용해 클래스 자체에서 멤버를 선언한다.
- 타입스크립트는 `static` 키워드를 단독으로 사용하거나 readonly와 접근성 키워드를 함께 사용할 수 있도록 지원한다.
  - 접근성 키워드를 먼저 작성하고, 그 다음 static, readonly 키워드 순

```tsx
class Question {
  protected static readonly answer: 'bash';
  protected static readonly prompt =
    "What's an ogre's favorite programming language?";

  guess(getAnswer: (prompt: string) => string) {
    const answer = getAnswer(Question.prompt);

    // Ok
    if (answer === Question.answer) {
      console.log('You got it!');
    } else {
      console.log('Try again...');
    }
  }
}

Question.answer;
//Error: Property 'answer' does not exist on type 'typeof Question'.
```

- 해당 필드가 해당 클래스 외부에서 접근되거나 수정되는 것을 제한하는 데 유용하다.
