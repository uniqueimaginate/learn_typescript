# Interfaces

TypeScript 중요한 원칙 중에 하나가 타입 체크가 값의 형태에 주목한다는 것이다.

이를 "duck typing", "structural subtyping" 이라고 한다.

```tsx
interface LabeledValue {
  label: string;
}

function printLabel(labeledObj: LabeledValue) {
  console.log(labeledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

위의 코드를 보면, 분명 `myObj` 가 `LabeldValue` 인터페이스와 형태가 다른다. 그러나 인터페이스의 string 을 포함하고 있기 때문에 문제가 없다. 

### Optional Properties

- 있어도 되고 없어도 되는 프로퍼티
- 인터페이스에 정의되어 있지 않은 프로퍼티를 사용하려고 한다면, 에러가 뜬다.
- 즉, 정의되어 있는 프로퍼티는 있어도 되고 없어도 되지만, 정의 되지 않은 프로퍼티를 사용하게 되면 바로 에러가 뜬다.

```tsx
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = { color: "white", area: 100 };
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({ color: "black" });
```

### Readonly Properties

```tsx
interface Point {
  readonly x: number;
  readonly y: number;
}

let p1: Point = {x: 10, y: 15};
p1.x = 5; // Error!

let ro: ReadonlyArray<number> = a;
// 특아한 Array 이다.
// 읽는 것 빼고는 아무것도 못한다.
// 심지어 readonlyArray 가 아닌 것을 할당하지도 할당 받지도 않는다.
```

- `readonly` vs `const`

    `readonly` 는 프로퍼티에서 사용되고 `const` 는 변수에서 사용된다.

### Excess Property Checks

```tsx
interface SquareConfig{
	color?: string;
	width?: number;
}

function createSquare(config: SquareConfig): {color: string, area: number} {
	return {
		color: config.color || "red",
		area: config.width ? config.width * config.width : 20,
	};
}

let mySquare = createSquare( { colour: "red", width: 100 } );
```

위의 코드를 보자, Optional Properties 에서 보았던 예시이다. 가장 아랫줄을 보면, interface 에서 정의한 `color` 프로퍼티가 아닌, `colour` 로 오타가 발생했다. JS 의 경우, 이 오류는 조용히 일어나지만, TS에서는 오류를 일으킨다. 따라서 Type Check 를 분명히 해야한다.

```tsx
let mySquare = createSquare( {width: 100, opacity: 0.5} as SquareConfig );
```

위와 같이 Type Assertion 을 사용해, 오류를 없앨 수 있다.

그러나 더 나은 방안이 있다.

```tsx
interface SquareConfig {
  color?: string;
  width?: number;
  [propName: string]: any;
}

let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

이렇게 될 경우 `colour` 라는 오타가 발생해도 컴파일러에서 오류가 발생하지 않는다. 이는 extra properties 를 받게 했기 때문이다. 그러나 이 때도 `width` 라는 common property 가 있기 때문에 괜찮은 것이다. common property 가 하나라도 없으면 바로 오류가 난다.

### Function Types

개인적으로 좀 신기한 기능인 것 같다.

이 인터페이스는 함수의 타입을 결정해 준다. 인터페이스에 Call signature 을 부여한다.

```tsx
interface SearchFunc {
	(source: string, subString: string): boolean;
}

let mySearch: SearchFunc;

mySearch = function(source: string, subString: string): boolean{
	let result = source.search(subString);
	return result >= -1;
}
```

위의 코드를 보면 `mySearch` 는 SearchFunc interface 가 가지는 function type 을 그대로 갖고 있다. 프로퍼티의 타입과 함수의 리턴 타입이 같다. 여기서 프로퍼티의 이름은 달라도 가능하다.

즉,

```tsx
mySearch = function(src: string, sub: string): boolean{
	let result = src.search(sub);
	return result >= -1;
}
```

위의 코드가 가능하다는 이야기이다.

또한 타입을 생략해도 된다.

```tsx
mySearch = function(src, sub){
	let result = src.search(sub);
	return result >= -1;
}
```

물론, 나라면 타입을 생략하지는 않을 것 같다.

### Indexable Types

Index 를 가지는 타입으로 인터페이스를 선언할 수도 있다.

배열, 맵과 같은 형태를 가질 수 있다는 이야기이다.

```tsx
interface StringArray {
    [idx: number]: string;
  }
  
  let myArray: StringArray;
  myArray = ["Bob", "Fred"];
  
  let myStr: string = myArray[0];
```

문자열 index, 숫자열 index 모두를 가질수 있다.

그러나 이 때, 숫자열 index 타입은 문자열 index 타입의 서브 타입이어야 한다는 것이다.

```tsx
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

// Error: indexing with a numeric string might get you a completely separate type of Animal!
interface NotOkay {
  [x: number]: Animal;
  [x: string]: Dog;
}
```

위의 코드는 오류가 난다.

```tsx
interface Animal {
  name: string;
}

interface Dog extends Animal {
  breed: string;
}

interface NotOkay {
  [x: number]: Dog;
  [x: string]: Animal;
}
```

