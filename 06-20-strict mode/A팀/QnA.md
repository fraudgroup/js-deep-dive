# 1. [유경] 왜 strict mode와 non-strict mode를 혼용하는 것이 오류를 발생시키는지 설명해주세요.

## 보경

코드의 일관성을 해칠 것 같아요. 어디에서는 strict 모드 적용된 코드가 존재하고, 어디에서는 적용되지 않은 코드가 존재하게 되면 같은 프로젝트 안에서 일관성을 해칠 수 있다. 컨벤션을 해칠 수 있다.

## 지원

일관성이 부족함으로서 코드의 이해가 힘들어서 코드의 유지보수가 어려울 것 같고, 동작 또한 예상대로 흘러가지 않는다는 문제점이 발생할 것 같습니다.

## 모범 답안

두 모드를 혼용하면 오류가 발생하는 이유는 두 모드간의 규칙과 동작 방식에 차이가 있기 때문이다.

→ 일관성과 예측 가능성에 영향을 미치며, 혼용 시 예상치 못한 동작을 초래할 수 있다.

1. **변수 선언 및 할당 규칙**
   - Non-strict mode에서는 변수 선언 없이 변수를 사용할 수 있으며, 이 경우 암묵적으로 전역 변수가 생성됩니다. 하지만 strict mode에서는 변수 선언 없이 변수를 사용하면 ReferenceError가 발생합니다.
   - 이로 인해 동일한 코드가 모드에 따라 전혀 다른 동작을 할 수 있으며, 특히 코드가 다양한 모드의 환경에서 실행될 때 일관된 결과를 기대하기 어렵습니다.
2. **함수 내 this 바인딩**

- Non-strict mode에서는 `this`가 전역 객체(window)를 참조할 수 있지만, strict mode에서는 `this`가 `undefined`로 바인딩됩니다. 이는 특히 메서드 호출 시 `this`의 예상치 못한 값으로 인한 오류를 유발할 수 있습니다.

1. **예약어 및 문법 제한**

- Strict mode에서는 사용이 제한되거나 금지된 식별자(예: `implements`, `interface`, `let`, `private`, `public`, `yield` 등)가 존재합니다. 반면 non-strict mode에서는 이러한 식별자를 사용할 수 있습니다. 코드가 strict mode로 변환되거나 모듈 간에 혼용될 때 이러한 식별자 사용이 문제가 될 수 있습니다.

1. **객체 및 함수의 추가적인 제약**:
   - Strict mode에서는 읽기 전용 프로퍼티에 값을 할당하려고 하거나, `delete` 연산자로 삭제할 수 없는 프로퍼티를 삭제하려고 하면 TypeError가 발생합니다. 반면 non-strict mode에서는 이러한 시도가 무시됩니다.
   - 이러한 동작 차이는 strict mode와 non-strict mode가 혼용될 때 특히 문제가 됩니다. 예를 들어, 라이브러리와 사용자 코드가 다른 모드에서 실행될 때 의도하지 않은 예외가 발생할 수 있습니다.
2. **중복 매개변수 금지**:
   - Strict mode에서는 함수 선언에서 중복된 매개변수를 허용하지 않습니다. 반면 non-strict mode에서는 중복된 매개변수를 허용합니다. 이러한 차이는 strict mode로 전환하거나 혼용할 때 함수 정의에 대한 호환성 문제를 야기할 수 있습니다.

<br />

# 2. [보경] strict mode란 무엇인가요?

## 민영

자바스크립트 코드를 엄격하게 만들어가지구 코드 안에서 오류를 줄이고, 최적화를 돕기 위해 사용됩니다. 그래서 스트릭트 모드를 적용한다면 잘못 적혀 있는 코드나 잠재적인 오류같은 거를 에러로 나타내서 사전에 방지할 수 있다는 특징을 가지고 있기 떄문에 유지보수성이 높아집니다.

## 모범 답안

strict mode는 자바스크립트의 언어 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시키는 모드를 이야기합니다.

# 3. [민영] strict mode에서 발생하는 주요 에러는 무엇인가요?

## 유경

제가 답변할 때는 받아적지 못해서 기억이...

## 모범 답안

- 암묵적 전역: 선언하지 않은 변수를 참조하면 ReferenceError가 발생합니다.
- 변수, 함수, 매개변수의 삭제: delete 연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생합니다.
- 매개변수 이름의 중복: 중복된 매개변수 이름을 사용하면 SyntaxError가 발생합니다.
- with 문의 사용: with 문을 사용하면 SyntaxError가 발생합니다. with 문은 성능과 가독성 문제 때문에 사용하지 않는 것이 좋습니다.

<br />

