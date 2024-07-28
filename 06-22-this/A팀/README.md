# this

# this 키워드

- 객체는 상태를 나타내는 프로퍼티와 동작을 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조다.

- 메서드는 자신이 속한 객체의 상태(프로퍼티)를 참조하고 변경할 수 있어야 한다. 이때 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 한다.

- 객체 리터럴로 생성해도 재귀적으로 호출할 수 있다. (ex. circle.getDiameter)

=> 자기 자신이 속한 객체를 재귀적으로 참조하는 방식은 일반적이지 않으며 바람직하지도 않다.

## 생성자 함수로 인스턴스를 생성할 때

- 생성자 함수로 인ㅇ스턴스를 생성하려면 먼저 생성자 함수가 존재해야 한다.
- 생성자 함수를 정의하는 시점에는 아직 인스턴스를 생성하기 전이므로, 생성할 인스턴스를 가리키는 식별자를 알 수 없다.

=> this를 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수로서 사용할 수 있다.

## this의 특징

- 자바스크립트 엔진에 의해 암묵적으로 생성된다.
- 코드 어디서든 참조할 수 있다.
- 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달된다.
- this를 지역 변수처럼 사용할 수 있다.

=> **this 바인딩은 함수 호출 방식에 의해 동적으로 결정된다.**

## 호출 위치 및 방식에 따른 this 바인딩

1. 전역에서의 this

- window를 가리킨다.

2. `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출

- 인수에 의해 결정된다.

# 함수 호출 방식과 this 바인딩

> **렉시컬 스코프와 this 바인딩은 결정 시기가 다르다** <br/>
> : 함수의 상위 스코프를 결정하는 방식인 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정한다. this 바인딩은 함수 호출 시점에 결정된다.

## 1. 일반 함수 호출

: 일반 함수로 호출된 모든 함수(중첩, 콜백 함수 포함) 내부의 this에는 전역 객체(window)가 바인딩된다.

\*\* strict mode가 적용된 일반 함수 내부의 this에는 undefined가 바인딩 됨 (필요가 없어서)

### 메서드 내부의 중첩 함수나 콜백 함수의 this바인딩을 메서드의 this바인딩과 일치시키기 위한 방법

```
  var value = 1;

  const obj ={
    value : 100,
    foo(){
      // this 바인딩(obj)을 변수 that에 할당한다.
      const that = this;

      // 콜백 함수 내부에서 this 대신 that을 참조한다.
      setTimeout(function(){
        console.log(that.value); // 100
      }, 100);
    }
  };

  obj.foo();
```

- `Function.prototype.apply`, `Function.prototype.call`, `Function.prototype.bind` 메서드를 이용하면 this를 명시적으로 바인딩할 수 있다.
- 화살표 함수를 사용해서 일치시킬 수 있다.

```
var value = 1;

const obj = {
  value : 100,
  foo(){
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(()=> console.log(this.value), 100); // 100
  }
}

obj.foo();
```

<br />

## 2. 메서드 호출

: 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표 연산자 앞에 기술한 객체가 바인딩된다.

- 주의할 점 : 메서드 내부의 this는 메서드를 소유한 객체가 아닌 메서드를 호출한 객체에 바인딩 된다.

### 메서드의 특징에 따른 this 바인딩

- 특정 객체에 포함된 것이 아닌, 독립적으로 존재하는 별도의 객체다.
- 다른 객체의 프로퍼티에 할당하는 것으로 다른 객체의 메서드가 될 수 있고, 일반 변수에 할당하여 일반 함수로 호출될 수도 있다.
- 따라서 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고, 메서드를 호출한 객체에 바인딩된다.

<br />

## 3. 생성자 함수 호출

: 생성자 함수 내부의 this에는 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩된다.

- new 연산자와 함께 호출하지 않으면 일반 함수로 동작하기 때문에 일반 함수의 this 바인딩이 일어난다.

## 4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출

- `apply`, `call`, `bind`는 Function.prototype의 메서드다. 이들 메서드는 모든 함수가 상속받아 사용할 수 있다.

### `apply`, `call`메서드

- 본질적인 기능은 함수를 호출하는 것이다.
- 인수를 전달하는 방식만 다를 뿐 동일하게 동작한다.

```
  /**
  * 주어진 this 바인딩과 인수 리스트 배열을 사용하여 함수를 호출한다.
  * @param thisArg - this로 사용할 객체
  * @param argsArray - 함수에게 전달할 인수 리스트의 배열 또는 유사 배열 객체
  * @returns 호출된 함수의 반환값
  */
  Function.prototype.apply(thisArg[, argsArray])

  /**
  * 주어진 this 바인딩과 ,로 구분된 인수 리스트를 사용하여 함수를 호출한다.
  * @param thisArg - this로 사용할 객체
  * @param arg1, arg2, ... - 함수에게 전달할 인수 리스트
  * @returns 호출된 함수의 반환값
  */
  Function.prototype.apply(thisArg[, arg1[, arg2[, ...]]])
```

- 주로 arguments와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우 사용한다.

```
  function convertArgsToArray(){

    // arguments 객체를 배열로 변환
    // Array.prototype.slice를 인수 없이 호출하면 배열의 복사본을 생성한다.
    const arr = Array.prototype.slice.call(arguments);
    // const arr = Array.prototype.slice.apply(arguments);
    console.log(arr);

    return arr;
  }

  convertArgsToArray(1,2,3); // [1,2,3]
```

### `bind` 메서드

- 함수를 호출하지 않고 this로 사용할 객체만 전달한다.

```
  function getThisBinding(){
    return this;
  }

  // this로 사용할 객체
  const thisArg = { a : 1 };

  // bind 메서드는 함수에 this로 사용할 객체를 전달한다.
  // bind 메서드는 함수를 호출하지는 않는다.
  console.log(getThisBinding.bind(thisArg)); // getThisBinding
  // bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
  console.log(getThisBinding.bind(thisArg)()); // { a : 1 }
```

- 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용된다.

```
  const person = {
    name : 'Lee',
    foo(callback){
      // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
      setTimeout(callback.bind(this), 100);
    }
  };

  person.foo(function(){
    console.log(`Hi! my name is ${this.name}.`); // Hi my name is Lee.
  });
```
