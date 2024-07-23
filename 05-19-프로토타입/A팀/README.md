# 19장 프로토타입 (01)

- 자바스크립트는 명령형, 함수형, 프로토타입 기반 객체지향 프로그래밍을 지원하는 멀티 패러다임 프로그래밍 언어다.
- ⭐️ 자바스크립트는 객체 기반의 프로그래밍 언어이며 `자바스크립트를 이루고 있는 거의 "모든 것"이 객체`다.
  - 원시 타입의 값을 제외한 나머지 값들(함수, 배열, 정규 표현식 등)은 모두 객체다.

> #### 클래스 (class)
>
> ES6에서 클래스가 도입되었다.
> 클래스와 생성자 함수는 모드 프로토타입 기반의 인스턴스를 생성하지만 정확히 동일하게 동작하지는 않는다. 클래스는 생성자 함수보다 엄격하며 클래스는 생성자 함수에서는 제공하지 않는 기능도 제공한다.

## 01. 객체지향 프로그래밍

- 사람은 이름, 주소, 성별, 나이, 신장, 체중, 학력, 성격, 직업 등 다양한 속성을 갖는다. 이러한 속성을 추상화하여 객체의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 객체지향 프로그래밍이라 한다.
  - ⭐️ 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 `추상화`라 한다.
  - 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 `객체`라 한다.
  - 객체는 상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조라고 할 수 있다.
  - ⭐️ 이때 객체의 상태 데이터를 프로퍼티, 동작을 메서드라 부른다.

```js
const circle = {
  radius: 5, // 반지름: 프로퍼티(상태)

  // 원의 지름 2r: 메서드(동작)
  getDiameter() {
    return 2 * Math.PI * this.radius;
  },

  // 원의 넓이 2 * 3.14 * r: 메서드(동작)
  getPerimeter() {
    return 2 * Math.PI * this.radius;
  },

  // 원의 넓이 3.14 * rr : 메서드(동작)
  getArea() {
    return Math.PI * this.radius ** 2;
  },
};
```

## 02. 상속(inheritance)과 프로토타입

- 상속은 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그래도 사용할 수 있는 것을 말한다.
- ⭐️ 자바스크립트는 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다.
  - 중복을 제거하는 방법은 기존의 코드를 적극적으로 재사용하는 것이다.
- 상속은 코드의 재사용이란 관점에서 매우 유용하다.

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true
```

## 03. 프로토타입 객체

- 프로토타입 객체는 객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위해 사용된다.
- 상위(부모) 객체에서 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자유롭게 사용할 수 있다.
- 모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조다.
  - `null`인 경우도 있다.
- 객체가 생성될 때 객체 생성 방식에 의해 프로토타입이 결정되고 `[[Prototype]]`에 저장된다.
- ⭐️ 모든 객체는 하나의 프로토타입을 갖는다.
  - 모든 프로토타입은 생성자 함수와 연결되어 있다.
  - 즉, 객체와 프로토타입과 생성자 함수는 서로 연결되어 있다.

### 03-01. `__proto__`접근자 프로퍼티

- 모든 객체는 `__proto__`접근자 프로퍼티를 통해 자신의 `[[Prototype]]` 내부 슬롯에 간접적으로 접근할 수 있다.

1. `__proto__`는 접근자 프로퍼티다.

   - 내부 슬롯은 프로퍼티가 아니다.
   - 자바스크립트는 원칙적으로 내부 슬롯과 내부 메서드에 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 않는다.
   - `[[Prototype]]`내부 슬롯에도 직접 접근할 수 없으며 `__proto__` 접근자 프로퍼티를 동해 간접적으로 `[[Prototype]]` 내부 슬롯의 값(프로토타입)에 접근할 수 있다.

   ```js
   const obj = {};
   const parent = { x: 1 };

   // getter 함수인 get __proto__가 호출되어 obj 객체의 프로토타입을 취득
   obj.__proto__;

   // setter 함수인 set __proto__가 호출되어 obj 객체의 프로토타입을 교체
   obj.__proto__ = parent;

   console.log(obj.x); // 1
   ```

   > 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수(`[[Get]], [[Set]]`)프로퍼티 어트리뷰트로 구성된 프로퍼티다.

2. `__proto__` 접근자 프로퍼티는 상속을 통해 사용된다.

   - `__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 프로퍼티가 아니라 `Object.prototype`의 프로퍼티다.
   - 모든 객체는 상속을 통해 `Object.prototype.__proto__`접근자 프로퍼티를 사용할 수 있다.
     > #### ⭐️ Object.prototype
     >
     > 모든 객체는 프로토타입의 계층 구조인 프로토타입 체인에 묶여 있다. 자바스크립트 엔진은 객체의 프로퍼티에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 `__proto__` 접근자 프로퍼티가 가리키는 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색한다.
     > 프로토타입 체인의 최상위 객체는 `Object.prototype`이며, 이 객체의 프로퍼티와 메서드는 모든 객체에 상속된다.