# 4. [보경] strict mode에서 일반 함수로 호출하면 this에 바인딩되는 값과 왜 그렇게 할당되는지?

## 지원

일반 함수의 this는 undefined가 바인딩되는 거 맞나요? (yeah) strict mode에서는 전역 객체의 암묵적 바인딩을 막아서 undefined가 반환되는 걸로 알고 있습니다.

## 모범 답안

- strict mode에서 함수를 일반 함수로서 호출하면 this에 `undefined`가 바인딩된다.
  - 생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다.

### [유경] 준비만 한 문제

# 5. strict mode가 발생시키는 에러의 종류 중 2가지 이상을 말해주고, strict mode가 아닐 때는 어떻게 동작하는 지 설명해주세요.

## 모범 답안

1. **변수 선언 없이 변수 사용 (ReferenceError)**:

- **Strict Mode**: 변수를 선언하지 않고 사용하면 `ReferenceError`가 발생합니다.
- **Non-Strict Mode**: 변수를 선언하지 않고 사용하면 암묵적으로 전역 변수가 생성됩니다. 이 전역 변수는 전역 객체(window)의 프로퍼티로 추가됩니다.
- **예시**:
  ```jsx
  function foo() {
    "use strict";
    x = 10; // ReferenceError: x is not defined
  }
  foo();
  ```
  ```jsx
  function foo() {
    x = 10; // 암묵적으로 전역 변수가 생성됨 (non-strict mode)
  }
  foo();
  console.log(x); // 10
  ```

2.  **읽기 전용 프로퍼티에 값 할당 (TypeError)**:

- **Strict Mode**: 읽기 전용(read-only) 프로퍼티에 값을 할당하려고 하면 `TypeError`가 발생합니다.
- **Non-Strict Mode**: 아무런 에러도 발생하지 않으며, 할당이 무시됩니다.
- **예시**:

  ```jsx
  "use strict";
  var obj = {};
  Object.defineProperty(obj, "x", { value: 42, writable: false });

  obj.x = 9; // TypeError: Cannot assign to read-only property 'x' of object
  ```

  ```jsx
  var obj = {};
  Object.defineProperty(obj, "x", { value: 42, writable: false });

  obj.x = 9; // 할당은 무시되고, 에러가 발생하지 않음 (non-strict mode)
  console.log(obj.x); // 42
  ```

3. **삭제할 수 없는 속성의 삭제 (TypeError)**:

- **Strict Mode**: `configurable: false`로 설정된 속성을 `delete`하려고 하면 `TypeError`가 발생합니다.
- **Non-Strict Mode**: `delete` 연산이 무시되고, 아무런 에러도 발생하지 않습니다.
- **예시**:

  ```jsx
  "use strict";
  var obj = {};
  Object.defineProperty(obj, "x", { value: 42, configurable: false });

  delete obj.x; // TypeError: Cannot delete property 'x' of #<Object>
  ```

  ```jsx
  var obj = {};
  Object.defineProperty(obj, "x", { value: 42, configurable: false });

  delete obj.x; // 삭제 시도는 무시되고, 에러가 발생하지 않음 (non-strict mode)
  console.log(obj.x); // 42
  ```

4. **객체의 속성 이름 중복 (SyntaxError)**:

- **Strict Mode**: 객체 리터럴에서 중복된 속성 이름을 사용하면 `SyntaxError`가 발생합니다.
- **Non-Strict Mode**: 중복된 속성 이름이 있을 경우 마지막에 정의된 속성 값으로 덮어씌워집니다.
- **예시**:
  ```jsx
  "use strict";
  var obj = { a: 1, a: 2 }; // SyntaxError: Duplicate data property in object literal not allowed in strict mode
  ```
  ```jsx
  var obj = { a: 1, a: 2 }; // 두 번째 'a' 프로퍼티로 덮어씌워짐 (non-strict mode)
  console.log(obj.a); // 2
  ```

5. **함수 매개변수 이름 중복 (SyntaxError)**:

- **Strict Mode**: 함수 선언 시 매개변수 이름이 중복되면 `SyntaxError`가 발생합니다.
- **Non-Strict Mode**: 매개변수 이름이 중복되어도 에러가 발생하지 않으며, 마지막 매개변수로 덮어씌워집니다.
- **예시**:
  ```jsx
  "use strict";
  function foo(x, x) {
    // SyntaxError: Duplicate parameter name not allowed in this context
    return x;
  }
  ```
  ```jsx
  function foo(x, x) {
    // 두 번째 'x' 매개변수로 덮어씌워짐 (non-strict mode)
    return x;
  }
  console.log(foo(1, 2)); // 2
  ```