따라서 다음과 같이 숫자열 타입을 Dog 라는 Animal 의 서브타입으로 두면 에러가 발생하지 않는다.

이 에러는 JS 의 특징 때문인데, 객체의 index 에 접근할 때, 내부적으로는 숫자가 아니라 문자열로 변형된 값을 index 로 사용하기 때문이다. 

```tsx
interface NumberDictionary {
  [index: string]: number;
  length: number; // ok, length is a number
  name: string; // error, the type of 'name' is not a subtype of the indexer
}
```

```tsx
interface NumberOrStringDictionary {
  [index: string]: number | string;
  length: number; // ok, length is a number
  name: string; // ok, name is a string
}
```

두 코드의 차이점이 보이는가?

위의 첫번째 코드는 index type 이 숫자이다. 따라서 인터페이스 프로퍼티는 모두 숫자 타입이 강제된다. 따라서 오류가 발생한다. 

위의 두번째 코드는 오류가 발생하지 않는다. index type 이 숫자와 문자 모두를 가지는 유니언 프로퍼티이기 때문이다.

Readonly 도 가능하다.

```tsx
interface ReadonlyStringArray {
  readonly [index: number]: string;
}

let myArray: ReadonlyStringArray = ["Alice", "Bob"];
myArray[2] = "Mallory"; // error!
```

위의 코드는 오류를 발생한다. 

인터페이스 프로퍼티에 readonly 가 있기 때문이다.

### Class Types

class 가 반드시 구현하게끔 한다. 영어로 되어 있는 글을 어떻게 번역해야 할지 모르겠다.

```tsx
interface ClockInterface {
  currentTime: Date;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  constructor(h: number, m: number) {}
}
```

객체지향언어에서는 뭐 당연한 소리이다.

```tsx
interface ClockInterface {
  currentTime: Date;
  setTime(d: Date): void;
}

class Clock implements ClockInterface {
  currentTime: Date = new Date();
  setTime(d: Date) {
    this.currentTime = d;
  }
  constructor(h: number, m: number) {}
}
```

위와 같이 함수도 구현할 수 있다.

### Class 의 Static 부분과 Instance 부분의 차이.

- 이 부분은 아직 정확히 이해가 되지 않는다.. ㅠ

```tsx
interface ClockConstructor {
  new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
  currentTime: Date;
  constructor(h: number, m: number) {}
}
```

위의 코드는 에러가 난다.  constructor 가 static 부분에 해당하기 때문이다.

일단 예시만 나둔다.

```tsx
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface {
  tick(): void;
}

function createClock(
  ctor: ClockConstructor,
  hour: number,
  minute: number
): ClockInterface {
  return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
}

class AnalogClock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("tick tock");
  }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

```tsx
interface ClockConstructor {
  new (hour: number, minute: number): ClockInterface;
}

interface ClockInterface {
  tick(): void;
}

const Clock: ClockConstructor = class Clock implements ClockInterface {
  constructor(h: number, m: number) {}
  tick() {
    console.log("beep beep");
  }
};

let clock = new Clock(12, 17);
clock.tick();
```

위의 2 코드는 모두 에러 없이 잘 작동한다. 

### Extending Interfaces

Class 와 마찬가지로 Interface 도 확장 시킬 수 있다.

```tsx
interface Shape {
  color: string;
}

interface Square extends Shape {
  sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10
```

여러 개의 인터페이스를 가질 수 있다.

```tsx
interface Shape {
  color: string;
}

interface PenStroke {
  penWidth: number;
}

interface Square extends Shape, PenStroke {
  sideLength: number;
}

let square = {} as Square;
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### Hybrid Types

객체가 함수로도, 객체로서의 역할을 한다.

```tsx
interface Counter {
  (start: number): string;
  interval: number;
  reset(): void;
}

function getCounter(): Counter {
  let counter = function (start: number) {} as Counter;
  counter.interval = 123;
  counter.reset = function () {};
  return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;s
```

### Interfaces Extending Classes

private, protected 멤버가 있는 클래스를 extend 하는 인터페이스는 오직 해당하는 클래스의 서브클래스만 인터페이스를 implement 할 수 있다.

말이 어려운데, 아래의 예시를 통해 설명하겠다.

```tsx
class Control {
  private state: any;
}

interface SelectableControl extends Control {
  select(): void;
}

class Button extends Control implements SelectableControl {
  select() {}
}

class TextBox extends Control {
  select() {}
}

class ImageControl implements SelectableControl {
  private state: any;
  select() {}
}
// ImageControl 에서 에러가 발생한다.
```

클래스 Control 은 private member 를 가지고 있다.

Selectable Control Interface 는 Control 클래스를 extend 한다.

Button  클래스는 SelectableControl 을 implement 하고 Control 클래스를 extend 한다.

이에 반해 ImageControl 클래스는 SelectableControl 을 implement 하지만 Control 클래스를 extend 하지 않기 때문에 에러가 발생한다. ImageControl 이 에러를 발생하지 않게 하기 위해서는 Control 클래스를 extend 하고 private state 를 지우면 된다.

```tsx
class ImageControl extends Control implements SelectableControl {
  select() {}
}
```