3. `__proto__`접근자 프로퍼티를 통해 프로토타입에 접근하는 이유

   - `[[Prototype]]` 내부 슬롯의 값(프로토타입)에 접근하기 위해 접근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서다.
   - 프로토타입 체인은 단뱡향 링크드 리스트로 구현되어야 한다.
     - 프로퍼티 검색 방향이 한쪽 방향으로만 흘러가야 한다.
     - 서로가 자신의 프로토타입이 되는 무한루프의 프로토타입 체인이 만들어 질 수 있으므로 무조건적으로 프로토타입을 교체할 수 없도록 `__proto__` 접근자 프로퍼티를 통해 접근하고 교체하도록 구현되어 있다.

   ```js
   const parent = {};
   const child = {};

   // child의 프로토타입을 parent로 설정
   child.__proto__ = parent;
   // parent의 프로토타입을 child로 설정
   parent.__proto__ = child; // TypeError: Cyclic __proto__ value
   ```

4. `__proto__` 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.

   - 모든 객첵 `__proto__`접근자 프로퍼티를 사용할 수 있는 것은 아니다.
   - 직접 상속을 통하여 `Object.prototype`을 상속받지 않는 객체를 생성할 수도 있개 때문에 `__proto__` 접근자 프로퍼티를 사용할 수 없는 경우도 존재한다.
     - 프로토타입의 참조를 취득하고 싶은 경우에는 `Object.getPrototypeOf` 메서드를 사용을 권장한다.
     - 프로토타입을 교체하고 싶은 경우에는 `Object.setPrototypeOf` 메서드를 사용할 것을 권장한다.

   ```js
   // obj는 프로토타입 체인의 종점이다. 따라서 Object.__proto__를 상속받을 수 없다.
   const obj = Object.create(null);

   // obj는 Object.__proto__를 상속받을 수 없다.
   console.log(obj.__proto__); // undefined

   // 따라서 __proto__보다 Object.getPrototypeOf 메서드를 사용하는 편이 좋다.
   console.log(Object.getPrototypeOf(obj)); // null
   ```

### 03-02. 함수 객체의 prototype 프로퍼티

- ⭐️ 함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.
  - 생성자 함수로서 호출할 수 없는 함수 non-constructor인 화살표 함수와 ES6 메서드 축약 표현으로 정의한 메서드는 prototype 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.

```js
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty("prototype"); // true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty("prototype"); // false

// 화살표 함수는 non-constructor다.
const Person = (name) => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty("prototype")); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor다.
const obj = {
  foo() {},
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty("prototype")); // false

// non-constructor는 프토로타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```

- ⭐️ 모든 객체가 가지고 있는(엄밀히 말하면 `Object.prototype`으로부터 상속받은) `__proto__`접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 결국 동일한 프로토타입을 가리킨다.

  - 하지만 이들 프로퍼티를 사용하는 주체가 다르다.
    | 구분 | 소유 | 값 | 사용 주체 | ⭐️ 사용 목적 |
    |-------------------|------------|------------------|---------------|-------------------------------------------------------------------------------------|
    | `__proto__` 접근자 프로퍼티 | 모든 객체 | 프로토타입의 참조 | 모든 객체 | 객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용 |
    | `prototype` 프로퍼티 | constructor | 프로토타입의 참조 | 생성자 함수 | 생성자 함수가 자신이 생성할 객체(인스턴스)의 프로토타입을 할당하기 위해 사용 |

    ```js
    // 생성자 함수
    function Person(name) {
      this.name = name;
    }

    const me = new Person("Kim");

    // 결국 Person.prototype과 me.__proto__는 동일한 프로토타입을 가리킨다.
    console.log(Person.prototype === me.__proto__); // true
    ```

