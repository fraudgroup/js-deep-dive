# Strict Mode

- 암묵적 전역 : ReferenceError를 발생시킬 것으로 예상되는 변수 참조를 암묵적으로 전역 객체의 프로퍼티로 동적 생성하여 참조하는 것

```
  function foo(){
    x = 10;
  }

  foo();

  console.log(x); // ReferenceError를 발생시킬 것으로 예상되오나, 암묵적 전역 객체의 프로퍼티로 동적 생성하여 오류가 발생하지 않음
```

- 이 외에도 개발자의 실수로 인한 에러는 발생하기 마련
  <br />
  -> 이를 방지하기 위한 근본적인 해결책으로 strict mode가 ES5부터 추가되엇다.

## Strict Mode

: 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시키는 것
<br />
=> 에러 발생의 여지가 있으면 에러를 뿜어내는 것

> **ESLint와 같은 린트 도구** > <br />
> 정적 분석 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류 뿐만이 아닌 잠재적 오류까지 찾아내고 오류의 원인을 리포팅해주는 도구
> <br />
>
> - strict mode가 제한하는 오류는 물론 코딩 컨벤션을 설정 파일 형태로 정의하고 강제할 수 있어 더욱 강력하다.
> - strict mode < Lint 도구 (추천)

## Strict Mode의 적용

- 전역의 선두 또는 함수 몸체의 선두에 'use strict'; 추가
  - 전역에 선언하면 전역에 모두 적용되고, 함수 몸체의 선두에 선언하면 함수 몸체 내부와 중첩 함수에 적용 된다.
    <br />

\*\* 코드의 선두가 아니면 정상적으로 적용되지 않음

## 전역에 strict mode를 적용하는 것은 피하자

: 전역에 적용한 strict mode는 스크립트 단위로 적용된다.

- strict mode 스크립트와 non-strict mode 스크립트를 혼용하는 것은 오류를 발생시킬 수 있다.
- 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문
- 굳이굳이 전역의 선두에 선언하고 싶다면 **즉시 실행함수로 감싸서** 함수 몸체 내부에서 선언할 수 있도록 하자

## strict mode가 발생시키는 에러

### 1. 암묵적 전역

```
function foo(){
  'use strict';
  let = 20; // SyntaxError
}
```

### 2. 변수, 함수, 매개변수의 삭제

```
  (function () {
    'use strict';

    var x = 1;
    delete x ; // SyntaxError

    function foo(a){
      delete a; // SyntaxError
    }
    delete foo; // SyntaxError
  }())
```

### 3. 매개변수 이름의 중복

```
  (function (){
    'use strict';

    // SyntaxError
    function foo(x, x){
      return x + x;
    }
    console.log(foo(1,2));
  }())
```

### 4. with 문의 사용

: with문은 전달된 객체를 스코프체인에 추가한다.

- 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만, 성능과 가독성이 나빠진다.

```
  (function(){
    'use strict';

    // SyntaxError
    with({x : 1}){
      console.log(x);
    }
  }());
```

## strict mode 적용에 의한 변화

### 일반 함수의 this

: strict mode에서 함수를 일반 함수로서 호출하면 this에 undefined가 바인딩된다.

- why? : 생성자 함수로서 호출된 것이 아니면 this가 필요가 없기 때문

### arguments 객체

: strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments객체에 반영되지 않는다.

```
  (function(a){
    'use strict';
    // 매개변수에 전달된 인수를 재할당하여 변경
    a = 2;

    // 변경된 인수가 arguments 객체에 반영되지 않는다.
    console.log(arguments); // {0 : 1, length : 1}
  }(1));
```
