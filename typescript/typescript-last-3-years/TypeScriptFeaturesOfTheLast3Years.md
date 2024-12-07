# 지난 3년간의 자바스크립트 및 타입스크립트

> 원문: https://betterprogramming.pub/all-javascript-and-typescript-features-of-the-last-3-years-629c57e73e42

## ECMAScript

### 과거(아직 유효한 이전 방식)

- 템플릿 리터럴 태그(Tagged template literals): 템플릿 리터럴 앞에 함수 이름을 붙이면 함수에 템플릿 리터럴과 템플릿 값의 일부가 전달된다.

```ts
function formatNumbers(strings: TemplateStringsArray, number: number): string {
  return strings[0] + number.toFixed(2) + strings[1];
}
console.log(formatNumbers`This is the value: ${0}, it's important.`); // This is the value: 0.00, it's important.

// 또는 문자열 내에서 키를 소문자로 변경하려는 경우
function translateKey(key: string): string {
  return key.toLocaleLowerCase();
}
function translate(
  strings: TemplateStringsArray,
  ...expressions: string[]
): string {
  return strings.reduce(
    (accumulator, currentValue, index) =>
      accumulator + currentVale + translateKey(expression[index] ?? ""),
    ""
  );
}
console.log(translate`Hello, this is ${"NAME"} to say ${"MESSAGE"}.`); // Hello, this is name to say message.
```

- 심볼(Symbol): 객체의 고유한 키로 내부적으로 사용된다. `Symbol("foo") === Symbol("foo"); // false`

```ts
const obj: { [index: string]: string } = {};

const symbolA = Symbol("a");
const symbolB = Symbol("b");

console.log(symbolA.description); // "a"

obj[symbolA] = "a";
obj[symbolB] = "b";
obj["c"] = "c";
obj.d = "d";

console.log(obj[symbolA]); // "a"
console.log(obj[symbolB]); // "b"
// 해당 키는 다른 심볼이나 심볼 없이는 접근할 수 없다.
console.log(obj[Symbol("a")]); // undefined
console.log(obj["a"]); // undefined

// for...in 구문을 사용하여 열거할 때, 해당 키는 열거되지 않는다.
for (const i in obj) {
  console.log(i); // "c", "d"
}
```

### ES2020

- 옵셔널 체이닝(Optional chaining): 인덱싱을 통해 정의되지 않은 객체의 값에 접근할 필요가 있을 때, 부모 객체 이름 뒤에 `?`를 사용하여 옵셔널 체이닝을 사용할 수 있다. 이 기능은 인덱싱(`[...]`)이나 함수 호출에도 사용할 수 있다.

```ts
// 이전 방식
// 객체 변수 또는 다른 구조가 정의되어 있는지 확실하지 않은 경우,
// 프로퍼티에 쉽게 접근할 수 없다.
const object: { name: string } | undefined =
  Math.random() > 0.5 ? undefined : { name: "test" };
const value = object.name; // 타입 에러: 'object'가 'undefined'일 수 있습니다.

// 먼저 정의되어 있는지 확인할 수 있지만, 이렇게 하면 가독성이 떨어지고 중첩된 객체의 경우 사용하기가 복잡해진다.
const objectOld: { name: string } | undefined =
  Math.random() > 0.5 ? undefined : { name: "test" };
const valueOld = objectOld ? objectOld.name : undefined;

// 새로운 방식
// 대신 옵셔널 체이닝을 이용할 수 있다.
const objectNew: { name: string } | undefined =
  Math.random() > 0.5 ? undefined : { name: "test" };
const valueNew = objectNew?.name;

// 인덱싱 및 함수에도 사용할 수 있다.
const array: string[] | undefined = Math.random() > 0.5 ? undefined : ["test"];
const item = array?.[0];
const func: (() => string) | undefined =
  Math.random() > 0.5 ? undefined : () => "test";
const result = func?.();
```

- 널 병합 연산자(Nullish coalescing operator): 조건부 할당에 `||` 연산자를 사용하는 대신 새로운 `??` 연산자를 사용할 수 있다. 모든 falsy 값에 적용되는 `||` 연산자와 달리, `null`과 `undefined`에만 적용된다.

```ts
const value: string | undefined = Math.random() > 0.5 ? undefined : "test";

// 이전 방식
// 만약 값을 할당하려는데 해당 값이 undefined나 null일 경우, "||" 연산자를 사용하여 다른 값으로 조건부 할당할 수 있다.
const anotherValue = value || "hello";
console.log(anotherValue); // "test" 또는 "hello"

// 이 방법은 truthy한 값에 대해서는 잘 작동하지만, 0이나 빈 문자열과 비교할 경우에도 조건이 적용되어 버리는 문제가 있다.
const incorrectValue = "" || "incorrect";
console.log(incorrectValue); // 항상 "Incorrect"
const anotherIncorrectValue = 0 || "incorrect";
console.log(anotherIncorrectValue); // 항상 "incorrect"

// 새로운 방식
// 대신 새로운 널 병합 연산자를 사용할 수 있다.
// 이 연산자는 오직 undefined와 null 값에만 적용된다.
const newValue = value ?? "hello";
console.log(newValue); // "test" 또는 "hello"

// falsy 값인 경우, 대체되지 않는다.
const correctValue = "" ?? "incorrect";
console.log(correctValue); // 항상 ""
const anotherCorrectValue = 0 ?? "incorrect";
console.log(anotherCorrectValue); // 항상 0
```

