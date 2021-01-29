# Unions and Intersection Types

### Union Types

예시를 들어 설명!

```tsx
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: any) {
  if (typeof padding === "number") {
    return Array(padding + 1).join(" ") + value;
  }
  if (typeof padding === "string") {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${typeof padding}'.`);
}

padLeft("Hello world", 4); // returns "    Hello world"
```

```tsx
// passes at compile time, fails at runtime.
let indentedString = padLeft("Hello world", true);
```

위의 코드 중, 첫 번째에서는 오류가 나지 않는다.

그러나, 두 번째 코드에서는 런타임 오류가 난다. 

그야 당연히, `padding` 으로 들어온 변수가 `number` 도 `string` 도 아닌 `boolean` 이기 때문이다.

```tsx
function padLeft(value: string, padding: string | number) {
  // ...
}

let indentedString = padLeft("Hello world", true);
```

따라서 위와 같이 Union 을 통해 `string`, `number` 만 받을 수 있게 해야 한다.

### Unions with Common Fields

```tsx
interface Bird {
  fly(): void;
  layEggs(): void;
}

interface Fish {
  swim(): void;
  layEggs(): void;
}

declare function getSmallPet(): Fish | Bird;

let pet = getSmallPet();
pet.layEggs();

// Only available in one of the two possible types
pet.swim();
```

위의 코드는 `pet.swim()` 에서 오류가 난다.

union 타입의 값을 갖는다면 union 에 있는 모든 타입들 중 공통으로 갖는 멤버만 접근할 수 있기 때문이다.

### Discriminating Unions

```tsx
type NetworkLoadingState = {
  state: "loading";
};

type NetworkFailedState = {
  state: "failed";
  code: number;
};

type NetworkSuccessState = {
  state: "success";
  response: {
    title: string;
    duration: number;
    summary: string;
  };
};

// Create a type which represents only one of the above types
// but you aren't sure which it is yet.
type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState;
```

```tsx
type NetworkState =
  | NetworkLoadingState
  | NetworkFailedState
  | NetworkSuccessState;

function logger(state: NetworkState): string {
  // Right now TypeScript does not know which of the three
  // potential types state could be.

  // Trying to access a property which isn't shared
  // 여기서 에러가 난다.
  state.code;

  // By switching on state, TypeScript can narrow the union
  // down in code flow analysis
  switch (state.state) {
    case "loading":
      return "Downloading...";
    case "failed":
      // The type must be NetworkFailedState here,
      // so accessing the `code` field is safe
      return `Error ${state.code} downloading`;
    case "success":
      return `Downloaded ${state.response.title} - ${state.response.summary}`;
  }
}
```

첫 번째 코드 블럭은 Type 선언부이다.

두 번째 코드 블럭은 선언한 type 을 이용하는 것을 보여준다.

`state.code;` 부분에서 에러가 난다.

왜냐하면, 선언된 3가지의 타입 중 `code` 라는 멤버를 가진 타입은 `NetworkFailedState` 밖에 없기 때문이다.

### Union Exhaustiveness checking

Union 타입 안에 속해 있는 모든 타입을 체크 하기 위해서 필요하다.

```tsx
type NetworkLoadingState = {
    state: "loading";
};

type NetworkFailedState = {
    state: "failed";
    code: number;
};

type NetworkSuccessState = {
    state: "success";
    response: {
        title: string;
        duration: number;
        summary: string;
    };
};

type NetworkFromCachedState = {
    state: "from_cache";
    id: string;
    response: NetworkSuccessState["response"];
};

type NetworkState =
    | NetworkLoadingState
    | NetworkFailedState
    | NetworkSuccessState
    | NetworkFromCachedState;

function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}

function logger(s: NetworkState): string {
    switch (s.state) {
        case "loading":
            return "loading request";
        case "failed":
            return `failed with code ${s.code}`;
        case "success":
            return "got response";
        default:
            return assertNever(s);
    }
}
```

위의 코드에서 `logger` 함수는  `NetworkFromCachedState` 타입을 구별할 수 없게되어 있다.

그러나 default 로 넣은 `assertNever` 함수를 통해서 컴파일 단계에서 `logger` 함수에서 `NetworkFromCachedState` 타입을 구별하지 않았다는 것을 오류로 보여준다.

### Intersection Types;

Intersection 타입은 여러 개의 타입을 하나로 만들 수 있는 것이다.

```tsx
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtworksData {
  artworks: { title: string }[];
}

interface ArtistsData {
  artists: { name: string }[];
}

// These interfaces are composed to have
// consistent error handling, and their own data.

type ArtworksResponse = ArtworksData & ErrorHandling;
type ArtistsResponse = ArtistsData & ErrorHandling;

const handleArtistsResponse = (response: ArtistsResponse) => {
  if (response.error) {
    console.error(response.error.message);
    return;
  }

  console.log(response.artists);
};
```