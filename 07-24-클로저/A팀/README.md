# 24장 클로저

## 목차

1. [변수의 생명 주기](#1-변수의-생명-주기)
2. [전역 변수의 문제점](#2-전역-변수의-문제점)
3. [전역 변수의 사용을 억제하는 방법](#3-전역-변수의-사용을-억제하는-방법)

<br />

## 들어가며

MDN에서는 클로저에 대해 다음과 같이 정의하고 있다.

> "A closure is the combination of a function and the lexical environment within that function was declared."
>
> 클로저는 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.

<br />

## 1. 렉시컬 스코프

렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 저장할 참조값, 즉 상위 스코프에 대한 참조는 **함수 정의가 평가되는 시점**에 정의된 환경(위치)에 의해 결정된다.

<br />

## 2. 함수 객체의 내부 슬롯 [[Environment]]

함수는 자신의 내부 슬롯 `[[Environment]]`에 **자신이 정의된 환경**, 즉 상위 스코프의 참조를 저장한다.

`[[Environment]]`에 저장된 상위 스코프의 참조는 **현재 실행 중인 실행 컨텍스트의 렉시컬 환경**을 가리킨다. 이 내부 슬롯에 저장된 렉시컬 환경의 참조는 **"외부 렉시컬 환경에 대한 참조(OuterLexicalEnvironmentReference)"에 할당**된다.

<br />

## 3. 클로저와 렉시컬 환경

모든 함수는 자신의 내부 슬롯 `[[Environment]]`에 의해 언제나 상위 스코프의 식별자를 참조할 수 있으며 식별자에 바인딩된 값을 변경할 수도 있다.

```js
const x = 1;

function outer() {
  const x = 10; // 자유 변수
  const y = 20;
  const inner = function () {
    console.log(x); // 클로저
  };
  return inner;
}

const innerFunc = outer();
innerFunc(); // 10
```

`outer` 함수의 반환값이 `innerFunc`에 할당되면 `outer` 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거된다.

하지만 `outer` 함수의 렉시컬 환경까지 같이 소멸하는 것은 아니다.

`outer` 함수의 렉시컬 환경은 `inner` 함수의 `[[Environment]]` 내부 슬롯에 의해 참조되고 있고 `inner` 함수는 전역 변수 `innerFunc`에 의해 참조되고 있으므로 가비지 컬렉션의 대상이 되지 않는다.

> 📜 **클로저(closure)**
>
> 중첩 함수가 상위 스코프의 식별자를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우에 한정하는 것이 일반적이다.

> 📜 **자유 변수(free variable)**
>
> 클로저에 의해 참조되는 상위 스코프의 변수(위 예제의 `outer` 함수의 `x` 변수)
>
> 클로저란 자유 변수에 묶여있는 함수라고 할 수 있다.

<br />

## 4. 클로저의 활용

클로저는 상태를 **안전하게 변경하고 유지**하기 위해 사용한다.

즉, 상태를 안전하게 은닉하고 특정 함수에게만 상태 변경을 허용하기 위해 사용한다.

아래 예제 (1)은 `num`이 전역 변수이기 때문에 누구든지 변경할 수 있다.

```js
// (1) 전역 변수를 사용한 경우
let num = 0;

const increase = function () {
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

아래 예제는 전역 변수를 지역 변수로 변경하여 `num`의 상태를 `increase` 함수만 변경할 수 있게 되었다.

문제는 `increase` 함수가 호출될 때마다 `num`이 `0`으로 초기화돼서 이전 상태를 유지하지 못한다.

```js
// (2) 지역 변수를 사용한 경우
const increase = function () {
  let num = 0;

  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1
```

아래 예제는 클로저를 통해 `num`의 상태를 `increase` 함수만 변경할 수 있으면서 이전 상태를 유지할 수 있게 되었다.

```js
// (3) 클로저를 사용한 경우
const increase = (function () {
  let num = 0;

  return function () {
    return ++num;
  };
})();

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

직시 실행 함수와 클로저를 활용하면 상태를 안전하게 은닉할 수 있다.

<br />

## 5. 캡슐화와 정보 은닉

> 📜 **캡슐화(encapsulation)**
>
> 객체의 상태(state)를 나타내는 **프로퍼티**와 프로퍼티를 참조하고 조작할 수 있는 동작(behavior)인 **메서드**를 **하나로 묶는 것**을 말한다.
>
> 캡슐화는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 **정보 은닉**이라 한다.

대부분의 객체지향 프로그래밍 언어는 클래스를 정의하고 그 클래스를 구성하는 멤버에 대하여 `public`, `private`, `protected` 같은 접근 제한자를 선언하여 공개 범위를 한정할 수 있다.

자바스크립트 객체의 모든 프로퍼티와 메서드는 기본적으로 `public`하다.

즉시 실행 함수와 클로저를 통해서 `private`를 구현해 보자.

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

  // 생성자 함수 반환
  return Person;
})();

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! my name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined

// _age 변수 값이 변경된다!
me.sayHi(); // Hi! My name is Lee. I am 30.
```

위 예제는 `private`를 구현하긴 했지만 문제가 있다. 여러 개의 인스턴스를 생성할 경우 `_age` 변수의 상태가 유지되지 않는다는 것이다.

인스턴스 메서드를 사용, Symbol, WeakMap 등을 사용하여 `private`를 흉내 내기도 했으나 근본적인 해결책이 되지는 않는다.

이는 25장 클래스에서 살펴보자.

<br />

## 6. 자주 발생하는 실수

아래는 클로저를 사용할 때 자주 발생할 수 있는 실수를 보여주는 예제다.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}

// 예상
// 0 1 2

// 현실
// 3 3 3
```

`var` 키워드로 선언한 `i` 변수는 함수 레벨 스코프를 갖기 때문에 전역 변수로 인해 발생한 실수다.

다음은 클로저를 사용해 바르게 동작하는 예제다.

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

// 0 1 2
```

즉시 실행 함수가 반환하는 중첩 함수(클로저)는 상위 스코프를 기억하고 매개 변수 `id`는 자유 변수가 되어 값이 유지된다.

다음은 `let` 키워드(블록 레벨 스코프)를 사용해 바르게 동작하는 예제다.

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]());
}

// 0 1 2
```

`let`, `const` 키워드로 선언한 변수를 사용하면 반복문의 코드 블록이 반복 실행될 때마다 새로운 렉시컬 환경이 생성된다.
