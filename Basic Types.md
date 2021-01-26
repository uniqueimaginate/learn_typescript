# Basic Types

### Boolean

### Number

```tsx
let decimal: number = 6;
let hex: number = 0xf00d;
let binary: number = 0b1010;
let octal: number = 0o744;
let big: bigint = 100n;
```

### String

### Array

```tsx
let list: number[] = [1,2,3]
let list: Array<number> = [1,2,3]
```

### Tuple

```tsx
let x: [string, number]
x = ["hello", 10]
// This is Okay

x = [10, "hello"]
// Error

```

### Enum

```tsx
enum Color {
	Red,
	Green,
	Blue,
}

let c: Color = Color.Green

// enum 은 0 부터 시작한다.
// 그러나 숫자를 자기가 지정해줄수 있음

enum Color {
	Red = 1,
	Green = 2,
	Blue = 4, 
}

let colorName: String = Color[2]

console.log(colorName) // Green
```

### Unknown

- `typeof` 를 이용해서 타입을 확인한 후에 쓰면 좋을 것 같다.
- 타입 캐스팅을 통해 진행하는 느낌

### Any

- TypeScript 가 property 의 type check 또는 existence 에 대해서 체크 하지 않겠다는 의미 (사실상 자바스크립트 처럼 쓴다는 의미 인것 같다)

### Void

- only `null` 만 assign 할 수 있다.

### Null, Undefined

- 흠... Kotlin 을 써본 나로썬, 가능한 쓰기 싫은 타입
- `--strictNullchecks` flag 를 사용하면, `null`, `undefined` 키워드는 오직 `unknowned`, `any` 타입에만 사용할 수 있다. (딱 하나 예외가 있다면, undefined 는 `void` 에 assign 할 수 있다.)

### Never

- 일어나면 안되는 값을 나타낼 때
- `throw new Error(message)`

### Object

- non-primitive type 인 경우 사용하면 된다
- 즉, number, string, boolean, bigint, symbol, null, undefined 가 아닌 모든 경우

### Type assertions

- 다른 언어에서 타입 캐스팅과 비슷한 역할을 한다
- 다만, 타입 캐스팅 처럼 데이터를 restructuring 또는 check 하는 것이 아니다.
- 그냥 컴파일러한테 "야, 컴파일러, 나만 믿어, 이 데이터 내가 알고 있는 타입이야 ㅇㅋ?" 이런 느낌이다.
- `as` 키워드를 쓰거나 `<string>someValue` 와 같이 `<>` 를 쓴다.