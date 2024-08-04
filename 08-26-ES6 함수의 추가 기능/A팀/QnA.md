# 26장 ES6 함수의 추가 기능

## 1. 아래의 코드에서 콘솔에 출력되는 값과 그 이유를 설명해주세요.

```jsx
const obj = {
  value: 42,
  regularFunction: function () {
    console.log(this.value);
  },
  arrowFunction: () => {
    console.log(this.value);
  },
};

obj.regularFunction(); // (1)
obj.arrowFunction(); // (2)

const newObj = {
  value: 100,
};

newObj.regularFunction = obj.regularFunction;
newObj.arrowFunction = obj.arrowFunction;

newObj.regularFunction(); // (3)
newObj.arrowFunction(); // (4)
```

### 답변

- (1) **42**: regularFunction은 객체 obj의 메서드로 호출되어서 this는 obj를 가리키고, obj.value는 42이다.
- (2) **undefined**: arrowFunction은 화살표 함수 → this는 상위 스코프 (전역 객체)를 참조한다. 여기에는 value 프로퍼티가 없어서 undefined가 출력된다.
- (3) **100**: newObj 에서 regularFunction을 호출할 때, this는 newObj를 가리키기 때문에 newObj.value는 100이다.
- (4) **undefined**: arrowFunction은 화살표 함수라서 여전히 상위 스코프(전역 객체)를 참조하여 value는 여전히 undefined이다.

<br />

## 2. 화살표 함수와 일반 함수의 차이 3가지

### 답변

- 화살표 함수는 인스턴스를 생성할 수 없는 non-constructor다.
- 화살표 함수에서 중복된 매개변수 이름을 선언할 수 없다.
- 화살표 함수는 함수 자체의 this, arguments, super, new.target 바인딩을 갖지 않는다.

<br />

## 3. ES6의 일반 함수, 메서드, 화살표 함수가 지니는 특징을 보고 ❓를 ✅, ❌로 채워주세요.

- 일반함수: constructor [❓], prototype[❓], super [❓], arguments[❓]
- 메서드: constructor [❓], prototype[❓], super [❓], arguments[❓]
- 화살표 함수: constructor [❓], prototype[❓], super [❓], arguments[❓]

### 답변

- 일반 함수: constructor [✅], prototype[✅], super[❌], arguments[✅]
- 메서드: constructor [❌], prototype[❌], super[✅], arguments[✅]
- 화살표 함수: constructor [❌], prototype[❌], super[❌], arguments[❌]

<br />

## 4. 다음 코드를 보고 화살표 함수의 this 바인딩과 sayHi의 출력 결과에 대해서 설명해 주세요.

```jsx
class Person {
  name = "Lee";
  sayHi = () => console.log(`Hi ${this.name}`);
}

const person = new Person();
person.sayHi(); // Hi Lee
```

### 답변

화살표 함수는 자체적으로 this 를 지니고 있지 않고 상위 스코프의 this 를 참조한다. new Person(); 이 실행되면 인스턴스가 생성되며 내부적으로 constructor가 실행된다. 이때 클래스 필드로 정의된 메서드도 인스턴스의 프로퍼티로 바인딩된다. sayHi에 할당된 화살표 함수의 정의가 평가될 때의 상위 스코프는 constructor가 실행될 때의 this를 가리킨다. 이때의 this는 생성된 인스턴스를 가리킨다.

<br />

## 5. 화살표 함수가 this값을 참조하는 과정을 실행 컨텍스트와 렉시컬 환경을 언급하여 설명해주세요.

### 답변

**1. 화살표 함수 정의 시점**

- 화살표 함수가 정의될 때, 함수는 자신이 정의된 위치의 렉시컬 환경을 기억합니다.
- 이 렉시컬 환경은 함수의 상위 스코프를 포함하며, 상위 스코프의 this 바인딩도 포함됩니다.
- [[Environment]]의 값이 외부 렉시컬 환경에 대한 참조로 바인딩됨

**2. 렉시컬 환경의 구조**

- 각 렉시컬 환경은 두 가지 주요 컴포넌트로 구성됩니다
- 1️⃣ 환경 레코드 (Environment Record): 현재 스코프의 변수, 함수 선언 등을 저장합니다.
- 2️⃣ 외부 렉시컬 환경 참조 (Outer Lexical Environment Reference): 상위 렉시컬 환경에 대한 참조를 가집니다.

**3. 화살표 함수 호출 시점**

- 화살표 함수가 호출될 때, 새로운 this 바인딩을 생성하지 않습니다.
- 대신, 정의 시점에 기억한 상위 렉시컬 환경의 this 바인딩을 그대로 사용합니다.
- 이 과정은 상위 렉시컬 환경 참조를 통해 상위 스코프의 this 값을 참조하는 방식으로 이루어집니다.
