# Enums

### Numeric enums

```tsx
enum Direction {
    Up = 1,
    Down,
    Left,
    Right,
}
```

여기서 `Up = 1` 로 두었기 때문에 나머지는 2,3,4 로 순차적으로 증가한다.

만약 `Up = 1` 을 두지 않았다면 0,1,2,3 으로 설정된다.

아니면 모든 멤버에 숫자를 할당 할 수도 있다.

### String enums

```tsx
enum Direction {
  Up = "UP",
  Down = "DOWN",
  Left = "LEFT",
  Right = "RIGHT",
}
```

### Heterogeneous enums

```tsx
enum BooleanLikeHeterogeneousEnum {
  No = 0,
  Yes = "YES",
}
```

기술적으로 가능하나, 왜 필요할까?

### Computed and consant members

```tsx
enum FileAccess {
  // constant members
  None,
  Read = 1 << 1,
  Write = 1 << 2,
  ReadWrite = Read | Write,
  // computed member
  G = "123".length,
}
```

다음에 해당되는 것은 constant members 이다.

1. a literal enum expression (basically a string literal or a numeric literal)
2. a reference to previously defined constant enum member (which can originate from a different enum)
3. a parenthesized constant enum expression
4. one of the `+`, ``, `~` unary operators applied to constant enum expression
5. `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `>>>`, `&`, `|`, `^` binary operators with constant enum expressions as operands

### Union enums and enum member types

liternal enum members

A literal enum member is a constant enum member with no initialized value, or with values that are initialized to

- any string literal (e.g. `"foo"`, `"bar`, `"baz"`)
- any numeric literal (e.g. `1`, `100`)
- a unary minus applied to any numeric literal (e.g. `1`, `100`)

When all members in an enum have literal enum values, some special semantics come to play.

하나의 타입으로 사용이 가능하다

```tsx
enum ShapeKind {
  Circle,
  Square,
}

interface Circle {
  kind: ShapeKind.Circle;
  radius: number;
}

interface Square {
  kind: ShapeKind.Square;
  sideLength: number;
}

let c: Circle = {
  kind: ShapeKind.Square, // 여기서 에러가 난다. kind 의 타입은 ShapeKind.Circle 만 가능하기에
  radius: 100,
};
```

### Enums at run time

```tsx
enum E {
  X,
  Y,
  Z,
}

function f(obj: { X: number }) {
  return obj.X;
}

// Works, since 'E' has a property named 'X' which is a number.
f(E);
```

### Enums at compile time

```tsx
enum LogLevel {
  ERROR,
  WARN,
  INFO,
  DEBUG,
}

/**
 * This is equivalent to:
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
type LogLevelStrings = keyof typeof LogLevel;

function printImportant(key: LogLevelStrings, message: string) {
  const num = LogLevel[key];
  if (num <= LogLevel.WARN) {
    console.log("Log level key is:", key);
    console.log("Log level value is:", num);
    console.log("Log level message is:", message);
  }
}
printImportant("ERROR", "This is a message");
```

이 코드는 다음의 결과를 보인다.

```tsx
Log level key is: ERROR
Log level value is: 0
Log level message is: This is a message
```

**Reverse mappings**

```tsx
enum Enum {
  A,
}

let a = Enum.A;
let nameOfA = Enum[a]; // "A"
```

**Const enums**

const enum 은 컴파일 단계에서 완전히 지워지며 사용하는 곳에서 Inline 된다. 

또한 computed members 를 사용할 수 없다.

```tsx
const enum Direction {
  Up,
  Down,
  Left,
  Right,
}

let directions = [
  Direction.Up,
  Direction.Down,
  Direction.Left,
  Direction.Right,
];
```