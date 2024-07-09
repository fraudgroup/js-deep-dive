# 07장 연산자

- 연산자(operator)는 하나 이상의 표현식을 대상으로 산술, 할당, 비교, 논리, 타입, 지수 연산(operation)등을 수행해 하나의 값을 만든다.

```js
// 산술 연산자
5 * 4; // 20

// 문자열 연결 연산자
"My name is" + "Kim"; // 'My name is Kim'

// 할당 연산자
color = "red"; // 'red'

// 비교 연산자
3 > 5; // false

// 논리 연산자
true && false; // false

// 타입 연산자
typeof "Hi"; // string
```

### 1. 산술 연산자

> 산술 연산자는 피연산자를 대상으로 수학적 계산을 수행해 새로운 숫자 값을 만든다.
> 산술 연산이 불가능한 경우, NaN을 반환한다.

#### 1-1. 문자열 연결 연산자

- `+` 연산자는 피연산자 중 하나 이상이 문자열인 경우 문자열 연결 연산자로 동작한다.
- 개발자의 의도와는 상관없이 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환되기도 한다.
- 이를 `암묵적 타입 변환(implicit coercion)` 또는 `타입 강제 변환(type coercion)` 이라고 한다.

```js
// 문자열 연결 연산자
"1" + 2; // '12'
1 + "2"; // '12'

// 산술 연산자
1 + 2; // 3

//true는 1로 타입변환
1 + true; // 2

// false는 0으로 타입 변환
1 + false; // 1

// null은 0으로 타입 변환
1 + null; // 1

// undefined는 숫자로 타입 변환되지 않는다
+undefined; // NaN
1 + undefined; // NaN
```

### 2. 비교 연산자

- 동등 비교(loose equality)연산자와 일치 비교(strict equality)연산자는 좌항과 우항의 피연산자가 같은 값으로 평가되는지 비교하여 불리언 값을 반환한다.
- 하지만 이 둘의 엄격성의 정도가 다르다.
- 동등 비교(==) 연산자는 좌항과 우항의 피연산자를 비교할 때 먼저 암묵적 타입 변환을 통해 타입을 일치시킨 후 같은 값인지 비교한다.
- 동등 비교(==) 연산자는 예측하기 어려운 결과를 만들어 내므로 사용하지 않는 편이 좋다.

```js
// 동등 비교
5 == 5; // true

// 타입은 다르지만 암묵적 타입 변환을 통해 타입을 일치시키면 동등하다.
5 == "5"; // true
```

- 일치 비교(===) 연산자는 좌항과 우항의 피연산자가 타입도 같고 값도 같은 경우에 한하여 true를 반환한다.

```js
// 일치 비교
5 === 5; // true

// 암묵적 타입 변환을 하지 않고 값을 비교한다.
// 즉, 값과 타입이 모두 같은 경우만 true를 반환한다.
5 === "5";
false;
```

### 3. 삼항 조건 연산자

> 삼항 조건 연산자 표현식은 값으로 평가할 수 있는 표현식인 문이다.

### 4. typeof 연산자

- typeof 연산자는 피연산자의 데이터 타입을 문자열로 반환한다.
- 7가지 문자열 `sting, number, boolean, undefined, symbol, object, function` 중 하나를 반환한다.
- `null`을 안환하는 경우는 없으며, 함수의 경우 `function`을 반환한다.

```js
typeof ""; // "string"
typeof 1; // "number"
typeof NaN; // "number"
typeof true; // "boolean"
typeof undefined; // "undefined"
typeof Symbol(); // "symbol"
typeof null; // "object"
typeof []; // "object"
typeof {}; // "object"
typeof new Date(); // "object"
typeof /test/gi; // "object"
typeof function () {}; // "function"
```

> typeof 연산자로 `null`값을 연산해 보면 `null`이 아닌 `object`를 반환한다는 데 주의하자
> 자바스크립트의 첫 번째 버젼의 버그이며 기존 코드에 영향을 줄 수 있기 때문에 수정되지 못하고 있다.

> 따라서 값이 null 타입인지 확인할 때는 typeof 연산자가 아닌 일치 연산자(===)를 사용한다.

> 또한, 선언하지 않은 식별자를 typeof 연산자로 연산해 보면 ReferenceError가 발생하지 않고 undefined를 반환한다.

### 5. 지수 연산자

- ES7에서 도입되었다.
- 지수 연산자가 도입되기 이전에는 `Math.pow` 메서드를 사용했다.

```js
2 ** 2 === Math.pow(2, 2);

// 음수를 거듭제곱의 밑으로 사용해 계산사려면 괄호로 묶어야 한다.
-5 ** 2; //SyntaxError
(-5) ** 2; // 25
```

### 6. 연산자의 부수 효과

- 대부분의 연산자는 다른 코드에 영향을 주지 않는다.
  > 부수 효과가 있는 연산자는 할당 연산자(=), 증가/감소 연산자(++/--), delete 연산자가 있다
