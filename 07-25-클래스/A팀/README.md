# 25장 클래스

## 목차

1. [클래스는 프로토타입의 문법적 설탕인가?](#1-클래스는-프로토타입의-문법적-설탕인가)
2. [클래스 정의](#2-클래스-정의)
3. [클래스 호이스팅](#3-클래스-호이스팅)
4. [인스턴스 생성](#4-인스턴스-생성)
5. [메서드](#5-메서드)
6. [클래스의 인스턴스 생성 과정](#6-클래스의-인스턴스-생성-과정)
7. [프로퍼티](#7-프로퍼티)
8. [상속에 의한 클래스 확장](#8-상속에-의한-클래스-확장)

<br />

## 1. 클래스는 프로토타입의 문법적 설탕인가?

클래스는 생성자 함수와 같이 **프로토타입 기반의 객체지향**을 구현했다는 점에서 **매우 유사하지만 더 견고하고 명료하다**.

그렇다고 클래스가 자바스크립트의 다른 객체 생성 방식보다 우월한 것은 아니다.

따라서 클래스를 프로토타입 기반 객체 생성 패턴의 단순한 문법적 설탕이라고 보기보다는 **새로운 객체 생성 메커니즘**으로 보는 것이 좀 더 합당하다.

<br />

클래스와 생성자 함수의 차이는 다음과 같다.

- 클래스를 `new` 연산자 없이 호출하면 에러가 발생한다. 하지만 생성자 함수를 `new` 연산자 없이 호출하면 일반 함수로서 호출된다.
- 클래스는 상속을 지원하는 `extends`와 `super` 키워드를 제공한다. 하지만 생성자 함수는 `extends`와 `super` 키워드를 지원하지 않는다.
- 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다. 하지만 함수 선언문으로 정의된 생성자 함수는 함수 호이스팅이, 함수 표현식으로 정의한 생성자 함수는 변수 호이스팅이 발생한다.
- 클래스 내의 모든 코드에는 암묵적으로 `strict mode`가 지정되어 실행되며 `strict mode`를 해제할 수 없다. 하지만 생성자 함수는 암묵적으로 `strict mode`가 지정되지 않는다.
- 클래스의 `constructor`, 프로토타입 메서드, 정적 메서드는 모두 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `false`다. 다시 말해, 열거되지 않는다.

<br />

## 2. 클래스 정의

`class` 키워드를 사용하여 정의한다. 파스칼 케이스를 사용하는 것이 일반적이다.

클래스는 함수로서 일급 객체의 특징을 지닌다.

클래스 몸체에서 정의할 수 있는 메서드는 세 가지가 있다.

- constructor(생성자)
- 프로토타입 메서드
- 정적 메서드

```js
// 클래스 선언문
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // public
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }

  // 정적 메서드
  static sayHello() {
    console.log('Hello!');
  }
}

// 인스턴스 생성
const me = new Person('Lee');

// 인스턴스의 프로퍼티 참조
console.log(me.name); // Lee

// 프로토타입 메서드 호출
me.sayHi(); // Hi! My name is Lee

// 정적 메서드 호출
Person.sayHello(); // Hello!
```

<br />

## 3. 클래스 호이스팅

클래스 선언문으로 정의한 클래스는 런타임 이전에 평가되어 함수 객체(constructor)를 생성한다.

함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다. 프로토타입과 생성자 함수는 언제나 쌍으로 존재하기 때문이다.

단, 클래스는 클래스 정의 이전에 참조할 수 없다.

```js
const Person = '';
{
  // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
  console.log(Person);
  // ReferenceError: Cannot access 'Person' before initialization

  // 클래스 선언문
  class Person {}
}
```

클래스 선언문도 호이스팅이 발생하지만 `let`, `const` 키워드로 선언한 변수처럼 일시적 사각지대(TDZ)에 빠지기 때문에 호이스팅이 발생하지 않는 것처럼 동작한다.

<br />

## 4. 인스턴스 생성

클래스는 생성자 함수이며 `new` 연산자와 함께 호출되어 인스턴스를 생성한다.

```js
class Person {}

const me = new Person();
```

클래스로 인스턴스 생성 시 주의사항

- 반드시 `new` 연산자와 함께 호출해야 한다.
- 클래스 표현식으로 정의된 클래스는 식별자를 사용해 인스턴스를 생성해야 한다.

```js
const Person = class MyClass {};

// ❌ new 연산자 없이 호출
const me = Person();
// TypeError: Class constructor MyClass cannot be invoked without 'new'

// ❌ 표현식으로 정의된 클래스를 식별자가 아닌 클래스 이름을 사용해 인스턴스 생성
const you = new MyClass();
// ReferenceError: MyClass is not defined

// ✅
const we = new Person();
```

<br />

## 5. 메서드

클래스 몸체에는 0개 이상의 **메서드만 선언**할 수 있다.

클래스 몸체에 정의할 수 있는 메서드는 세 가지가 있다.

- constructor(생성자)
- 프로토타입 메서드
- 정적 메서드

<br />

> 📜 **클래스 정의에 대한 새로운 제안 사양**
>
> ES11 사양에 따르면 인스턴스 프로퍼티는 반드시 `constructor` 내부에 정의해야 한다.
>
> ES13 사양에서 클래스 내부에 바로 프로퍼티를 정의할 수 있다.

<br />

### 5-1. constructor

`constructor`는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드다.

클래스 정의가 평가되면 `constructor`의 기술된 동작을 하는 함수 객체가 생성된다.

> 📜 **클래스의 constructor 메서드와 프로토타입의 constructor 프로퍼티**
>
> 클래스의 `constructor` 메서드와 프로토타입의 `constructor` 프로퍼티는 직접적인 관련이 없다.
>
> 프로토타입의 `constructor` 프로퍼티는 모든 프로토타입이 가지고 있는 프로퍼티이며, 생성자 함수를 가리킨다.

`constructor`의 몇 가지 특징을 살펴보자.

- `constructor`는 클래스 내에 최대 한 개만 존재할 수 있다.
- `constructor`를 생략하면 클래스에 빈 `constructor`가 암묵적으로 정의된다.
- `constructor`는 별도의 반환문을 갖지 않아야 한다.

<br />

### 5-2. 프로토타입 메서드

클래스 몸체에서 정의한 메서드는 기본적으로 프로토타입 메서드가 된다.

<br />

### 5-3. 정적 메서드

정적 메서드는 **인스턴스를 생성하지 않아도 호출할 수 있는 메서드**를 말한다.

클래스에서는 메서드에 `static` 키워드를 붙이면 정적 메서드(클래스 메서드)가 된다.

클래스는 **클래스 정의가 평가되는 시점에 함수 객체가 되므로** 인스턴스와 달리 별다른 생성 과정이 필요 없다. 따라서 정적 메서드는 클래스 정의 이후 **인스턴스를 생성하지 않아도 호출할 수 있다**.

<br />

### 5-4. 정적 메서드와 프로토타입 메서드의 차이

정적 메서드와 프로토타입 메서드의 차이는 다음과 같다.

- 정적 메서드와 프로토타입 메서드는 자신이 속해 있는 프로토타입 체인이 다르다.
- 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
- 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.

인스턴스 프로퍼티를 참조하려면 `this`를 사용해야 한다. 메서드를 호출할 때 정적 메서드와 프로토타입 메서드 내부의 `this` 바인딩이 다르기 때문에 인스턴스 프로퍼티를 참조해야 한다면 프로토타입 메서드를 사용해야 한다.

<br />

> 📜 **ES6에 추가된 표준 빌트인 객체 Number의 정적 메서드**
>
> `isFinite`, `isNaN`, `parseFloat`, `PaseInt` 등의 빌트인 전역 함수를 표준 빌트인 객체 `Number`의 정적 메서드로 추가 구현했다. 이는 빌트인 전역 함수보다 엄격하다.

<br />

### 5-5. 클래스에서 정의한 메서드의 특징

클래스에서 정의한 메서드는 다음과 같은 특징을 갖는다.

- `function` 키워드를 생략한 메서드 축약 표현을 사용한다.
- 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
- 암묵적으로 `strict mode`로 실행된다.
- `for ... in` 문이나 `Object.keys` 메서드 등으로 열거할 수 없다. 즉, 프로퍼티의 열거 가능 여부를 나타내며, 불리언 값을 갖는 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `false`다.
- 내부 메서드 `[[Construct]]`를 갖지 않는 `non-constructor`다. 따라서 `new` 연산자와 함께 호출할 수 없다.

<br />

## 6. 클래스의 인스턴스 생성 과정

`new` 연산자와 함께 클래스를 호출하면 생성자 함수와 마찬가지로 클래스의 내부 메서드 `[[Construct]]`가 호출된다.

생성자 함수의 인스턴스 생성 과정과 유사한 과정을 거쳐 인스턴스가 생성된다.

<br />

**1. 인스턴스 생성과 this 바인딩**

`construct`의 내부 코드가 실행되기에 앞서 암묵적으로 빈 객체(인스턴스)가 생성된다.

이때 클래스의 `prototype` 프로퍼티가 가리키는 객체가 인스턴스의 프로토타입으로 설정된다.

인스턴스는 `this`에 바인딩된다.

<br />

**2. 인스턴스 초기화**

`constructor`의 내부 코드가 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화한다.

<br />

**3. 인스턴스 반환**

클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.

```js
class Person {
  // 생성자
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Person {}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.name = name;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
  }
}
```

<br />

## 7. 프로퍼티

### 7-1. 인스턴스 프로퍼티

ES6 클래스의 인스턴스 프로퍼티는 `constructor` 내부에서 정의해야 한다. 또한 언제나 `public`하다.

ES13 클래스에서는 클래스 내부에서 정의할 수 있으며 `private` 필드도 지원한다.

<br />

### 7-2. 접근자 프로퍼티

자체적으로는 값(`[[Value]]` 내부 슬롯)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수로 구성된 프로퍼티다.

```js
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lasName;
  }

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  set fullName() {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

me.fullName = 'Bongchan Jeong';
console.log(me); // {firstNmae: "Bongchan", lastName: "Jeong"}

console.log(me.fullName); // Bongchan Jeong
```

`getter`와 `setter`는 인스턴스 프로퍼티처럼 사용된다.

하지만 접근자 프로퍼티는 인스턴스 프로퍼티가 아닌 프로토타입의 프로퍼티가 된다.

<br />

### 7-3. 클래스 필드 정의 제안

클래스 필드는 클래스가 생성할 인스턴스의 프로퍼티를 가리키는 용어다.

자바스크립트의 클래스에서 인스턴스 프로퍼티를 선언하고 초기화하려면 반드시 `constructor` 내부에서 `this`에 프로퍼티를 추가해야 한다.

하지만 ES13에서 클래스 내부에서 클래스 필드를 정의할 수 있게 되었다.

클래스 필드에 함수를 할당하는 것도 가능하지만 모든 클래스 필드는 프로토타입 프로퍼티가 아닌 인스턴스 프로퍼티가 되기 때문에 권장하지 않는다.

```js
class Person {
  name = 'Lee';
  address = 'Seoul';

  constructor(name) {
    this.name = name;
    console.log(address); // ReferenceError: address is not defined
  }

  getName = function () {
    return this.name;
  };
}
```

<br />

### 7-4. private 필드 정의 제안

자바스크립트는 캡슐화를 완전하게 지원하지 않는다. 하지만 ES13에서 `private` 필드를 정의할 수 있게 되었다.

`private` 필드의 선두에는 `#`을 붙여준다. `private` 필드를 참조할 때도 `#`을 붙여주어야 한다.

`private` 필드는 클래스 외부에서 참조할 수 없다. 다만 접근자 프로퍼티를 통해 간접적으로 접근하는 방법은 유효하다.

```js
class Person {
  // private 필드 정의
  #name = '';

  constructor(name) {
    this.#name = name;
  }

  // 접근자 프로퍼티
  get name() {
    // private 필드를 참조하여 반환한다.
    return this.#name;
  }
}

const me = new Person('Lee');

console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class

console.log(me.name); // Lee
```

`private` 필드는 반드시 클래스 몸체에 정의해야 한다. `constructor`에 정의하면 에러가 발생한다.

`public` 필드는 어디서든 참조할 수 있지만 `private` 필드는 클래스 내부에서만 참조할 수 있다.

| 접근 가능성                 | public | private |
| --------------------------- | ------ | ------- |
| 클래스 내부                 | ✅     | ✅      |
| 자식 클래스 내부            | ✅     | ❌      |
| 클래스 인스턴스를 통한 접근 | ✅     | ❌      |

<br />

### 7-5. static 필드 정의 제안

`static` 키워드를 사용하여 정적 메서드를 정의할 수 있다. 하지만 `static` 키워드를 사용하여 정적 필드를 정의할 수는 없었다.

ES13에서는 `static` 키워드를 사용하여 정적 필드를 정의할 수 있게 되었다.

```js
class MyMath {
  static PI = 22 / 7;

  static #num = 10;

  static increment() {
    return ++MyMath.#num;
  }
}

console.log(MyMath.PI); // 3.142857142857143
console.log(MyMath.increment()); // 11
```

<br />

## 8. 상속에 의한 클래스 확장

### 8-1. 클래스 상속과 생성자 함수 상속

클래스는 `extends` 키워드가 기본적으로 제공되어 상속을 통해 다른 클래스를 확장할 수 있다.

생성자 함수는 상속을 통해 다른 생성자 함수를 확장할 수 있는 문법이 제공되지 않는다.

```js
class Animal {
  constructor(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  eat() {
    return 'eat';
  }

  move() {
    return 'move';
  }
}

class Bird extends Animal {
  fly() {
    return 'fly';
  }
}

const bird = new Bird(1, 5);

console.log(bird); // Bird {age: 1, weight: 5}
console.log(bird instanceof Bird); // true
console.log(bird instanceof Animal); // true

console.log(bird.eat()); // eat
console.log(bird.move()); // move
console.log(bird.fly()); // fly
```

<br />

### 8-2. extends 키워드

상속을 통해 클래스를 확장하려면 `extends` 키워드를 사용한다.

```js
// 수퍼(베이스/부모)클래스
class Base {}

// 서브(파생/자식)클래스
class Derived extends Base {}
```

클래스의 상속은 인스턴스 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다.

이를 통해 프로토타입 메서드, 정적 메서드 모두 상속이 가능하다.

<br />

### 8-3. 동적 상속

`extends` 키워드는 클래스뿐만 아니라 `[[Construct]]` 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.

단, `extends` 키워드 앞에는 반드시 클래스가 와야 한다.

```js
function Base1() {}

class Base2 {}

let condition = true;

class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

<br />

### 8-4. 서브클래스의 constructor

서브클래스에서 `constructor`를 생략하면 클래스에 다음과 같은 `constructor`가 암묵적으로 정의된다.

`args`는 `new` 연산자와 함께 클래스를 호출할 때 전달한 인수의 리스트다.

```js
constructor(...args) { super(...args); }
```

`super()`는 수퍼클래스의 `constructor`(super-constructor)를 호출하여 인스턴스를 생성한다.

<br />

> 📜 **Rest 파라미터**
>
> 매개변수에 ...을 붙이면 Rest 파라미터가 된다. Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받는다.

<br />

### 8-5. super 키워드

`super` 키워드는 함수처럼 호출할 수도 있고 `this`와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.

- `super`를 호출하면 수퍼클래스의 `constructor`(super-constructor)를 호출한다.
- `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

<br />

#### super 호출

`super`를 호출하면 수퍼클래스의 `constructor`(super-constructor)를 호출한다.

```js
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  constructor(a, b, c) {
    super(a, b);
    this.c = c;
  }
}

const derived = new Derived(1, 2, 3);
console.log(derived); // Derived {a: 1, b: 2, c: 3}
```

`super`를 호출할 때 주의사항은 다음과 같다.

- 서브클래스에서 `constructor`를 생략하지 않는 경우 서브클래스의 `constructor`에서는 반드시 `super`를 호출해야한다.
- 서브클래스의 `constructor`에서 `super`를 호출하기 전에는 `this`를 참조할 수 없다.
- `super`는 반드시 서브클래스의 `constructor`에서만 호출한다. 서브클래스가 아닌 클래스의 `constructor`나 함수에서 `super`를 호출하면 에러가 발생한다.

<br />

#### super 참조

메서드 내에서 `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

- 서브클래스의 프로토타입 메서드 내에서 `super.메서드명`은 수퍼클래스의 프로토타입 메서드 `메서드명`을 가리킨다.
- 서브클래스의 정적 메서드 내에서 `super.메서드명`은 수퍼클래스의 정적 메서드 `메서드명`을 가리킨다.

`super` 참조가 동작하기 위해 메서드는 내부 슬롯 `[[HomeObject]]`를 가지며, 자신을 바인딩하고 있는 객체를 가리킨다.

```js
super = Object.getPrototypeOf([[HomeObject]])
```

⚠️ 주의할 것은 `ES6`의 축약 표현으로 정의된 함수만이 `[[HomeObject]]`를 갖는다는 것이다.

```js
const obj = {
  // ES6의 메서드 축약 포현으로 [[HomeObject]]를 갖는다.
  foo() {},
  // ES6의 메서드 축약 표현이 아닌 일반 함수로 [[HomeObject]]를 갖지 않는다.
  bar: function () {},
};
```

<br />

### 8-6. 상속 클래스의 인스턴스 생성 과정

<br />

**1. 서브클래스의 super 호출**

자바스크립트 엔진은 수퍼클래스와 서브클래스를 구분하기 위해 `"base"` 또는 `"derived"`를 값으로 갖는 내부 슬롯 `[[ConstructorKind]]`를 갖는다.

다른 클래스를 상속받지 않는 클래스(그리고 생성자 함수)는 내부 슬롯 `[[ConstructorKind]]`의 값이 `"base"`로 설정된다.

다른 클래스를 상속받는 서브클래스는 내부 슬롯 `[[ConstructorKind]]`의 값이 `"derived"`로 설정된다.

**서브클래스는 자신이 직접 인스턴스를 생성하지 않고 수퍼클래스에게 인스턴스 생성을 위임한다. 이것이 바로 서브클래스의 `constructor`에서 반드시 `super`를 호출해야 하는 이유다.**

<br />

**2. 수퍼클래스의 인스턴스 생성과 this 바인딩**

수퍼클래스의 `constructor` 내부의 코드가 실행되기 이전에 암묵적으로 빈 객체(인스턴스)를 생성한다.

하지만 `new` 연산자와 함께 호출된 클래스는 서브클래스로 `new` 연산자와 함께 호출된 함수를 가리키는 `new.target`은 서브클래스를 가리킨다.

따라서 인스턴스는 `new.target`이 가리키는 서브클래스가 생성한 것으로 처리된다.

<br />

**3. 수퍼클래스의 인스턴스 초기화**

수퍼클래스의 `constructor`가 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화한다.

이때 `this`는 서브클래스의 인스턴스가 바인딩되어 있다.

<br />

**4. 서브클래스 constructor로의 복귀와 this 바인딩**

서브클래스의 `constructor` 내부의 `super`의 호출이 종료되고 제어 흐름이 서브클래스의 `constructor`로 돌아온다.

서브클래스는 별도의 인스턴스를 생성하지 않고 `super`가 반환한 인스턴스를 `this`에 바인딩하여 그대로 사용한다.

이처럼 `super`가 호출되지 않으면 인스턴스가 생성되지 않으며, `this` 바인딩도 할 수 없다. 서브클래스의 `constructor`에서 `super`를 호출하기 전에는 `this`를 참조할 수 없는 이유가 바로 이 때문이다.

<br />

**5. 서브클래스의 인스턴스 초기화**

`super` 호출 이후, 서브클래스의 `constructor`에 기술되어 있는 인스턴스 초기화가 실행된다.

<br />

**6. 인스턴스 반환**

클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.

<br />

### 8-7. 표준 빌트인 생성자 함수 확장

`extends` 키워드 다음에는 클래스뿐만 아니라 `[[Construct]]` 내부 메서드를 갖는 함수 객체로 평가될 수 있는 모든 표현식을 사용할 수 있다.

`String`, `Number`, `Array` 같은 표준 빌트인 객체도 `[[Construct]]` 내부 메서드를 갖는 생성자 함수이므로 `extends` 키워드를 사용하여 확장할 수 있다.

```js
class MyArray extends Array {
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);
console.log(myArray); // MyArray(4) [1, 1, 2, 3]

console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
console.log(myArray.average()); // 1.75
```

⚠️ 주의할 것은 `Array.prototype`의 메서드 중에서 `map`, `filter`와 같이 새로운 배열을 반환하는 메서드가 `MyArray` 클래스의 인스턴스를 반환한다는 것이다.

만약 `Array`의 인스턴스를 반환하면 `MyArray` 클래스의 메서드와 메서드 체이닝이 불가능하다.