- `import()`: 동적 import다. `import ... from ...`과 비슷하지만 런타임에 동작하고 변수를 사용한다.

```ts
let importModule;
if (shouldImport) {
  importModule = await import("./module.mjs");
}
```

- `String.matchAll`: 루프를 사용하지 않고 캡처 그룹을 포함하여 정규식의 여러 개의 일치 항목을 가져온다.

```ts
const stringVar = "testhello,testagain,";

// 이전 방식
// 일치하는 결과를 가져올 수는 있지만, 캡처 그룹은 가져올 수 없다.
console.log(stringVar.match(/test[\w]+?),/g)); // ["testhello,", "testagain"]

// 캡처 그룹을 포함하여 한 개의 일치 결과만 가져온다.
const singleMatch = stringVar.match(/test([\w]+?),/);
if (singleMatch) {
  console.log(singleMatch[0]); // "testhello,"
  console.log(singleMatch[1]); // "hello"
}

// 동일한 결과를 얻지만 매우 직관적이지 않다. (실행 메서드는 마지막 인덱스를 저장한다.)
// 상태를 저장하기 위해 루프 외부에서 정의되어야 하며 전역(/g)이어야 한다.
// 그렇지 않으면 무한 루프가 생성된다.
const regex = /test([\w]+?),/g;
let execMatch;
while ((execMatch = regex.exec(stringVar)) !== null) {
  console.log(execMatch[0]); // "testhello,", "testagain,"
  console.log(execMatch[1]); // "hello", "again"
}

// 새로운 방식
// 정규식은 전역(/g)이어야 하며, 그렇지 않으면 의미없다.
const matchesIterator = stringVar.matchAll(/test([\w]+?),/g);
// 직접 인덱싱하지 않고 반복하거나 Array.from()을 이용하여 배열로 변환해야 한다.
for (const match of matchesIterator) {
  console.log(match[0]); // "testhello,", "testagain,"
  console.log(match[1]); // "hello", "again"
}
```

- `Promise.allSettled()`: `Promise.all`과 비슷하지만 모든 Promise가 완료될 때까지 기다리며 첫번째 reject/throw시 반환하지 않는다. 모든 에러를 더 쉽게 처리할 수 있다.

```ts
async function success1() {
  return "a";
}
async function success2() {
  return "b";
}
async function fail1() {
  throw "fail 1";
}
async function fail2() {
  throw "fail 2";
}

// 이전 방식
console.log(await Promise.all([success1(), success2()])); // ["a", "b"]
// 하지만 오류가 있는 경우, 다음과 같다.
try {
  await Promise.all([success1(), success2(), fail1(), fail2()]);
} catch (e) {
  console.log(e); // "fail 1"
}
// NOTE: 한 개의 오류만 포찰할 수 있으며 성공 값에 접근할 수 없다.

// 차선책으로 이전 방식을 다음과 같이 수정할 수 있다.
console.log(
  await Promise.all([
    success1().catch((e) => console.log(e)),
    success2().catch((e) => console.log(e)),
    fail1().catch((e) => console.log(e)), // "fail 1"
    fail2().catch((e) => console.log(e)), // "fail 2"
  ])
); // ["a", "b", undefined, undefined]

// 새로운 방식
const results = await Promise.allSettled([
  success1(),
  success2(),
  fail1(),
  fail2(),
]);
const successfulResults = results
  .filter((result) => result.status === "fulfilled")
  .map((result) => (result as PromiseFulfilledResult<string>).value);
console.log(successfulResults); // ["a", "b"]
results
  .filter((result) => result.status === "rejected")
  .forEach((error) => {
    console.log((error as PromiseRejectedResult).reason); // "fail 1", "fail 2"
  });
// 또는 다음과 같다.
for (const result of results) {
  if (result.status === "fulfilled") {
    console.log(result.value); // "a", "b"
  } else if (result.status === "rejected") {
    console.log(results.reason); // "fail 1", "fail 2"
  }
}
```

- `BigInt`: 새로운 `BigInt` 데이터 타입을 사용하면 큰 정수의 숫자를 정확하게 저장하고 연산할 수 있으므로 자바스크립트가 숫자를 부동소수점으로 저장하면서 발생하는 오류를 방지할 수 있다. `BigInt()` 생성자(오류를 방지하기 위해 가능하면 문자열과 함께 사용하는 것이 좋다.)를 사용하여 생성하거나 숫자 뒤에 `n`을 추가하여 생성할 수 있다.

