### 요약

- 자바스트립트의 간략한 역사
- 자바스크립트의 함정 : 값 비싼 자유, 부족한 문서, 부족한 개발자 도구
- 프로그래밍 언어, 타입 검사기, 컴파일러 및 언어 서비스 역할을 하는 타입 스크립트
- 타입스크립트의 장점 : 제한을 통한 자유로움, 정확한 문서화, 강력한 개발자 도구
- 타입스크립트에 대한 오해

## 1. 자바스크립트의 역사

- 1995년 넷스케이프의 브렌던 아이크가 설계
- 이후 발전하며 ECMA스크립트(JS의 기반이 되는 언어 사양)를 2015년 부터 매년 출시
  - 다른 최신 프로그래밍 언어에서 제공하는 기능도 함께 제공
  - 브라우저와 임베디드 애플리케이션 그리고 서버 런타임을 포함환 다양한 환경에서 새로운 버전과 이전 버전과의 호환성을 수십 년 동안 유지

## 2. 바닐라 자바스크립트의 함정

- `바닐라(vanilla)` 언어의 확장이나 프레임없이 자바스크립트를 사용하는 것 == 순수 자바스크립트

### 💡 값비싼 자유

- ‘자바스크립트에서 코드를 구성하는 방법에 제한이 없다’ → 사용하는 개발자들 불만
- 높은 자유도로 프로젝트를 시작하면 재밌지만, 파일이 늘어날 수록 안전한 코드 실행에 멀어져서 고통받는다.
- 자바스크립트는 동적 타입 언어. 즉, 충돌 가능성을 먼저 확인하지 않고 코드 실행함

### 💡 부족한 문서

- 자바스크립트의 언어 사양에는 함수의 매개변수, 함수 반환, 변수 또는 다른 구성 요소의 의미를 설명하는 표준화된 내용이 없다.
- 따라서 다수의 개발자들은 `JSDoc` 표준을 채택
- `JSDoc`: 표준으로 형식화된 함수와 변수 코드 바로 위에 문서 주석을 작성하는 방식

**BUT, JSDoc의 주요 문제들로 규모가 큰 코드베이스에서는 사용하기 불편하다.**

- JSDoc 설명이 코드가 잘못되는 것을 막을 수 없다.
- JSDoc 설명이 이전에는 정확했더라도 코드 리팩터링 중에 생긴 변경 사항과 관련된 현재 유효하지 않은 JSDoc 주석을 모두 찾기란 어렵다.
- 복잡한 객체를 설명할 때는 다루기 어렵고 장황해서 타입과 그 관계를 정의하려면 다수의 독립형 주석이 필요하다.

### 💡 부족한 개발자 도구

- 자바스크립트는 타입을 식별하는 내장된 방법을 제공하지 않는다.
- 코드가 JSDoc 주석에서 쉽게 분리되기 때문에 코드베이스에 대한 대규모 변경을 자동화하거나 통찰력을 얻기가 매우 어렵다.

## 3. 타입스크립트

- 타입스크립트는 2010년대 초에 마이크로소프트 내부에서 만들어진 후 2012년에 출시 및 오픈 소스화되었다.
- `자바스크립트의 상위 집합` or `타입이 있는 자바스크립트`

**타입스크립트는 네 가지 핵심**

- `프로그래밍 언어` : 자바스크립트의 모든 구문과, 타입을 정의하고 사용하기 위한 새로운 타입스크립트 고유 구문이 포함된 언어
- `타입 검사기` : 자바스크립트 및 타입스크립트로 작성된 일련의 파일에서 생성된 모든 구성 요소(변수, 함수 등)를 이해하고, 잘못 구성된 부분을 알려주는 프로그램
- `컴파일러` : 타입 검사기를 실행하고 문제를 보고한 후 이에 대응되는 자바스크립트 코드를 생성하는 프로그램
- `언어 서비스` : 타입 검사기를 사용해 비주얼 스튜디오 코드(VS Code)와 같은 편집기에 개발자에게 유용한 유틸리티 제공법을 알려주는 프로그램

### 💡 제한을 통한 자유

- 타입스크립트를 사용하면 매개변수와 변수에 제공되는 값의 타입을 지정할 수 있다.
- 코드를 지정한 방법으로만 사용하도록 제한 → 코드의 한 영역을 변경하더라도 다른 영역이 멈추지 않는다는 확신
- 예를 들어 함수의 매개변수 개수를 변경했을 경우, 변경된 함수를 호출하는 코드를 업데이트하지 않았다면 타입스크립트가 알려준다.

### 💡 정확한 문서화

```tsx
interface Painter {
  finish(): boolean;
  ownMaterials: Material[];
  paint(painting: string, materials: Material[]): boolean;
}

function paintPainting(painter: Painter, painting: string): boolean {
  /* ... */
}
```

- Painter에 적어도 세 가지 속성이 있고, 그 중 두 가지는 메서드라는 것을 알 수 있다.
- 타입스크립트는 구문을 적용해 객체의 ‘형태’를 설명하고, 우수하고 강력한 시스템을 이용해 객체가 어떻게 보이는지 설명한다.

### 💡더 강력한 개발자 도구

- VS code 등의 편집기에서 타입스크립트의 똑똑한 제안을 확인할 수 있다.

## 3. 타입스크립트에 대한 오해

- 모든 도구가 그렇듯이 타입스크립트도 몇 가지 제약이 있다.

### 💡 잘못된 코드 해결책

- 타입스크립트는 자바스크립트 코드를 구조화하는 데 도움이 되지만, 타입 안정성 강화를 제외하고는 해당 구조가 어떻게 보여야 하는지에 대해서는 어떤 것도 강요하지 않는다.
- 특정 대상만을 위한 독단적인 프레임워크가 아닌 모든 개발자가 사용할 수 있는 프로그래밍 언어다.
- 클래스나 함수 사용 여부와 같은 코드 스타일 의견을 강요하지 않으며, 앵귤러, 리액트 등의 특정 애플리케이션 프레임워크와도 연관되어 있지 않다.

### 💡 자바스크립트로의 확장

- 타입스크립트는 자바스크립트의 작동 방식을 전혀 변경하지 않는다.

### 💡 자바스크립트보다 느림

- 타입스크립트가 코드에 적용하는 유일한 변경 사항은 인터넷 익스플로러 11과 같이 오래된 런타임 환경을 지원하기 위해 이전 버전의 자바스크립트로 코드를 컴파일하도록 요청하는 경우
- 운영 프레임워크 대다수는 타입스크립트의 컴파일러를 전혀 사용하지 않는다.
- 그러나 타입스크립트는 코드를 빌드하는 데 시간이 조금 더 걸린다.
- 타입스크립트 코드는 브라우저 Node.js와 같은 환경에서 실행되기 전에 자바스크립트로 컴파일되어야 한다.
- 코드에서 발생할 수 있는 오류를 분석하는 느린 타입스크립트 기능은 **실행 가능한 애플리케이션 코드 파일을 생성하는 것과는 분리된 채로 수행**된다.

### 💡 진화가 끝남

- 웹 커뮤니티는 타입스크립트에 버그 수정과 기능 추가를 지속적으로 요청한다.
- 타입스크립트의 기본 원칙은 거의 변함이 없겠지만, 오류 메시지, 더 멋진 기능 그리고 편집기와의 통합은 시간이 지남에 따라 개선될 것이다.
