# Classes

### Classes

```tsx
class Greeter {
    greeting: string;

    constructor(message: string){
        this.greeting = message;
    }

    greet(){
        return `Hello, ${this.greeting}`
    }
}

let greeter = new Greeter("Hello")
```

### Inheritance

```tsx
class Animal {
  move(distanceInMeters: number = 0) {
    console.log(`Animal moved ${distanceInMeters}m.`);
  }
}

class Dog extends Animal {
  bark() {
    console.log("Woof! Woof!");
  }
}

const dog = new Dog();
dog.bark();
dog.move(10);
dog.bark();
```

```tsx
class Animal {
    name: string;
    constructor(theName: string) {
      this.name = theName;
    }
    move(distanceInMeters: number = 0) {
      console.log(`${this.name} moved ${distanceInMeters}m.`);
    }
  }
  
  class Snake extends Animal {
    constructor(name: string) {
      super(name);
    }
    move(distanceInMeters = 10) {
      console.log("Slithering...");
      super.move(distanceInMeters);
    }
  }
  
  class Horse extends Animal {
    constructor(name: string) {
      super(name);
    }
    move(distanceInMeters = 45) {
      console.log("Galloping...");
      super.move(distanceInMeters);
    }
  }
  
  let sam = new Snake("Sammy the Python");
  let tom: Animal = new Horse("Tommy the Palomino");
  
  sam.move();
  tom.move(34);
```

두 번째 코드 블럭을 보면, `tom` 은 `Animal` 객체로 선언되었지만 값은 `Horse` 객체 이기 때문에 `Horse` 객체에서 override 한 메서드를 사용한다.

### Public, private, and protected modifiers

**Public by default**

명시적으로 public 키워드를 써도 되지만, 키워드가 없더라도 기본적으로 public 이다.

**ECAMScript Private Fields**

TS 3.8 부터, 새로운 JS 문법을 도입했다.

# private fields

```tsx
class Animal {
  #name: string;
  constructor(theName: string) {
    this.#name = theName;
  }
}

new Animal("Cat").#name; // 에러가 발생한다.
```

`private` 으로 선언된 멤버는 해당 객체 밖에서 접근할 수 없다.

### Understanding TypeScript's private

```tsx
class Animal {
  private name: string;
  constructor(theName: string) {
    this.name = theName;
  }
}

class Rhino extends Animal {
  constructor() {
    super("Rhino");
  }
}

class Employee {
  private name: string;
  constructor(theName: string) {
    this.name = theName;
  }
}

let animal = new Animal("Goat");
let rhino = new Rhino();
let employee = new Employee("Bob");

animal = rhino;
animal = employee; // 에러가 발생한다.
```

위의 코드를 보면 `Animal` 객체와 `Employee` 객체는 모든 멤버들의 타입이 동일하다.

그러나 `private` 키워드로 인해서 `animal` 에 `employee` 를 할당 할 수 없다. 

위와 같은 현상은 `protected` 키워드에도 적용된다.

### Understanding protected

```tsx
class Person {
  protected name: string;
  constructor(name: string) {
    this.name = name;
  }
}

class Employee extends Person {
  private department: string;

  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`;
  }
}

let howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
console.log(howard.name);  // 에러가 발생한다.
```

`protected` 키워드로 선언된 멤버들은 해당 객체와 상속 받는 객체 안에서만 접근 할 수 있고, 밖에서는 접근 할 수 없다.

```tsx
class Person {
  protected name: string;
  protected constructor(theName: string) {
    this.name = theName;
  }
}

// Employee can extend Person
class Employee extends Person {
  private department: string;

  constructor(name: string, department: string) {
    super(name);
    this.department = department;
  }

  public getElevatorPitch() {
    return `Hello, my name is ${this.name} and I work in ${this.department}.`;
  }
}

let howard = new Employee("Howard", "Sales");
let john = new Person("John");  // 에러가 발생한다.
```

위와 같이 `constructor` 도 `protected` 로 선언 될 수 있다. 이 때 생성자는 상속받는 객체에서 호출 할 수 있으며 밖에서는 호출 할 수 없다.

### Readonly modifier

```tsx
class Octopus {
  readonly name: string;
  readonly numberOfLegs: number = 8;

  constructor(theName: string) {
    this.name = theName;
  }
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name = "Man with the 3-piece suit";  // 에러가 발생한다.
```

`readonly` 키워드를 이용해서 멤버를 선언할 수 있다.

이 때 선언된 멤버는 선언시 값을 할당하거나 생성자에서 할당해야 한다.

### Parameter properties

```tsx
class Octopus {
  readonly numberOfLegs: number = 8;
  constructor(readonly name: string) {}
}

let dad = new Octopus("Man with the 8 strong legs");
dad.name;
```

위의 코드를 보면 이전 코드에서 살짝 변형 된 것을 알 수 있다. 

생성자 안에 `readonly name: string` 이 들어가 있는 것을 볼 수 있다.

이는 이전 코드와 동일한 역할을 한다.

`Octopus` 객체에서 `readonly` 멤버인 `name` 을 선언하고 할당하는 생성자이다.

위의 코드는 다음의 코드로도 바뀔 수 있다.

```tsx
class Octopus{
	constructor(readonly numberOfLegs:number = 8, readonly name: string){}
}

let dad = new Octopus(undefined, "Man with the 8 strong legs");
dad.name
```

### Accessors

`get, set` 키워드를 이용해보자

```tsx
class Employee {
  fullName: string;
}

let employee = new Employee();
employee.fullName = "Bob Smith";

if (employee.fullName) {
  console.log(employee.fullName);
}
```

```tsx
const fullNameMaxLength = 10;

class Employee {
  private _fullName: string = "";

