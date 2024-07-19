# 17장 생성자 함수에 의한 객체 생성

## 목차

1. [Object 생성자 함수](#1-Object-생성자-함수)
2. [생성자 함수](#2-생성자-함수)

<br />

## 1. Object 생성자 함수

new 연산자와 함께 Object 생성자 함수를 호출하면 빈 객체를 생성하여 반환한다.

생성자 함수 : new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수<br>
인스턴스 : 생성자 함수에 의해 생성된 객체

Object 생성자 함수 이외에도 String, Number, Boolean, Function, Array, Date, RegExp, Promise 등의 빌트인 생성자 함수를 제공한다.

<br />

## 2. 생성자 함수

### 1. 객체 리터럴에 의한 객체 생성 방식의 문제점

단 하나의 객체만 생성한다.

객체는 프로퍼티를 통해 객체 고유의 상태를 표현한다.<br>
메서드를 통해 상태 데이터인 프로퍼티를 참조하고 조작하는 동작을 표현한다.<br>
⇒ 객체마다 프로퍼티 값은 달라도 메서드는 동일한 경우가 일반적이다.

> 객체 리터럴에 의해 객체를 생성하는 경우 프로퍼티 구조가 동일함에도 불구하고 매번 같은 프로퍼티와 메서드를 기술해야 한다.

<br>

### 2. 생성자 함수에 의한 객체 생성 방식의 장점

생성자 함수를 사용하여 프로퍼티 구조가 동일한 객체 여러 개를 간편하게 생성할 수 있다.

일반 함수와 동일한 방법으로 생성자 함수를 정의하고 new 연산자와 함께 호출하며 해당 함수는 생성자 함수로 동작한다.

<br>

### 3. 생성자 함수의 인스턴스 생성 과정

1) 인스턴스 생성과 this 바인딩 (런타임 이전)
    - 암묵적으로 빈 객체(인스턴스)를 생성
    - 인스턴스 this 바인딩
2) 인스턴스 초기화
    - 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 인스턴스 프로퍼티에 할당하여 초기화하거나 고정값을 할당
    - 개발자가 기술
3) 인스턴스 반환
    - this를 암묵적으로 반환
    - this가 아닌 객체를 명시적으로 반환 ⇒ this 반환 X, 명시된 객체 반환
    - 원시값 반환 ⇒ 원시값 무시, this 반환

<br>

생성자 함수 내부에서 this가 아닌 값을 반환 하는 것은 생성자 함수의 기본 동작을 훼손<br>
⇒ return 문 반드시 생략

<br>

### 4. 내부 메서드 [[Call]]과 [[Construct]]

함수 = 객체<br>
⇒ 일반 객체가 가지고 있는 내부 슬롯과 내부 매서드를 모두 가지고 있다.<br>
⇒ 일반 객체와 동일하게 동작할 수 있다.

함수는 일반 객체와 다르게 호출할 수 있다.<br>
함수 객체만을 위한 [[Environment]], [[FormalParameters]] 등의 내부슬롯과 [[Call]], [[Construct]] 같은 내부 메서드를 추가로 가지고 있다.

일반 함수로서 호출 : [[Call]] 호출 <br>
new 연산자와 함께 생성자 함수로서 호출 : [[Construct]] 호출

내부 매서드 [[Call]]을 갖는 함수 객체 ⇒ callable
내부 메서드 [[Construct]]를 갖는 함수 객체 ⇒ constructor
내부 메서드 [[Construct]]를 갖지 않는 함수 객체 ⇒ non-constructor

함수 객체는 반드시 callable이어야 한다.

<br>

### 5. constructor와 non-constructor의 구분

constructor: 함수 선언문, 함수 표현식, 클래스<br>
non-constructor: 메서드(ES6 메서드 축약 표현), 화살표 함수

non-constructor인 함수 객체를 생성자 함수로서 호출하면 에러가 발생한다.

생성자 함수로서 호출될 것을 기대하고 정의하지 않은 일반 함수에 new 연산자를 붙여 호출하면 생성자 함수처럼 동작할 수 있다는 것이다.

<br>

### 6. new 연산자

일반 함수와 생성자 함수에 특별한 형식적 차이는 없다.

new 연산자와 함께 함수를 호출 : 생성자 함수로 동작, [[Construct]] 호출<br>
non-constructor가 아닌 constructor이어야 한다.

new 연산자 없이 생성자 함수 호출 : 일반 함수로 호출, [[Call]] 호출

생성자 함수는 일반적으로 첫 문자를 대문자로 기술하는 파스칼 케이스로 명명하여 일반 함수와 구별할 수 있도록 노력한다.

<br>

### 7. new.target

실수는 언제나 발생한다.

new 연산자와 함께 생성자 함수로서 호출 : 함수 내부의 new.target === 함수 자신<br>
new 연산자 없이 일반 함수로서 호출 : 함수 내부의 new.target === undefined

함수 내부에서 new.target 사용하여 생성자 함수로 호출했는지 확인하고 아닐 경우 new 연산자와 함께 재귀 호출을 통해 생성자 함수로서 호출할 수 있다.

```js
function Circle(radius) {
  if (!new.target) {
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

new.target은 ES6에서 도입된 최신 문법으로 IE 지원하지 않는다. 대신 스코프 세이프 생성자 패턴을 사용할 수 있다.

```js
function Circle(radius) {
  if (!(this instanceof Circle)) {
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

대부분의 빌트인 생성자 함수는 new 연산자와 함께 호출되었는지 확인 후 적절한 값을 반환한다.

String, Number, Boolean 생성자 함수는 new 연산자 없이 호출하면 문자열, 숫자, 불리언 값을 반환한다. 이를 통해 데이터 타입을 변환하기도 한다.