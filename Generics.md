# Generics

### Hello World of Generics

```tsx
function identity<T>(arg: T): T{
	return arg;
}
```

T 를 이용해 타입을 표기한다

```tsx
function loggingIdentity<T>(arg: T[]): T[]{
	console.log(arg.length);
	return arg;
}
```

들어오는 변수가 만약 숫자가 된다면 이는 에러를 일으킨다

```tsx
function loggingIdentity<T>(arg: Array<T>): Array<T>{
	console.log(arg.length)
	return arg;
}
```

따라서 위와 같이 배열이라고 명시해준다면 더 이상 에러를 일으키지 않는다.

### Generic Types

각각의 코드상 차이를 보자.

```tsx
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: <T>(arg: T) => T = identity;
```

```tsx
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: <U>(arg: U) => U = identity;
```

```tsx
function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: { <T>(arg: T): T } = identity;
```

```tsx
interface GenericIdentityFn {
  <T>(arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn = identity;
```

```tsx
interface GenericIdentityFn<T> {
  (arg: T): T;
}

function identity<T>(arg: T): T {
  return arg;
}

let myIdentity: GenericIdentityFn<number> = identity;
```

### Generic Classes

```tsx
class GenericNumber<T>{
	zeroValue: T;
	add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x,y) {
	return x + y;
}
```

```tsx
let stringNumeric = new GenericNumber<string>();
stringNumeric.zeroValue = "";
stringNumeric.add = function (x, y) {
  return x + y;
};

console.log(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

### Generic Constraints

```tsx
interface Lengthwise {
  length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
  console.log(arg.length); // Now we know it has a .length property, so no more error
  return arg;
}
```

```tsx
loggingIdentity(3); // 에러가 발생한다.
```

`number` 타입으로 들어왔지만, `Lengthwise` 타입으로 제한되어있기 때문에 에러가 발생한다.

```tsx
loggingIdentity({ length: 10, value: 3 });
```

### Using Type Parameters in Generic Constraints

```tsx
function getProperty<T, K extends keyof T>(obj: T, key: K) {
  return obj[key];
}

let x = { a: 1, b: 2, c: 3, d: 4 };

getProperty(x, "a");
getProperty(x, "m");  // 에러가 난다! a,b,c,d 중 하나가 아니기 때문에
```