```ts
// 이전 방식
// 자바스크립트는 숫자를 부동소수점으로 저장하기 때문에 항상 약간의 부정확성이 존재한다.
// 하지만 더 중요한 것은 일정 숫자 이상의 정수 연산에서 부정확성이 발생하기 시작한다는 점이다.
const maxSafeInteger = 9007199254740991;
console.log(maxSafeInteger === Number.MAX_SAFE_INTEGER); // true

// 이보다 큰 숫자를 비교하면 부정확할 수 있다.
console.log(Number.MAX_SAFE_INTEGER + 1 === Number.MAX_SAFE_INTEGER + 2);

// 새로운 방식
// 새로운 BigInt 데이터 타입을 사용하면 이론적으로 무한대 크기의 정수 숫자를 저장하고 연산할 수 있다.
// BigInt 생성자를 사용하거나 숫자 끝에 'n'을 추가하여 사용할 수 있다.
const maxSafeIntegerPreviously = 9007199254740991n;
console.log(maxSafeIntegerPreviously); // 9007199254740991

const anotherWay = BigInt(9007199254740991);
console.log(anotherWay); // 9007199254740991

// 숫자를 사용하여 생성자를 사용하면 MAX_SAFE_INTEGER보다 큰 정수를 안전하게 전달할 수 없다.
const incorrect = BigInt(9007199254740992);
console.log(incorrect); // 9007199254740992
const incorrectAgain = BigInt(9007199254740993);
console.log(incorrectAgain); // 9007199254740992
// 동일한 값으로 반환된다.

// 대신 문자열이나 더 나은 구문을 사용하자.
const correct = BigInt("9007199254740993");
console.log(correct); // 9007199254740993
const correctAgain = 9007199254740993n;
console.log(correctAgain); // 9007199254740993

// 16진수, 8진수, 2진수도 문자열로 전달할 수 있다.
const hex = BigInt("0x1fffffffffffff");
console.log(hex); // 9007199254740991
const octal = BigInt("0o377777777777777777");
console.log(octal); // 9007199254740991
const binary = BigInt(
  "0b11111111111111111111111111111111111111111111111111111"
);
console.log(binary); // 9007199254740991

// 대부분의 산술 연산은 예상한 대로 작동하지만, 다른 연산자도 BigInt여야 한다.
// 모든 연산은 BigInt를 반환한다.
const addition = maxSafeIntegerPreviously + 2n;
console.log(addition); // 9007199254740993

const multiplication = maxSafeIntegerPreviously * 2n;
console.log(multiplication); // 18014398509481982

const subtraction = multiplication - 10n;
console.log(subtraction); // 18014398509481972

const modulo = multiplication % 10n;
console.log(modulo); // 2

const exponentiation = 2n ** 54n;
console.log(exponentiation); // 18014398509481984

const exponentiationAgain = 2n ^ 54n;
console.log(exponentiationAgain); // 18014398509481984

const negative = exponentiation * -1n;
console.log(negative); // -18014398509481984

// 나눗셈은 약간 다르게 작동하는데, BigInt는 정수만 저장할 수 있기 때문이다.
const division = multiplication / 2n;
console.log(division); // 9007199254740991
// 나눌 수 있는 정수의 경우 이 방법이 잘 작동합니다.

// 하지만 나눌 수 없는 숫자의 경우, 정수 나눗셈(반내림)처럼 작동한다.
const divisionAgain = 5n / 2n;
console.log(divisionAgain); // 2

// BigInt가 아닌 숫자와 엄격한 동등성은 없지만 느슨한 동등성은 있다.
console.log(0n === 0); // false
console.log(0n == 0); // true

// 그러나 비교는 예상대로 작동한다.
console.log(1n < 2); // true
console.log(2n > 1); // true
console.log(2 > 2); // false
console.log(2n > 2); // false
console.log(2n >= 2); // true

// "bigint" 타입.
console.log(typeof 1n); // "bigint"

// BigInt는 일반적인 숫자(부호가 있는 정수 및 부호 없는 정수(음수가 없음))로 다시 변환할 수 있다.
// 물론 이 경우 정확도가 떨어진다. 유효 자릿수를 지정할 수 있다.

console.log(BigInt.asIntN(0, -2n)); // 0
console.log(BigInt.asIntN(1, -2n)); // 0
console.log(BigInt.asIntN(2, -2n)); // -2
// 일반적으로 더 많은 비트 수를 사용한다.

// 부호가 없느 숫자로 변환할 때 음수는 2의 보수로 변환한다.
console.log(BigInt.asUnitN(8, -2n)); // 254
```

- `globalThis`: 브라우저, Node.js 등과 같은 환경에 관계없이 전역 컨텍스트 내 변수에 접근할 수 있다. 여전히 나쁜 습관으로 여겨지지만 때로는 필요하다. 브라우저의 최상위 수준에서 `this`와 유사하다.

```ts
console.log(globalThis.Math); // Math 객체
```

- `import.meta`: ES 모듈을 사용하는 경우, `import.meta.url`을 사용하여 현재 모듈의 URL을 가져온다.

```ts
console.log(import.meta.url); // "file://..."
```

- export\*as...from...: 기본값을 서브모듈로 쉽게 다시 내보낼 수 있다.

```ts
export * as am from "another-module";
```

```ts
import { am } from "module";
```
