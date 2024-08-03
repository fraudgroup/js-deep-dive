# 24장 클로저

## 목차

1. [렉시컬 스코프](#1-렉시컬-스코프)
2. [함수 객체의 내부 슬롯 [[Environment]]](#2-함수-객체의-내부-슬롯-[[Environment]])
3. [클로저와 렉시컬 환경](#3-클로저와-렉시컬-환경)
4. [클로저의 활용](#4-클로저의-활용)
5. [캡슐화와 정보 은닉](#5-캡슐화와-정보-은닉)
6. [자주 발생하는 실수](#6-자주-발생하는-실수)

<br />

> A closure is the combination of a function and the lexical environment within which that function was declared.
>
> 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

## 1. 렉시컬 스코프

정적 스코프, 함수를 어디에 정의했는지에 따라 상위 스코프를 결정한다.

스코프의 실체는 실행 컨텍스트의 렉시컬 환경이다. 렉시컬 환경은 자신의 "외부 렉시컬 환경에 대한 참조"를 통해 상위 렉시컬 환경과 연결된다. (스코프 체인)

렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 저장할 참조값, 즉 상위 스코프에 대한 참조는 함수 정의가 평가되는 시점에 함수가 정의된 환경(위치)에 의해 결정된다.

<br />

## 2. 함수 객체의 내부 슬롯 [[Environment]]

함수 정의가 평가되어 함수 객체를 생성할 때 자신이 정의된 환경(위치)에 의해 결정된 상위 스코프의 참조를 함수 객체 자신의 내부 슬롯 [[Environment]]에 저장한다.

함수 정의가 평가되어 함수 객체를 생성하는 시점은 함수가 정의된 환경, 즉 상위 함수(또는 전역 코드)가 평가 또는 실행되고 있는 시점이며, 이때 현재 실행 중인 실행 컨텍스트인 상위 함수(또는 전역 코드)의 실행 컨텍스트가 [[Environment]]에 저장됩니다.

<br>

### 3. 클로저와 렉시컬 환경

외부함수보다 중첩함수가 더 오래 유지되는 경우 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 중첩 함수를 클로저라고 부른다.

자바스크립트의 모든 함수는 상위 스코프를 기억하므로 이론적으로 모든 함수는 클로저다.

하지만, 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적입니다.

클로저에 의해 참조되는 상위 스코프의 변수를 자유 변수라고 부른다. 클로저란 "함수가 자유변수에 대해 닫혀있다"라는 의미다. 좀 더 알기 쉽게 의역하면 "자유 변수에 묶여있는 함수"라고 할 수 있다.

이론적으로 클로저는 상위 스코프를 기억해야 하므로 불필요한 메모리의 점유를 걱정할 수도 있다. 하지만 모던 자바스크립트 엔진은 **최적화**가 잘 되어 있어서 클로저가 참조하고 있지 않는 식별자는 기억하지 않는다. 즉, 상위 스코프의 식별자 중 **기억해야할 식별자만 기억**한다. 기억해야 할 식별자를 기억하는 것은 불필요한 메모리 낭비라고 볼 수 없으므로 걱정할 필요가 없다.

<br>

### 4. 클로저의 활용

클로저는 **상태를 안전하게 변경하고 유지**하기 위해 사용한다. 다시 말해 상태가 의도치 않게 변경되지 않도록 **상태를 안적하게 은닉**하고 **특정 함수에서만 상태 변경을 허용**하기 위해 사용한다.

**함수가 호출될 때마다 호출된 횟수를 누적하는 카운터**

- 카운트 상태가 특정 함수를 호출하기 전까지 변경되지 않고 유지되어야한다.
- 이를 위해 카운트 상태는 해당 함수만이 변경할 수 있어야 한다.

```js
let num = 0;

const increase = function () {
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
```

- 카운트 상태는 전역 변수를 통해 관리 ⇒ 언제든지 누구나 접근할 수 있고 변경할 수 있다.

```js
const increase = function () {
  let num = 0;

  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 1
```

- 상태가 변경되기 이전 상태를 유지하지 못한다.

```js
const increase = (function () {
  let num = 0;

  // 클로저
  return function () {
    return ++num;
  };
})();

console.log(increase()); // 1
console.log(increase()); // 2
```

- increase 변수에 할당된 함수는 자신이 정의된 위치에 의해 결정된 상위 스코프인 즉시 실행 함수의 렉시컬 환경을 기억하는 클로저다.
- 즉시 실행 함수가 반환한 클로저는 카운트 상태를 유지하기 위한 자유 변수 num을 언제 어디서 호출하든지 참조하고 변경할 수 있다.
- 즉시 실행 함수는 한 번만 실행되므로 increase가 호출될 때마다 num 변수가 재차 초기화 될 일은 없을 것이다. 또한 num 변수는 외부에서 직접 접근할 수 없는 은닉된 private 변수이므로 전역 변수를 사용했을 때와 같이 의도되지 않은 변경을 걱정할 필요가 없기 때문에 더 안정적인 프로그래밍이 가능하다.

```js
const Counter = (function () {
  let num = 0;

  function Counter() {
    // this.num = 0; // ② 프로퍼티는 public하므로 은닉되지 않는다.
  }

  Counter.prototype.increase = function () {
    return ++num;
  };

  Counter.prototype.decrease = function () {
    return num > 0 ? --num : 0;
  };

  return Counter;
})();

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

```js
// 함수를 인수로 전달받고 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
function makeCounter(aux) {
  let counter = 0;

  return function () {
    counter = aux(counter);
    return counter;
  };
}

function increase(n) {
  return ++n;
}

function decrease(n) {
  return --n;
}

const increaser = makeCounter(increase); // ①
console.log(increaser()); // 1
console.log(increaser()); // 2

const decreaser = makeCounter(decrease); // ②
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

- makeCounter 함수를 호출해 함수를 반환할 때 반환된 함수는 자신만의 독립된 렉시컬 환경을 갖는다.
- 전역변수 increase와 decrease에 할당된 함수는 각각 자신만의 독립된 렉시컬 환경을 갖기 때문에 카운터를 유지하기 위한 저유 변수 counter를 공유하지 않는다.
- 카운터를 연동하여 증감이 가능한 카운터를 만드려면 렉시컬 환경을 공유하는 클로저를 만들어야 한다. 이를 위해서는 makeCounter 함수를 두 번 호출하지 말아야 한다.

```js
// 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
const counter = (function () {
  let counter = 0;

  return function (aux) {
    counter = aux(counter);
    return counter;
  };
})();

function increase(n) {
  return ++n;
}

function decrease(n) {
  return --n;
}

console.log(counter(increase)); // 1
console.log(counter(increase)); // 2

console.log(counter(decrease)); // 1
console.log(counter(decrease)); // 0
```

### 5. 캡슐화와 정보 은닉

캡슐화는 객체의 상태를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다. 캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 정보 은닉이라 한다.

자바스크립트는 public, private, protected 같은 접근 제한자를 제공하지 않는다.

객체의 모든 프로퍼티와 메서드는 기본적으로 public하다.

```js
const Person = (function () {
  let _age = 0; // private

  // 생성자 함수
  function Person(name, age) {
    this.name = name; // public
    _age = age;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };

  // 생성자 함수를 반환
  return Person;
})();

const me = new Person("Lee", 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person("Kim", 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined

me.sayHi(); // Hi! My name is Lee. I am 30.
```

- Person.prototype.sayHi 메서드가 단 한번 생성되는 클로저이기 때문에 여러 개의 인스턴스를 생성할 경우 \_age 변수 상태가 유지 되지 않는다.
- 하나의 동일한 상위 스코프를 사용하기 때문이다.
- 클래스에서 private 필드를 사용할 수 있다.

### 6. 자주 발생하는 실수

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    return function () {
      return id;
    };
  })(i);
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
}
```

- var 키워드로 선언한 변수는 함수 레벨 스코프를 갖기 때문에 전역 변수가 된다.
- 그래서 let 키워드로 바꿔주면 블록 레벨 스코프를 갖기 때문에 for 문의 코드 블록이 실행 될 때마다 새로운 렉시컬 환경이 생성된다. 그래서 식별자의 값을 유지 할 수 있다.

- 고차 함수를 사용하는 방법도 있다.

```js
const funcs = Array.from(new Array(3), (_, i) => () => i); // (3) [ƒ, ƒ, ƒ]

funcs.forEach((f) => console.log(f())); // 0 1 2
```