### 03-03. 프로토타입의 constructor 프로퍼티와 생성자 함수

- 모든 프로토타입은 constructor 프로퍼티를 가지며, constructor 프로퍼티는 prototype 프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킨다.
  - 이 연결은 생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 이뤄진다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Kim");

// me 객체의 생성자 함수는 Person이다.
// me 객체에는 constructor 프로퍼티가 없지만 프로토타입인 Person.prototype의 constructor 프로퍼티를 상속받아 사용할 수 있다.
console.log(me.constructor === Person); // true
```

## 04. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

- 리터럴 표기법에 의한 객체 생성 방식과 같이 명시적으로 new 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하지 않는 객체 생성 방식도 있다.
- ⭐️ 리터럴 표기법에 의해 생성된 객체도 물론 프로토타입이 존재한다.
  - 하지만 리터럴 표기법에 의해 생성된 객체의 경우 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 생성자 함수라고 단정할 수는 없다.

> 앞에서 살펴본 바와 같이 생성자 함수에 의해 생성된 인스턴스는 프로토타입의 constructor 프로퍼티에 의해 생성자 함수와 연결된다. 이때 constructor 프로퍼티가 가리키는 생성자 함수는 인스턴스를 생성한 생성자 함수다.

```js
// obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴로 생성했다.
const obj = {};

// 하지만 obj 객체의 생성자 함수는 Object 생성자 함수다.
console.log(obj.constructor === Object);
true;
```

- 위 예제의 obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴에 의해 생성된 객체다.
  - 하지만 obj 객체는 Object 생성자 함수와 constructor 프로퍼티로 연결되어 있다.
- ⭐️ 이와같은 현상을 이해하기 위해서는 `추상연산`을 이해할 필요가 있다
  - Object 생성자 함수에 인수를 전달하지 않거나 `undefined` 또는 `null`을 인수로 전달하면서 호출하면 내부적으로는 추상 연산 `OrdinaryObjectCreate`를 호출하여 `Object.prototype`을 프로토타입으로 갖는 빈 객체를 생성한다.
  - 객체 리터럴이 평가될 때는 다음과 같이 추상 연산 `OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하고 프로퍼티를 추가하도록 정의되어 있다.
  - Object 생성자 함수 호출과 객체 리터럴 평가는 추상 연산 `OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하는 점에서 동일하나 new.target의 확인이나 프로퍼티를 추가하는 처리 등 세부 내용은 다르다.
  - 따라서 객체 리터럴에 의해 생성된 객체는 Object 생성자 함수가 생성한 객체가 아니다.
- 리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요하다.
  - 따라서 리터럴 표기법에 의해 생성된 객체도 가상적인 생성자 함수를 갖는다.
  - ⭐️ 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다.
- ⭐️ 리터럴 표기법에 의해 생성된 객체는 큰 틀에서 생각해 보면 생성자 함수에 의해 생성된 객체는 아니지만 생성자 함수로 생성한 객체와 본질적인 면에서 큰 차이는 없다.
  - 객체 리터럴에 의해 생성한 객체와 object 생성자 함수에 의해 생성한 객체는 생성 과정에 미묘한 차이는 있지만 결국 객체로서 동일한 특성을 갖는다.
  - 함수 리터럴에 의해 생성한 함수와 `Function` 생성자 함수에 의해 생성한 함수는 생성 과정과 스코프, 클로저 등의 차이가 있지만 결국 함수로서 동일한 특성을 갖는다.
  - 따라서 프로토타입의 constructor 프로퍼티를 통해 연결되어 있는 생성자 함수를 리터럴 표기법으로 생성한 객체를 생성한 생성자 함수로 생각해도 크게 무리는 없다.
  - 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입은 다음과 같다.
    | 리터럴 표기법 | 생성자 함수 | 프로토타입 |
    |------------------|-------------|----------------------|
    | 객체 리터럴 | Object | Object.prototype |
    | 함수 리터럴 | Function | Function.prototype |
    | 배열 리터럴 | Array | Array.prototype |
    | 정규 표현식 리터럴 | RegExp | RegExp.prototype |

> #### 추상 연산(abstract operation)
>
> 추상 연산은 ECMAScript 사양에서 내부 도작의 구현 알고리즘을 표현한 것이다. ECMAScript 사양에서 설명을 위해 사용되는 함수와 유사한 의사 코드라고 이해하자.

## 05. 프로토타입의 생성 시점

- ⭐️ 프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.
  - 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재하기 때문이다.
- 프로토타입 생성 시점은 사용자가 직접 정의한 사용자 정의 생성자 함수와 자바스크립트가 기본 제공하는 빌트인 생성자 함수로 구분할 수 있다.

### 05-01. 사용자 정의 생성자 함수와 프로토타입 생성 시점

- 내부 메서드 `[[Constructor]]`를 갖는 함수 객체, 즉 화살표 함수나 ES6의 축약 표현으로 정의하지 않고 일반 함수(함수 선언문, 함수 표현식)로 정의한 함수 객체는 new 연산자와 함께 생성자 함수로서 호출할 수 있다.
- 생성자 함수로서 호출할 수 있는 함수, 즉 constructor는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
- 생성자 함수로서 호출할 수 없는 함수, non-constructor는 프로토타입이 생성되지 않는다.

```js
// 함수 정의 constructor가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다
console.log(Preson1.prototype); // { constructor: f }

