# Functions

### Functions

자바스크립트와 같이 이름있는 함수와 익명 함수를 선언할 수 있다.

```tsx
// Named function
function add(x, y) {
  return x + y;
}

// Anonymous function
let myAdd = function (x, y) {
  return x + y;
};
```

### Function Types

함수의 변수와 리턴값에 타입을 지정할 수 있다.

```tsx
function add(x: number, y: number): number {
  return x + y;
}

let myAdd = function (x: number, y: number): number {
  return x + y;
};
```

### Writing the function Type

```tsx
let myAdd: (x: number, y: number) => number = function (
  x: number,
  y: number
): number {
  return x + y;
};
```

```tsx
let myAdd: (baseValue: number, increment: number) => number = function (
  x: number,
  y: number
): number {
  return x + y;
};
```

위의 두 코드는 같은 내용이다. 

### Inferring the Types

```tsx
// The parameters 'x' and 'y' have the type number
let myAdd = function (x: number, y: number): number {
  return x + y;
};

// myAdd has the full function type
let myAdd2: (baseValue: number, increment: number) => number = function (x, y) {
  return x + y;
};
```

음.. 뭐 당연한 것 같다.

### Optional and Default Parameters

```tsx
function buildName(firstName: string, lastName?: string) {
  if (lastName) return firstName + " " + lastName;
  else return firstName;
}

let result1 = buildName("Bob"); // 성공!
let result2 = buildName("Bob", "Adams", "Sr."); // 실패! 변수가 더 많다.
let result3 = buildName("Bob", "Adams"); // 성공!
```

```tsx
function buildName(firstName: string, lastName = "Smith") {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob"); // 성공! 리턴값 : "Bob Smith" 
let result2 = buildName("Bob", undefined); // 성공! 리턴값 : "Bob Smith"
let result3 = buildName("Bob", "Adams", "Sr."); // 실패! 너무 많은 변수
let result4 = buildName("Bob", "Adams"); // 성공!
```

```tsx
function buildName(firstName = "Will", lastName: string) {
  return firstName + " " + lastName;
}

let result1 = buildName("Bob"); // 실패! 변수가 하나 밖에 없다
let result2 = buildName("Bob", "Adams", "Sr."); // 실패! 변수가 너무 많다!
let result3 = buildName("Bob", "Adams"); // 성공! 리턴값: "Bob Adams"
let result4 = buildName(undefined, "Adams"); // 성공! 리턴값: "Will Adams"
```

필자는 코틀린을 이용한 안드로이드 개발을 해본적이 있기에 위의 세개의 코드블럭 모두 이해가 금방 되었지만, 마지막 코드블럭만 짚고 가겠다.

Default Parameter 는 파라미터의 순서와 상관없이 지정할 수 있다. 다만 개발자가 Default Parameter 값을 사용하고 싶다면 파라미터의 순서에 해당하는 곳에 `undefined` 를 명시적으로 입력해야 한다.

### Rest Parameters

여러 개의 파라미터를 하나의 그룹으로 사용하고 싶거나, 몇 개의 파라미터를 받을지 모를 때 사용한다.

```tsx
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

// employeeName will be "Joseph Samuel Lucas MacKinzie"
let employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

코드를 자세히 보면 `...restOfName: string[]` 부분이 있다.  `...` 에 유의하자.

```tsx
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

let buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```

### this

예시를 통해 설명하겠다.

```tsx
let deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function () {
    return function () {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: " + pickedCard.card +  " of " + pickedCard.suit)
```

위의 코드에서 this 로 인해 오류가 난다.  여기서 this 는 `deck` 객체가 아니라 `window` 객체 이기 때문이다. 

strict mode 에서는 `window` 가 아니라 `undefined` 가 될 것이다.

그렇다면 `deck` 객체가 되게끔 해야 한다.

⇒ arrow function 을 사용하자

```tsx
let deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  createCardPicker: function () {
    // NOTE: the line below is now an arrow function, allowing us to capture 'this' right here
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: " + pickedCard.card +  " of " + pickedCard.suit)
```

그러나 여전히 `this.suits[pickedSuit]` 의 타입은 `any` 이다. 

그 이유는 객체 리터럴의 함수 표현식에 있기 때문이다. 

따라서 이를 해결 하기 위해서는 명시적인 `this` 파라미터를 선언해준다. 

이 `this` 파라미터는 가짜 파라미터로 함수의 변수 중 가장 먼저 온다.

```tsx
interface Card {
  suit: string;
  card: number;
}

interface Deck {
  suits: string[];
  cards: number[];
  createCardPicker(this: Deck): () => Card;
}

let deck: Deck = {
  suits: ["hearts", "spades", "clubs", "diamonds"],
  cards: Array(52),
  // NOTE: The function now explicitly specifies that its callee must be of type Deck
  createCardPicker: function (this: Deck) {
    return () => {
      let pickedCard = Math.floor(Math.random() * 52);
      let pickedSuit = Math.floor(pickedCard / 13);

      return { suit: this.suits[pickedSuit], card: pickedCard % 13 };
    };
  },
};

let cardPicker = deck.createCardPicker();
let pickedCard = cardPicker();

console.log("card: " + pickedCard.card +  " of " + pickedCard.suit)
```

이제 `this` 는 `Deck` 객체이다.

### This parameters in callbacks

callback 함수에서 `this` 는 `undefined` 가 된다. 따라서 `this` 파라미터로 일어나는 에러를 예방해야 한다.

```tsx
interface UIElement {
  addClickListener(onclick: (this: void, e: Event) => void): void;
}
// addClickListener 함수가 (onClick 함수)를 인자로 받고 void 를 리턴한다.
```

```tsx
class Handler {
  info: string;
  onClickBad(this: Handler, e: Event) {
    // oops, used `this` here. using this callback would crash at runtime
    this.info = e.message;
  }
}

let h = new Handler();
uiElement.addClickListener(h.onClickBad); // error!
```

위의 코드는 에러가 난다.

`addClickListener` 는 함수를 인자로 받는데, 받는 함수의 인자 중 `this` 가 `void` 이기 때문이다. 

그러나 `h.onClickBad` 함수는 인자로 받는 `this` 가 `Handler` 이기 때문에 에러가 난다.

따라서 다음과 같이 변경해야 한다.

```tsx
class Handler {
  info: string;
  onClickGood(this: void, e: Event) {
    // can't use `this` here because it's of type void!
    console.log("clicked!");
  }
}

let h = new Handler();
uiElement.addClickListener(h.onClickGood);
```

### Overloads

```tsx
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: any): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == "object") {
    let pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just let them pick the card
  else if (typeof x == "number") {
    let pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

let myDeck = [
  { suit: "diamonds", card: 2 },
  { suit: "spades", card: 10 },
  { suit: "hearts", card: 4 },
];

let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

위의 코드에서 `pickCard` 함수는 타입이 없다. 

변수로 주어지는 객체 타입에 따라 리턴값의 타입이 변경된다.

그렇다면 어떻게 타입을 지정해 줄 수 있을까?

다음의 코드를 보자

```tsx
let suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: { suit: string; card: number }[]): number;
function pickCard(x: number): { suit: string; card: number };
function pickCard(x: any): any {
  // Check to see if we're working with an object/array
  // if so, they gave us the deck and we'll pick the card
  if (typeof x == "object") {
    let pickedCard = Math.floor(Math.random() * x.length);
    return pickedCard;
  }
  // Otherwise just let them pick the card
  else if (typeof x == "number") {
    let pickedSuit = Math.floor(x / 13);
    return { suit: suits[pickedSuit], card: x % 13 };
  }
}

let myDeck = [
  { suit: "diamonds", card: 2 },
  { suit: "spades", card: 10 },
  { suit: "hearts", card: 4 },
];

let pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

let pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);s
```