# 19장 프로토타입 (02)

## 08. 오버라이딩과 프로퍼티 섀도잉

- 프로토타입이 소유한 프로퍼티(메서드 포함)를 프로토타입 프로퍼티, 인스턴스가 소유한 프로퍼티를 인스턴스 프로퍼티라고 부른다.
- 프로토타입 프로퍼티와 같은 이름의 프로퍼티를 인스턴스에 추가하면 프로토타입 체인을 따라 프로토타입 프로퍼티를 검색하여 덮어쓰는 것이 아니라 인스턴스 프로퍼티로 추가한다.
  - 이때 인스턴스 메서드는 프로토타입 메서드를 오버라이딩 했고 프로토타입 메서드는 가려진다.
  - 이처럼 상속 관계에 의해 프로퍼티가 가려지는 현상을 프로퍼티 섀도잉이라 한다.

> #### 오버라이딩(overriding)
>
> 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

> #### 오버로딩(overloading)
>
> 함수의 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현하고 매개변수에 의해 메서드를 구별하여 호출하는 방식이다. 자바스크립트는 오버로딩을 지원하지 않지만 arguments 객체를 사용하여 구현할 수는 있다.

## 09. 프로토타입의 교체

- 프로토타입은 임의의 다른 객체로 변경할 수 있다.
  - 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 것을 의미한다.
  - 이러한 특징을 활용하여 객체 간의 상속 관계를 동적으로 변경할 수 있다.
  - 프로토타입은 생성자 함수 또는 인스턴스에 의해 교체할 수 있다.

### 09-01. 생성자 함수에 의한 프로토타입 교체

```js
const Person = function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
}; //  }()); 로 끝나야하는데 수정이 안됨

const me = new Person("Kim");

// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

- 위 예제에서는 Person.prototype에 객체 리터럴을 할당하여 Person 생성자 함수가 생성할 객체의 프로토타입을 객체 리터럴로 교체 하였다.
- 프로토타입으로 교체한 객체 리터럴에는 constructor 프로퍼티가 없다. (constructor 프로퍼티는 자바스크립트 엔진이 프로토타입을 생성할 때 암묵적으로 추가한 프로퍼티다.)
- me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다.

### 09-02. 인스턴스에 의한 프로토타입의 교체

- 프로토타입은 생성자 함수의 prototype 프로퍼티뿐만 아니라 인스턴스의 `__proto__`접근자 프로퍼티(또는 `Object.getPrototypeOf` 메서드)를 통해 접근할 수 있고 교체할 수도 있다.
- 생성자 함수의 prototype 프로퍼티에 다른 임의의 객체를 바인딩 하는 것은 미래에 생성할 인스턴스의 프로토타입을 교체하는 것이다.
- `__proto__` 접근자 프로퍼티를 통해 프로토타입을 교체하는 것은 이미 생성된 프로토타입을 교체하는 것이다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Kim");

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  },
};

// me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Kim

// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

- 위 예제 또한 프로토타입으로 교체한 객체에는 constructor 프로퍼티가 없으므로 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
- 따라서 프로토타입의 constructor 프로퍼티로 me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다.

## 10. instanceof 연산자

- instanceof 연산자는 이항 연산자로서 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다.
  - 만약 우변의 피연산자가 함수가 아닌 경우 TypeError가 발생한다.
  ```js
  객체 instanceof 생성자함수;
  ```
- 우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 체인 상에 존재하면 true로 평가되고, 그렇지 않은 경우에는 false로 평가된다.
- instanceof 연산자는 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아니라 생성자 함수의 prototype에 바인딩 된 객체가 프로토타입 체인 상에 존재하는지 확인한다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Kim");

// 프로토타입으로 교체할 객체
const parent = {};

Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩
Person.prototype = parent;

// Person.prototype 이 me 객체의 프로토타입 체인 상에 존재함
console.log(me instanceof Person); // true
// Object.prototype 이 me 객체의 프로토타입 체인 상에 존재함
console.log(me instanceof Object); // true
```

## 11. 직접 상속

### 11-01. Object.create에 의한 직접 상속

- `Object.create` 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다.

  - `Object.create` 메서드도 다른 객체 생성 방식과 마찬가지로 추상 연산을 호출한다.

- `Object.create` 메서드는 첫 번째 매개변수에 전달한 객체의 프로토타입 체인에 속하는 객체를 생성한다.
  - 객체를 생성하면서 직접적으로 상속을 구현
- `Object.create` 메서드의 장점은 다음과 같다
  - new 연산자가 없이도 객체를 생성할 수 있다.
  - 프로토타입을 지정하면서 객체를 생성할 수 있다.
  - 객체 리터럴에 의해 생성된 객체도 상속받을 수 있다.
- ESLint에서는 `Object.prototype`의 빌트인 메서드를 객체가 직접 호출하는 것을 권장하지 않는다.
  - 그 이유는 `Object.create` 메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다.
  - 프로토타입 체인의 종점에 위치하는 객체는 `Object.prototype`의 빌드인 메서드를 사용할 수 없다.
- 따라서 이 같은 에러를 발생시킬 위험을 없애기 위해 `Object.prototype`의 빌트인 메서드는 다음과 같이 간접적으로 호출하는 것이 좋다.