// 생성자 함수
function Person1(name) {
  this.name = name;
}

// 화살표 함수는 non-constructor다.
const Person2 = (name) => {
  this.name = name;
};

// non-constructor는 프로토타입이 생성되지 않는다.
console.log(Person2.prototype); // undefined
```

- 함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다.
  - ⭐️ 함수 선언문으로 정의된 생성자 함수는 어떤 코드보다 먼저 평가되어 생행된다. 이때 프로토타입도 더불어 생성된다.
  - 생성된 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩된다.
- 이처럼 사용자 정의 생성자 함수는 자신이 평가되어 함수 객체로 생성되는 시점에 프로토타입도 더불어 생성되며, 생성된 프로토타입은 언제나 `Object.prototype`이다.

### 05-02. 빌트인 생성자 함수와 프로토타입 생성 시점

- `Object, String, Number, Function, Array, RegExp, Date, Promise` 등과 같은 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다.
  - ⭐️ 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성된다.
  - 생성된 프로토타입은 빌트인 생성자 함수의 prototype 프로퍼티에 바인딩된다.
  - 객체가 생성되기 이전에 생성자 함수와 프로토타입은 이미 객체화되어 존재한다
  - 이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 `[[Prototype]]`내부 슬롯에 할당된다.

> #### 전역 객체(global object)
>
> 전역 객체는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 생성되는 특수한 객체다. 전역 객체는 클라이언트 사이드 환경(브라우저)에서는 window, 서버 사이드 환경(Node.js)에서는 global 객체를 의미한다.
> 전역 객체는 표준 빌트인 객체`(Object String, Number, Function, Array...)`들과 환경에 따른 호스트 객체(클라이언트 Web API 또는 Node.js의 호스트 API), 그리고 var 키워드로 선언한 전역 변수와 전역 함수를 프로퍼티로 갖는다. Math, Reflect, JSON을 제외한 표준 빌트인 객체는 모두 생성자 함수다.

## 06. 객체 생성 방식과 프로토타입의 결정

- 객체는 다음과 같이 다양한 생성 방법이 있다.
  - 객체 리터럴
  - Object 생성자 함수
  - 생성자 함수
  - Object.create 메서드
  - 클래스(ES6)
- ⭐️ 다양한 방식으로 생성된 모든 객체는 각 방식마다 세부적인 객체 생성 방식의 차이는 있으나 추상 연산에 의해 생성된다는 공통점이 존재한다.
  - 추상 연산은 필수적으로 자신이 생성할 객체의 프로토타입을 인수로 전달 받는다.
  - 추상 연산은 빈 객체를 생성한 후, 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 프로퍼티를 객체에 추가한다.
  - 그리고 인수로 전달받은 프로토타입을 자신이 생성한 객체의 `[[Prototype]]` 내부 슬롯에 할당한 다음, 생성한 객체를 반환한다.
- 프로토타입은 추상 연산에 전달되는 인수에 의해 결정된다.
  - 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

### 06-01. 객체 리터럴에 의해 생성된 프로토타입

- 자바스크립트 엔진은 객체 리터럴을 평가하여 객체를 생성할 때 추상 연산을 호출한다.
  - 이때 추상 연산에 전달되는 프로토타입은 `Object.prototype`이다.
  - 즉, 객체 리터럴에 의해 생성되는 객체의 프로토타입은 `Object.prototype`이다.

```js
// 객체 리터럴로 생성된 객체
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