  get fullName(): string {
    return this._fullName;
  }

  set fullName(newName: string) {
    if (newName && newName.length > fullNameMaxLength) {
      throw new Error("fullName has a max length of " + fullNameMaxLength);
    }

    this._fullName = newName;
  }
}

let employee = new Employee();
employee.fullName = "Bob Smith";

if (employee.fullName) {
  console.log(employee.fullName);
}
```

`accessors` 는 ECMAScript 5 이상을 요구한다.

`get` 은 있지만 `set` 이 없다면 이는 바로 `readonly` 가 된다.

### Static Properties

```tsx
class Grid {
    static origin = { x: 1, y: 0 };
  
    calculateDistanceFromOrigin(point: { x: number; y: number }) {
      let xDist = point.x - Grid.origin.x;
      let yDist = point.y - Grid.origin.y;
      return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
  
    constructor(public scale: number) {}
  }
  
  let grid1 = new Grid(1.0); // 1x scale
  let grid2 = new Grid(5.0); // 5x scale
  
  console.log(grid1.calculateDistanceFromOrigin({ x: 10, y: 10 }));
  console.log(grid2.calculateDistanceFromOrigin({ x: 10, y: 10 }));
```

`static` 멤버는 객체가 꼭 인스턴스화 될 필요가 없이, 그 자체로 객체 내에 있어 접근 할 수 있다.

```tsx
Grid.origin.x;
```

### Abstract Classes

사용하는 다른 클래스에서 베이스가 되는 클래스이다.

직접 인스턴스화는 불가능하다.

인터페이스와는 다르게 멤버를 직접 구현할 수 있다. 

메서드에 `abstract` 키워드가 붙으면 사용하는 다른 클래스에서 구현해야 한다.

```tsx
abstract class Animal {
  abstract makeSound(): void;

  move(): void {
    console.log("roaming the earth...");
  }
}
```

```tsx
abstract class Department {
  constructor(public name: string) {}

  printName(): void {
    console.log("Department name: " + this.name);
  }

  abstract printMeeting(): void; // must be implemented in derived classes
}

class AccountingDepartment extends Department {
  constructor() {
    super("Accounting and Auditing"); // constructors in derived classes must call super()
  }

  printMeeting(): void {
    console.log("The Accounting Department meets each Monday at 10am.");
  }

  generateReports(): void {
    console.log("Generating accounting reports...");
  }
}

let department: Department; // ok to create a reference to an abstract type
department = new Department(); // error: cannot create an instance of an abstract class
department = new AccountingDepartment(); // ok to create and assign a non-abstract subclass
department.printName();
department.printMeeting();
department.generateReports(); // error: department is not of type AccountingDepartment, cannot access generateReports
```

### Constructor functions

Instance 관점

```tsx
class Greeter {
  greeting: string;

  constructor(message: string) {
    this.greeting = message;
  }

  greet() {
    return "Hello, " + this.greeting;
  }
}

let greeter: Greeter;
greeter = new Greeter("world");
console.log(greeter.greet()); // "Hello, world"
```

```tsx
let Greeter = (function () {
  function Greeter(message) {
    this.greeting = message;
  }

  Greeter.prototype.greet = function () {
    return "Hello, " + this.greeting;
  };

  return Greeter;
})();

let greeter;
greeter = new Greeter("world");
console.log(greeter.greet()); // "Hello, world"
```

Static 관점

```tsx
class Greeter {
  static standardGreeting = "Hello, there";
  greeting: string;
  greet() {
    if (this.greeting) {
      return "Hello, " + this.greeting;
    } else {
      return Greeter.standardGreeting;
    }
  }
}

let greeter1: Greeter;
greeter1 = new Greeter();
console.log(greeter1.greet()); // "Hello, there"

let greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";

let greeter2: Greeter = new greeterMaker();
console.log(greeter2.greet()); // "Hey there!"

let greeter3: Greeter;
greeter3 = new Greeter();
console.log(greeter3.greet()); // "Hey there!"
```

`typeof` 키워드를 이용하는 것에 유의하자.

여기서는 `typeof Greeter` 를 이용해 `Greeter` 라고 불리는 심벌의 타입을 가져온다.

> Or, more precisely, “give me the type of the symbol called Greeter,”

이는 모든 static 멤버들과 `Greeer` 객체를 생성하는 생성자를 함께 불러온다.