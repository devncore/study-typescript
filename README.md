## 타입스크립트 스터디 

이 레포는 TypeScript 공식 사이트에서 제공하는 핸드북 가이드를 통한 스터디 내용을 다루고 있습니다. 

자바스크립트가 탄생한지 20년이 넘도록 규모와 수준은 나날이 발전하고 있는 반면에 Javascript 언어에 대한 발전은 제자리 걸음을 이어오고 있었습니다. 그래서 이를 보완하기 위한 것이 바로 TypeScript 입니다. 

## Contents

- The TypeScript Handbook
- The Basics

## 1. The TypeScript Handbook
[본문 확인](https://www.typescriptlang.org/ko/docs/handbook/intro.html).  

### 핸드북 지침(Rule) 체크 
- [x] 핸드북은 글을 편하게 읽는 것에 포커스를 맞추기 위해 내용의 간결성을 지향한다.
- [x] 타입스크립트를 설명하기 위해 자바스크립트를 상세하게 설명하지 않는다. 타입스크립트에 중점을 두도록 한다.
- [x] 필요한 경우를 제외하고 타입스크립트와 상호작용하는 환경(React, Angular 등)에 대해 설명하지 않는다.

### 타입스크립트와 기존 언어간의 차이점
[TypeScript for JavaScript Programmers](https://www.typescriptlang.org/ko/docs/handbook/typescript-in-5-minutes.html).  

본격적인 챕터로 넘어각 전에 타입스크립트에 대해 간단하게 알아보도록 합시다.

- 타입 추론 (Types by Inference)
- 타입 정의하기 (Defining Types)
- 타입 구성 (Composing Types)
- 구조적 타입 시스템 (Structural Type System)

<br />

------

# 타입 추론 (Types by Inference)

TypeScript는 JavaScript의 코드를 받아들이면서 변수에 값이 할당될때 타입을 자동으로 추론하여 내부적으로 타입을 사용합니다. 그래서 따로 타입을 명시적으로 선언하지 않더라도 내부적으로 타입스크립트의 타입 개념이 적용됩니다.

<br />

# 타입 정의하기 (Defining Types)

TypeScript는 기본적으로 값 할당시 자동으로 타입을 추론하지만 명시적 타입이 필요한 경우 `interface`와 `type` 두 가지 방법을 제공 합니다.<br />
(참고: `interface`를 우선적으로 사용하고 특정 기능이 필요할 때 `type`을 사용해야 합니다)


객체의 형태를 명시적으로 나타내기 위해서는 `interface` 로 선언합니다.

```typescript
interface User {
  name: string;
  id: number;
}
```

이제 변수 선언 뒤에 `: TypeName`의 구문을 사용해 JavaScript 객체가 새로운 `interface`의 형태를 따르고 있음을 선언할 수 있습니다.

```typescript
interface User {
  name: string;
  id: number;
}
// ---cut---
const user: User = {
  name: "Hayes",
  id: 0,
};
```
해당 인터페이스에 맞지 않는 객체를 생성하면 TypeScript는 경고를 줍니다.

```typescript
// @errors: 2322
interface User {
  name: string;
  id: number;
}
const user: User = {
  username: "Hayes",
  id: 0,
};
```

JavaScript는 클래스와 객체 지향 프로그래밍을 지원하기 때문에, TypeScript 또한 동일합니다. 인터페이스는 클래스로도 선언할 수 있습니다.
```typescript
interface User {
  name: string;
  id: number;
}
class UserAccount {
  name: string;
  id: number;
  constructor(name: string, id: number) {
    this.name = name;
    this.id = id;
  }
}
const user: User = new UserAccount("Murphy", 1);
```

인터페이스는 함수에서 매개변수와 리턴 값을 명시하는데 사용되기도 합니다.
```typescript
// @noErrors
interface User {
  name: string;
  id: number;
}
// ---cut---
function getAdminUser(): User {
  //...
}
function deleteUser(user: User) {
  // ...
}
```

JavaScript에서 사용할 수 있는 원시 타입이 이미 있습니다. <br />
`boolean`, `bigint`, `null`, `number`, `string`, `symbol`, `object`와 `undefined`는 인터페이스에서 사용할 수 있습니다.<br />
TypeScript는 몇 가지를 추가해 목록을 확장합니다.<br />
`any` (무엇이든 허용합니다), <br />
`unknown` (무엇이든 허용하지만 타입을 좁혀서 사용해야 하는 의무가 있습니다), <br />
(참고: any와 unknown중 unknown을 사용하는것이 좀 더 안전한 방법입니다)<br />
<br />
`never` (리턴이 없거나 항상예외를 던지는 함수에 사용할 수 있습니다.) <br />
`void` (undefined를 리턴하거나 리턴 값이 없는 함수)<br />
(참고: never는 리턴 자체가 없는 것이고, void는 리턴이 있지만 값이 없는 것이므로 혼동하지 말아야합니다)

<br />

# 타입 구성 (Composing Types)

여러가지 타입을 이용하여 새 타입을 작성하기 위해 일상적인 코드에서 가장 많이 사용되는 두 가지 코드로는 유니언(Union)과 제네릭(Generic)이 있습니다.

### 유니언 (Unions)

유니언은 타입이 여러 타입 중 하나일 수 있음을 선언하는 방법입니다.

유니언 타입이 가장 많이 사용된 사례 중 하나는 값이 다음과 같이 허용되는 `string` 또는 `number`의 리터럴집합을 설명하는 것입니다.

```typescript
type WindowStates = "open" | "closed" | "minimized";
type LockStates = "locked" | "unlocked";
type OddNumbersUnderTen = 1 | 3 | 5 | 7 | 9;
```

유니언은 다양한 타입을 처리하는 방법을 제공하는데, 예를 들어 `array` 또는 `string`을 받는 함수가 있을 수 있습니다.
```typescript
function getLength(obj: string | string[]) {
  return obj.length;
}
```

TypeScript는 코드가 시간에 따라 변수가 변경되는 방식을 이해하며, 이러한 검사를 사용해 타입을 골라낼 수 있습니다.


|Type|Predicate|
|:---|:--------|
|string|typeof s === "string"|
|number|typeof n === "number"|
|boolean|typeof b === "boolean"|
|undefined|typeof undefined === "undefined"|
|function|typeof f === "function"|
|array|Array.isArray(a)|

<br />

예를 들어, `typeof obj === "string"`을 이용하여 `string`과 `array`를 구분할 수 있으며 이로인해 상황에 맞는 인자를 사용하여<br />
함수의 기능을 쉽게 확장할 수 있습니다.

```typescript
function wrapInArray(obj: string | string[]) {
  if (typeof obj === "string") {
    return [obj];
  } else {
    return obj;
  }
}
```

### 제네릭 (Generics)

TypeScript 제네릭은 타입에 변수를 제공하는 방법입니다.

배열이 일반적인 예시이며, 제네릭이 없는 배열은 어떤 것이든 포함할 수 있습니다. 제네릭이 있는 배열은 배열 안의 값을 설명할 수 있습니다.

```typescript
type StringArray = Array<string>;
type NumberArray = Array<number>;
type ObjectWithNameArray = Array<{ name: string }>;
```

제네릭을 사용하는 고유 타입을 선언할 수 있습니다:

```typescript
// @errors: 2345
interface Backpack<Type> {
  add: (obj: Type) => void;
  get: () => Type;
}

// 이 줄은 TypeScript에 `backpack`이라는 상수가 있음을 알리는 지름길이며
// const backpack: Backpack<string>이 어디서 왔는지 걱정할 필요가 없습니다.
declare const backpack: Backpack<string>;

// 위에서 Backpack의 변수 부분으로 선언해서, object는 string입니다.
const object = backpack.get();

// backpack 변수가 string이므로, add 함수에 number를 전달할 수 없습니다.
backpack.add(23);
```

<br />

# 구조적 타입 시스템 (Structural Type System)

TypeScript의 핵심 원칙 중 하나는 타입 검사가 값이 있는 형태에 집중한다는 것입니다. 이는 때때로 “덕 타이핑(duck typing)” 또는 “구조적 타이핑” 이라고 불립니다.

구조적 타입 시스템에서 두 객체가 같은 형태를 가지면 같은 것으로 간주됩니다.

```typescript
interface Point {
  x: number;
  y: number;
}

function printPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}

// "12, 26"를 출력합니다
const point = { x: 12, y: 26 };
printPoint(point);
```

`point`변수는 `Point`타입으로 선언된 적이 없지만, TypeScript는 타입 검사에서 `point`의 형태와 `Point`의 형태를 비교합니다. 둘 다 같은 형태이기 때문에, 통과합니다.<br />

형태 일치에는 일치시킬 객체의 필드의 하위 집합만 필요합니다.

```typescript
// @errors: 2345
interface Point {
  x: number;
  y: number;
}

function printPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}
// ---cut---
const point3 = { x: 12, y: 26, z: 89 };
printPoint(point3); // prints "12, 26"

const rect = { x: 33, y: 3, width: 30, height: 80 };
printPoint(rect); // prints "33, 3"

const color = { hex: "#187ABF" };

printPoint(color);
```

마지막으로, 정확하게 마무리 짓기 위해, 구조적으로 클래스와 객체가 형태를 따르는 방법에는 차이가 없습니다:

```typescript
// @errors: 2345
interface Point {
  x: number;
  y: number;
}

function printPoint(p: Point) {
  console.log(`${p.x}, ${p.y}`);
}
// ---cut---
class VirtualPoint {
  x: number;
  y: number;

  constructor(x: number, y: number) {
    this.x = x;
    this.y = y;
  }
}

const newVPoint = new VirtualPoint(13, 56);
printPoint(newVPoint); // prints "13, 56"
```

객체 또는 클래스에 필요한 모든 속성이 존재한다면, TypeScript는 구현 세부 정보에 관계없이 일치하게 봅니다.