### 06-02. Object 생성자 함수에 의해 생성된 객체의 프로토타입

- Object 생성자 함수를 인수 없이 호출하면 빈 객체가 생성된다.
  - Object 생성자 함수를 호출하면 객체 리터럴과 마찬가지로 추상 연산이 호출된다.
  - 이때 추상 연산에 전달되는 프로토타입은 `Object.prototype`이다.
  - 즉, Object 생성자 함수에 의해 생성되는 객체의 프로토타입은 `Object.prototype`이다.

```js
// Object 생성자 함수로 생성된 객체
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

> 객체 리터럴과 Object 생성자 함수에 의한 객체 생성 방식의 차이는 프로퍼티를 추가하는 방식에 있다.
> 객체 리터럴 방식: 객체 리터럴 내부에 프로퍼티를 추가
> Object 생성자 함수: 일단 빈 객체를 생한 이후 프로퍼티를 추가해야 함

### 06-03. 생성자 함수에 의해 생성된 객체의 프로토타입

- new 연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하면 다른 객체 생성 방식과 마찬가지로 추상 연산이 호출된다.
  - 이때 추상 연산에 전달되는 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩 되어 있는 객체다.
  - 즉, 생성자 함수에 의해 생성되는 객체의 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체다.

## 07. 프로토타입 체인

- ⭐️ 프로토타입 체인이란 자바스크립트가 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때 해당 객체에 접근하려는 프로퍼티가 없다면 `[[Prototype]]` 내부 슬롯의 참조를 따라 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색하는 것을 말한다.
- ⭐️ 프로토타입 체인의 최상위 객체는 언제나 `Object.prototype`이다.
  - 모든 객체는 `Object.prototype`을 상속받는다.
  - `Object.prototype`을 프로토타입 체인의 종점(end of prototype chain)이라 한다.
  - `Object.prototype`의 프로토타입 `[[Prototype]]` 내부 슬롯의 값은 `null`이다
  - 프로토타입 체인의 종점인 `Object.prototype`에서도 프로퍼티를 검색할 수 없는 경우 `undefined`를 반환한다.
  - 이때 에러가 발생하지 않는것에 주의하자.

```js
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi my name is ${this.name}`);
};

const me = new Person("Kim");

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty("name")); // true

// Person.prototype의 프로토타입은 Object.prototype이다.
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true

// hasOwnProperty는 Object.prototype의 메서드다.
// me 객체는 프로토타입 체인을 따라 hasOwnProperty 메서드를 검색하여 사용한다.
me.hasOwnProperty("name"); // true
```

> ⭐️ 프로토타입 체인과 스코프 체인
>
> - 프로토타입 체인: 자바스크립트 엔진은 객체 간 상속 관계로 이루어진 프로토타입의 계층적인 구조(프로토타입 체인)을 따라 프로퍼티/메서드를 검색한다.
>   따라서 프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘 이라고 할 수 있다.
> - 스코프 체인: 자바스크립트 엔진은 함수의 중첩 관계로 이루어진 스코프의 계층적 구조에서 식별자를 검색한다.
>   따라서 스코프 체인은 식별자 검색을 위한 메커니즘이라고 할 수 있다.
>
> 스코프 체인과 프로토타입 체인근 서로 연관없이 별도로 동작하는 것이 아니라 서로 협력하여 식별자와 프로퍼티를 검색하는데 사용된다
>
> ```js
> // 먼저 스코프 체인에서 me 식별자를 검색한 다음, me 객체의 프로토타입 체인 안에서 hasOwnProperty 메서드를 검색한다.
> me.hasOwnProperty("name");
> ```
