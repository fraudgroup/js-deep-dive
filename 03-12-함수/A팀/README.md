# 12장 함수

## 목차

1. [함수란?](#1.-함수란?)
2. [함수를 사용하는 이유](#2.-함수를-사용하는-이유)
3. [함수 리터럴](#3-함수-리터럴)
4. [함수 정의](#4-함수-정의)
5. [함수 호출](#5-함수-호출)
6. [참조에 의한 전달과 외부 상태의 변경](#6-참조에-의한-전달과-외부-상태의-변경)
7. [다양한 함수의 형태](#7-다양한-함수의-형태)

<br />

## 1. 함수란?

일련의 과정을 문(statement)으로 구현하고 코드 블록으로 감싸서 **하나의 실행 단위로 정의**한 것이다.

함수는 다음으로 구성된다.

- **매개변수**(parameter): 함수 내부로 입력을 전달받는 변수.
- **인수**(argument): 함수 호출 시 전달하는 입력.
- **반환 값**(return value): 함수의 출력.

함수는 **함수 정의**(function definition)를 통해 생성한다. 함수 정의에는 대표적으로 **함수 선언문**, **함수 표현식**이 있다.

함수 선언문과 함수 표현식의 차이는 대표적으로 호이스팅이 있다. [함수 표현식 vs 함수 선언문](https://joshua1988.github.io/web-development/javascript/function-expressions-vs-declarations/)

- 함수 선언문

```js
function funcDeclarations() {
  return 'A function declaration';
}
```

- 함수 표현식

```js
var funcExpression = function () {
  return 'A function expression';
};
```

함수를 실행하려면 **함수 호출**(function call/invoke)을 해야 한다.

```js
var printHelloWorld = function () {
  console.log('Hello World');
};

printHelloWorld(); // "Hello World"
```

<br />

## 2. 함수를 사용하는 이유

**코드의 재사용**. 정의된 함수를 몇 번이든 호출할 수 있다.

<br />

## 3. 함수 리터럴

함수 리터럴은 `function` 키워드, 함수 이름, 매개 변수 목록, 함수 몸체로 구성된다.

함수 표현식에서 함수 이름은 독특한 특징을 가진다.

- 식별자로 식별자 네이밍 규칙을 준수해야 한다.
- 함수 몸체 내에서만 참조할 수 있는 식별자다.
- 생략할 수 있다. 이름이 있는 함수를 **기명 함수**(named function), 이름이 없는 함수를 **무명/익명 함수**(anonymous function)라 한다.

```js
var add = function addFn(x, y) {
  return x + y;
};

console.log(add(5, 3)); // 8
console.log(addFn(5, 3)); // Uncaught ReferenceError: addFn is not defined
```

<br />

## 4. 함수 정의

### 4-1. 함수 선언문

함수 이름을 생략할 수 없다.

```js
function add(x, y) {
  return x + y;
}

function (x, y) {
  return x + y;
}
// Uncaught SyntaxError: Function statements require a function name
```

표현식이 아닌 문이다.

함수는 **함수 이름으로 호출하는 것이 아니라** 함수 객체를 가리키는 **식별자로 호출한다.**

자바스크립트 엔진은 함수 선언문으로 생성된 함수를 호출하기 위해 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고, 거기에 함수 객체를 할당한다.

```js
// 함수 선언문
function add(x, y) {
  return x + y;
}

// 자바스크립트 엔진에 의한 변환
var add = function add(x, y) {
  return x + y;
};
```

### 4-2. 함수 표현식

값의 성질을 갖는 객체를 **일급 객체**라 한다.

자바스크립트 함수는 일급 객체다. 즉, 값처럼 자유롭게 사용할 수 있다는 의미다.

함수의 일급 객체 특성을 이용한 함수 정의 방식이다.

```js
var add = function (x, y) {
  return x + y;
};
```

함수 선언문은 "표현식이 아닌 문"이고 함수 표현식은 "표현식인 문"으로 차이를 잘 이해하자.

### 4-3. 함수 생성 시점과 함수 호이스팅

함수 선언문으로 정의한 함수와 함수 표현식으로 정의한 함수의 **생성 시점이 다르다**.

```js
console.log(add); // f add(x, y)
console.log(sub); // undefined

// 함수 선언문
function add(x, y) {
  return x + y;
}

// 함수 표현식
var sub = function (x, y) {
  return x - y;
};
```

함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다. 이런 특징을 함수 호이스팅이라 한다.

함수 호이스팅과 변수 호이스팅은 차이가 있다.

- var 키워드로 선언된 변수는 `undefined`로 초기화 된다.
- 함수 선언문을 통해 암묵적으로 생성된 식별자는 함수 객체로 초기화 된다.

함수 표현식으로 함수를 정의하면 함수 호이스팅이 발생하는 것이 아니라 **변수 호이스팅이 발생한다**.

### 4-4. Function 생성자 함수

`Function` 생성자 함수로 함수를 생성하는 방식은 일반적이지 않으며 바람직하지도 않다.

클로저(closure)를 생성하지 않는 등, 함수 선언문이나 함수 표현식으로 생성한 함수와 다르게 동작한다.

### 4-5. 화살표 함수

ES6에서 도입된 함수 정의 방식이다.

```js
const add = (x, y) => x + y;
```

기존의 함수보다 표현만 간력한 것이 아니라 내부 동작 또한 간략화되어 있다.

`this` 바인딩 방식이 다르고, `prototype` 프로퍼티가 없으며 `arguments` 객체를 생성하지 않는다.

<br />

## 5. 함수 호출

### 5-1. 매개변수와 인수

인수는 값으로 평가될 수 있는 표현식이어야 한다. 함수를 호출할 때 지정한다.

매개변수는 함수를 정의할 때 선언하며, 함수 몸체 내부에서 변수와 동일하게 취급된다.

```js
// 매개변수 x, y
function add(x, y) {
  return x + y;
}

var result = add(1, 2); // 인수: 1, 2
```

<br />

매개변수의 스코프(유효 범위)는 함수 내부다.

```js
function add(x, y) {
  console.log(x, y); // 2 5
  return x + y;
}

add(2, 5);

console.log(x, y); // ReferenceError: x is not defined
```

<br />

인수가 부족해서 할당되지 않은 매개변수의 값은 `undefined`다.

```js
function add(x, y) {
  console.log(x, y); // 2 undefined
  return x + y;
}

console.log(add(2)); // NaN
```

<br />

매개변수보다 인수가 더 많은 경우 초과된 인수는 무시된다. 암묵적으로 `arguments` 객체의 프로퍼티로 보관된다.

```js
function add(x, y) {
  console.log(arguments); // Arguments(3) [2, 5, 10, callee: ƒ, Symbol(Symbol.iterator): ƒ]

  return x + y;
}

add(2, 5, 10);
```

### 5-2. 인수 확인

자바스크립트 함수는 매개변수와 인수의 **개수가 일치하는지 확인하지 않는다**.

자바스크립트는 동적 타입 언어로 매개변수의 **타입을 사전에 지정할 수 없다**.

다음과 같은 특징 때문에 타입스크립트와 같은 정적 타입을 선언할 수 있는 상위 확장을 도입하는 것도 하나의 방법이다.

매개변수 기본값을 사용하면 인수가 전달되지 않았을 경우와 `undefined`를 전달한 경우에 기본값이 부여된다.

🔍 주의 `Falsy` 값, `null`의 경우 기본값이 부여되지 않는다.

```js
function add(a = 0, b = 0) {
  return a + b;
}

console.log(add()); // 0
console.log(add(1)); // 1
console.log(add(1, 2)); // 3
```

### 5-3. 매개변수의 최대 개수

매개변수는 적을 수록 좋다.

객체를 인수로 전달하는 것도 하나의 방법이다.

```js
// JQuery의 ajax 메서드에 객체를 인수로 전달하는 예
$.ajax({
  method: 'POST',
  url: '/user',
  data: { id: 1, name: 'Lee' },
  cache: false,
});
```

하지만 함수 외부에서 전달한 객체를 함수 내부에서 변경하면 함수 외부의 객체가 변경되는 **부수 효과(side effect)가 존재**한다.

### 5-4. 반환문

함수 호출은 표현식이다. 함수 호출 표현식은 `return` 키워드가 반환한 표현식의 평가 결과, 즉 **반환값으로 평가**된다.

반환문은 두 가지 역할을 한다.

- 함수의 실행을 중단하고 함수 몸체를 빠져나간다.
- `return` 키워드 뒤에 오는 표현식을 평가해 반환한다. 명시적으로 지정하지 않으면 `undefined`가 반환된다.

```js
function add(x, y) {
  return x + y;

  console.log('실행되지 않는다.');
}

console.log(add(3, 12)); // 15
```

```js
function foo() {
  return;
}

console.log(foo()); // undefined
```

<br />

## 6. 참조에 의한 전달과 외부 상태의 변경

매개변수도 함수 몸체 냉부에서 변수와 동일하게 취급되므로 타입에 따라 값에 의한 전달, 참조에 의한 전달 방식을 그대로 따른다.

```js
function changeVal(primitive, obj) {
  primitive += 100;
  obj.name = 'Kim';
}

var num = 100;
var person = { name: 'Lee' };

console.log(num); // 100
console.log(person); // { name: "Lee" }

changeVal(num, person);

console.log(num); // 100
console.log(person); // { name: "Kim" }
```

이러한 문제를 피하기 위해서 **옵저버(Observer) 패턴**, **불변 객체**, **깊은 복사** 등의 방식을 사용해서 외부 상태가 변경되는 부수 효과를 없앨 수 있다.

외부 상태를 변경하지 않고 외부 상태에 의존하지도 않는 함수를 순수 함수라 한다.

순수 함수를 통해 프로그램의 안정성을 높이려는 프로그래밍 패러다임을 함수형 프로그래밍이라 한다.

<br />

## 7. 다양한 함수의 형태

### 7-1. 즉시 실행 함수

함수 정의와 동시에 즉시 호출되는 함수를 즉시 실행 함수(IIFE; Immediately Invoked Function Expression)라고 한다.

단 한번만 호출되며 다시 호출할 수 없다. 또한 표현식으로 평가된다.

```js
var res = (function () {
  var a = 3;
  var b = 5;
  return a * b;
})();

console.log(res); // 15
```

즉시 실행 함수 내에 코드를 모아 두면 혹시 있을 수도 있는 변수나 함수 이름의 충돌을 방지할 수 있다.

### 7-2. 재귀 함수

자기 자신을 호출하는 행위, 즉 재귀 호출을 수행하는 함수를 말한다.

대표적으로 팩토리얼이 있다.

```js
function factorial(n) {
  if (n <= 1) return 1;

  return n * factorial(n - 1);
}

console.log(factorial(4)); // 4! = 4 * 3 * 2 * 1 = 24
```

반복되는 처리를 반복문 없이 구현할 수 있다는 장점이 있지만 무한 반복에 빠질 위험이 있고, 이로 인해 스택 오버플로 에러를 발생시킬 수 있으므로 주의해서 사용해야 한다.

### 7.3 중첩 함수

함수 내부에 정의된 함수를 중첩함수(nested function) 또는 내부 함수(inner function)라 한다. 중첩 함수를 포함하는 함수는 외부 함수(outer function)라 부른다.

중첩 함수는 스코프와 클로저에 깊은 관련이 있다.

### 7-4. 콜백 함수

자바스크립트 함수는 일급 객체이므로 함수의 매개변수를 통해 함수를 전달할 수 있다.

함수의 매개변수를 통해 다른 함수의 내부로 전달되는 함수를 콜백 함수(callback function)라고 하며, 매개변수를 통해 함수의 외부에서 콜백 함수를 전달받은 함수를 고차 함수(Higher-Order Function, HOF)라고 한다.

고차 함수는 매개변수를 통해 전달받은 콜백 함수의 호출 시점을 결정해서 호출한다.

고차 함수는 비동기 함수, 배열 고차 함수에서도 사용된다.

```js
// 비동기 함수에서의 콜백 함수
setTimeout(function () {
  console.log('1초 경과');
}, 1000);

// 배열 고차 함수에서의 콜백 함수
var res = [1, 2, 3].map(function (item) {
  return item * 2;
});
```

### 7-5. 순수 함수와 비순수 함수

순수 함수(pure function): 외부 상태에 의존하지도 않고 변경하지도 않는, 즉 부수 효과가 없는 함수.

순수 함수는 동일한 인수가 전달되면 언제나 동일한 값을 반환하는 함수다.

```js
var count = 0;

function increase(n) {
  return ++n;
}

count = increase(count);
console.log(count); // 1

count = increase(count);
console.log(count); // 2
```

<br />

비순수 함수(impure function): 외부 상태에 의존하거나 외부 상태를 변경하는, 즉 부수 효과가 있는 함수.

```js
var count = 0;

function increase() {
  return ++count;
}

increase();
console.log(count); // 1

increase();
console.log(count); // 2
```

함수가 외부 상태를 변경하면 상태 변화를 추적하기 어려워진다. 따라서 함수 외부 상태의 변경을 지양하는 순수 함수를 사용하는 것이 좋다.
