# TypeScript for JavaScript Programmers

[https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html)

모든 정보는 위의 사이트에 있습니다.

1. Types by Inference
    - 타입 추론 지원(사실 좀 당연하죠?)
2. 타입 정의

    ```tsx
    const user = {
    	name: "Hayes",
    	id: 0,
    };

    interface User {
    	name: String;
    	id: number;
    }

    const user: User{
    	name: "Hayes",
    	id: 0.
    }

    // 인터페이스에는 세미콜론이 있습니당

    class UserAccount {
      name: string;
      id: number;

      constructor(name: string, id: number) {
        this.name = name;
        this.id = id;
      }
    }

    const user: User = new UserAccount("Murphy", 1);

    function getAdminUser(): User {
      //...
    }

    function deleteUser(user: User) {
      // ...
    }
    ```

3. 타입 생성

    새로운 타입을 만들 수 있다.

    Unions, Generics 를 이용해서 생성

    Union

    ```tsx
    type MyBool = true | false

    type WindowStates = "open" | "closed" | "minimized";
    type LockSates = "locked" | "unlocked";
    type OddNumbersUndererTen = 1 | 3 | 5 | 7 | 9;

    function getLength(obj: string | string[]) {
      return obj.length;
    }
    ```

    - typeof 로 확인

    ```tsx
    function wrapInArray(obj: string | string[]) {
      if (typeof obj === "string") {
        return [obj];
    //          ^ = (parameter) obj: string
      } else {
        return obj;
      }
    }
    ```

    Generics

    ```tsx
    interface Backpack<Type> {
      add: (obj: Type) => void;
      get: () => Type;
    }

    // This line is a shortcut to tell TypeScript there is a
    // constant called `backpack`, and to not worry about where it came from.

    declare const backpack: Backpack<string>;
    // object is a string, because we declared it above as the variable part of Backpack.

    const object = backpack.get();

    // Since the backpack variable is a string, you can't pass a number to the add function.
    backpack.add(23);
    // Argument of type 'number' is not assignable to parameter of type 'string'.
    ```

4. Structural Type System
    - 한글로 위의 영어를 어떻게 표현하는지 모르겠지만, 두개의 객체가 같은 모양을 갖고 있다면 두개의 객체는 같은 타입으로 여긴다. ⇒ 즉, 근본이 없다는 소리다

    ```tsx
    // 예시
    interface Point{
    	x: number;
    	y: number;
    }

    function logPoint(p: Point){
    	console.log(`${p.x}, ${p.y}`)
    }

    const point = { x:12, y:26 };

    logPoint(point);

    const point3 = {x: 12, y: 26, z: 89};
    logPoint(point3); // logs 12, 26

    const color = {hex: "#187ABF");
    logPoint(color) // 애초에 color 에 에러 표시가 뜹니다
    ```