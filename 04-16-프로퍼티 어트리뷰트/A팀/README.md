# 16장 프로퍼티 어트리뷰트

## 목차

1. [내부 슬롯과 내부 메서드](#1-내부-슬롯과-내부-메서드)
2. [프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체](#2-프로퍼티-어트리뷰트와-프로퍼티-디스크립터-객체)
3. [데이터 프로퍼티와 접근자 프로퍼티](#3-데이터-프로퍼티와-접근자-프로퍼티)
4. [프로퍼티 정의](#4-프로퍼티-정의)
5. [객체 변경 방지](#5-객체-변경-방지)

<br />

## 1. 내부 슬롯과 내부 메서드

내부 슬롯과 내부 메서드는 **자바스크립트 엔진의 구현 알고리즘을 설명하기 위해** ECMAScript 사양에서 사용하는 의사 프로퍼티와 의사 메서드다.<br>
ECMAScript 사양에 등장하는 이중 대괄호로 감싼 이름들.

내부 슬롯과 내부 메서드는 자바스크립트 엔진의 내부 로직이므로 원칙적으로 자바스크립트는 내부 슬롯과 내부 메서드에 직접적으로 접근하거나 호출할 수 있는 방법을 제공하지 않는다.<br>
단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공한다.

[[Prototype]] 내부 슬롯의 경우, .\_\_proto\_\_를 통해 간접적으로 접근할 수 있다.

> \_\_proto\_\_를 읽을 때 'dunder proto(던더 프로토)'라고 읽으면 됩니다. <br>
> dunder는 'double underscore'의 줄임말.

<br />

## 2. 프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체

자바스크립트 엔진은 **프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰트를 기본값으로 자동 정의**한다.<br>
프로퍼티 상태 : 프로퍼티의 값, 값의 갱신 가능 여부, 열거 가능 여부, 재정의 가능 여부

프로퍼티 어트리뷰트 : 자바스크립트 엔진의 관리하는 내부 상태 값인 내부슬롯 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]]

Object.getOwnPropertyDescriptor 메서드를 사용하여 간접적으로 확인할 수 있다.

Object.getOwnPropertyDescriptor(객체의 참조, 프로퍼티 키(문자열))<br>
프로퍼티 디스크립터 객체를 반환한다.<br>
존재하지 않는 프로퍼티나 상속받은 프로퍼티에 대한 프로퍼티 디스크립터를 요구하면 undefined가 반환된다.

Object.getOwnPropertyDescriptor : 하나의 프로퍼티에 대해 프로퍼티 디스크립터 객체를 반환

Object.getOwnPropertyDescriptors : 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.

```js
const person = {
  name: 'Lee'
};

console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}

person.age = 20;

console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```
<br />

## 3. 데이터 프로퍼티와 접근자 프로퍼티

- 데이터 프로퍼티
  - 키와 값으로 구성된 일반적인 프로퍼티
- 접근자 프로퍼티
  - 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수로 구성된 프로퍼티

**1. 데이터 프로퍼티**

프로퍼티가 생성될 때 [[Value]]의 값은 프로퍼티 값으로 초기화되며, [[Writable]], [[Enumerable]], [[Configurable]]의 값은 true로 초기화된다.

- **[[Value]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : value
  - 프로퍼티 키를 통해 프로퍼티 값을 변경하면 [[Value]]에 값을 재할당
  - 프로퍼티가 없다면 프로퍼티를 동적 생성하고 생성된 프로퍼티의 [[Value]]에 값 저장
- **[[Writable]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : writable
  - 프로퍼티 **값의 변경 가능 여부** (boolean)
  - false인 경우, 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없는 읽기 전용 프로퍼티
- **[[Enumerable]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : enumerable
  - 프로퍼티의 **열거 가능 여부** (boolean)
  - false인 경우, for...in문이나 Object.keys 메서드 등으로 열거 X
- **[[Configurable]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : configurable
  - 프로퍼티의 **재정의 가능 여부** (boolean)
  - false인 경우, 해당 프로퍼티의 삭제, 프로퍼티 어트리뷰트 값의 변경이 금지
  - 단, [[Writable]]이 true인 경우, [[Value]]의 변경과 [[Writable]]을 false로 변경 허용

<br>

**2. 접근자 프로퍼티**

접근자 함수는 getter/setter 함수라고도 부른다.<br>
접근자 프로퍼티는 getter/setter 함수를 모두 정의할 수도 있고 하나만 정의할 수도 있다.

- **[[Get]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : get
  - 접근자 프로퍼티를 통해 데이터 프로퍼티의 **값을 읽을 때** 호출되는 접근자 함수
- **[[Set]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : set
  - 접근자 프로퍼티를 통해 데이터 프로퍼티의 **값을 저장할 때** 호출되는 접근자 함수
- **[[Enumerable]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : enumerable
  - 데이터 프로퍼티의 [[Enumerable]]과 동일
- **[[Configurable]]** 프로퍼티 어트리뷰트
  - 프로퍼티 디스크립터 객체의 프로퍼티 : configurable
  - 데이터 프로퍼티의 [[Configurable]]과 동일

```js
const person = {
  // 데이터 프로퍼티
  firstName: 'Ungmo',
  lastName: 'Lee',

  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + ' ' + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// {value: "Heegun", writable: true, enumerable: true, configurable: true}

descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```

<br>

> 📜 **프로토타입(prototype)**
>
> 프로토타입은 어떤 객체의 상위(부모) 객체의 역할을 하는 객체
>
> 하위(자식) 객체에서 자신의 프로퍼티와 메서드를 상속한다.
>
> 프로토타입 객체의 프로퍼티나 메서드를 상속받은 하위 객체는 자신의 프로퍼티 또는 메서드인 것처럼 자유롭게 사용할 수 있다.

<br>

## 4. 프로퍼티 정의
새로운 프로퍼티를 추가하면서 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의하는 것

Object.defineProperty 메서드 : 프로퍼티의 어트리뷰트를 정의<br>
인수 : 객체의 참조, 데이터 프로퍼티의 키인 문자열, 프로퍼티 디스크립터 객체<br>
프로퍼티 디스크립터 객체의 프로퍼티를 일부 생략할 경우 false 또는 undefined로 기본값이 적용된다.

Object.defineProperties: 여러 개의 프로퍼티를 한 번에 정의

<br>

## 5. 객체 변경 방지
객체의 변경을 방지하는 다양한 메서드를 제공

|구분|메서드|프로퍼티 추가|프로퍼티 삭제|프로퍼티 값 읽기|프로퍼티 값 쓰기|프로퍼티 어트리뷰트 재정의|
|:----------:|:-----------------------:|:--------:|:--------:|:--------:|:--------:|:---------:|
|객체 확장 금지| Object.preventExtensions|X|O|O|O|O|
|객체 밀봉|Object.seal|X|X|O|O|X|
|객체 동결|Object.freeze|X|X|O|X|X|

<br>

- 객체 확장 금지
  - 프로퍼티 추가 금지
  - Object.preventExtensions 메서드 사용
  - Object.isExtensible 메서드 확인 가능

- 객체 밀봉
  - 프로퍼티 추가, 삭제, 프로퍼티 어트리뷰터 재정의 금지
  - Object.seal 메서드 사용
  - Object.isSealed 메서드 확인 가능

- 객체 동결
  - 프로퍼티 값 읽기만 가능
  - Object.freeze 메서드 사용
  - Object.isFrozen 메서드 확인

<br>

- 불변 객체
  - 지금까지 살펴본 변경 방지 메서드들은 얕은 변경 방지로 중첩 객체까지는 영향을 주지 못한다.
  - 객체의 중첩 객체까지 동결하여 변경이 불가능한 읽기 전용의 불변 객체를 구현하려면 객체를 값으로 갖는 모든 프로퍼티에 대해 재귀적으로 Object.freeze 메서드를 호출해야한다.

```js
  function deepFreeze(target) {
  // 객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
  if (target && typeof target === 'object' && !Object.isFrozen(target)) {
    Object.freeze(target);
    
    Object.keys(target).forEach(key => deepFreeze(target[key]));
  }
  return target;
}
```