```js
// 프로토타입이 null인 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

// console.log(obj.hasOwnProperty("a"));
// TypeError: obj.hasOwnProperty is not a function

// Object.prototype의 빌트인 메서드는 객체로 직접 호출하지 않는다.
console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
```

### 11-02. 객체 리터럴 내부에서 `__proto__`에 의한 직접 상속

- `Object.create` 메서드에 의한 직접 상속은 두번째 프로퍼티를 정의하는것이 번거롭다.
- 일단 객체를 생성한 이후 프로퍼티를 추가하는 방법도 있으나 깔끔한 방법은 아니다.
- ES6에서는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용하여 직접 상속을 구현할 수 있다.

## 12. 정적(static) 프로퍼티 / 메서드

- 정적 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드를 말한다.
- 생성자 함수 객체가 소유한 프로퍼티/메서드를 정적 프로퍼티/메서드라고 한다.
- 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.

```js
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// 정적 프로퍼티
Person.staticProp = "static prop";

// 정적 메서드
Person.staticMethod = function () {
  console.log("staticMethod");
};

const me = new Person("Kim");

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // staticMethod

// 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

> 참고로 프로토타입 프로퍼티/메서드를 표기할 때 prototype을 `#`으로 표기(Object.prototype.isPrototypeOf === Object#isPrototypeOf) 하는 경우도 있으니 알아두자

## 13. 프로퍼티 존재 확인

### 13-01. in 연산자

- in 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.
- in 연산자는 확인 대상 객체의 프로퍼티 뿐만 아니라 확인 대상 객체가 상속받은 모든 프로토타입의 프로퍼티를 확인하므로 주의가 필요하다.

```js
/**
 * key: 프로퍼티 키를 나타내는 문자열
 * object: 객체로 평가되는 표현식
 */
key in object;

// person 객체에 name 프로퍼티가 존재할 경우 true
console.log("name" in person);

// 객체가 속한 프로토타입 체인 상에 존재하는 모든 프로토타입에서 확인이 이루어지는 경우
// toString은 Object.prototype의 메서드다.
console.log("toString" in person); // true
```

> in 연산자 대신 ES6에서 도입된 `Reflect.has` 메서드를 사용할 수도 있다. 동일하게 동작함
>
> ```js
> console.log(Reflect.has(person, "name"));
> ```

### 13-02. `Object.prototype.hasOwnProperty`메서드

- `Object.prototype.hasOwnProperty`메서드를 사용해도 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다.
- 메서드 이름에서 알 수 있듯이 인수로 전달받은 프로퍼티 키가 객체 고유의 프로퍼티 키인 경우에만 true를 반환하고 상속받은 프로토타입의 프로퍼티 키인 경우 false를 반환한다.

```js
// person 객체에 name 프로퍼티가 존재할 경우 true
console.log("name" in person);
// 상속받은 프로토타입의 프로퍼티 키인 경우
console.log(person.hasOwnProperty("toString")); // false
```

## 14. 프로퍼티 열거(enumeration)

### 14-01. `for...in 문`

- 객체의 모든 프로퍼티를 순회하며 열거 하려면 `for...in 문`을 사용한다.
- 객체의 프로퍼티 개수만큼 순회하며 `for...in 문`의 변수 선언문에서 선언한 변수에 프로퍼티 키를 할당한다.
  - 예를 들어, 객체에 2개의 프로퍼티가 있을 경우 객체를 2번 순화하면서 프로퍼티 키를 변수에 할당한 후 코드블록을 실행한다.
- 좀 더 정확히 표현하면 `for...in 문`은 객체의 프로토타입 체인 상에 존재하는 모든 프로토타입의 프로퍼티 중에서 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 true인 프로퍼티를 순회하며 열거한다.
- `for...in 문`은 프로퍼티를 열거할 때 순서를 보장하지 않으므로 주의가 필요하다.
  - 배열에는 해당 메서드보다는 for문이나 for...of문 또는 forEach메서드를 사용하기를 권장한다.

### 14-02. `Object.keys/values/entries` 메서드

- 객체 자신의 고유 프로퍼티만 열거하기 위해서는 `for...in 문`을 사용하는 것보다 `Object.keys/values/entries`메서드를 사용하는 것을 권장한다.

  - `Object.keys`메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.

    ```js
    const person = {
      name: "Kim",
      address: "Inchon",
      __proto__: { age: 30 },
    };

    console.log(Object.keys(person)); // ['name', 'address']
    ```

  - ES8에서 도입된 `Object.values`메서드는 객체 자신의 열거 가능한 프로퍼티 값을 배열로 반환한다.

    ```js
    console.log(Object.values(person)); // ['Kim', 'Inchon']
    ```

  - ES8에서 도입된 `Object.entries`메서드는 객체 자신의 열거 가능한 프로퍼티 키와 값의 쌍의 배열을 배열에 담아 반환한다.

    ```js
    console.log(Object.entries(person)); // [['name', 'Kim'], ['address', 'Inchon']]

    Object.entries(person).forEach(([key, value]) => console.log(key, value));
    /*
    name Kim
    address Inchon
    */
    ```
