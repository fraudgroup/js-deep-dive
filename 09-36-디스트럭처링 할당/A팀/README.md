# 36장 디스트럭처링 할당(destructuring assignment)

- 디스트럭처링 할당(구조 분해 할당)은 구조화된 배열과 같은 이터러블 또는 객체를 destructuring(비구조화, 구조 파괴)하여 1개 이상의 변수에 개별적으로 할당하는 것을 말한다.

## 01. 배열 디스트럭처링 할당

- ES5에서 구조화된 배열을 디스트럭처링하여 1개 이상의 변수에 할당하는 방법은 다음과 같다.

  ```js
  // ES5
  var arr = [1, 2, 3];

  var one = arr[0];
  var two = arr[1];
  var three = arr[2];

  console.log(one, two, three); // 1 2 3
  ```

- ES6의 배열 디스크럭처렁 할당은 배열의 각 요소를 배열로부터 추출하여 1개 이상의 변수에 할당한다.
- 이때 배열의 디스트럭처링 할당의 대상(할당문의 우변)은 이터러블이어야 하며, 할당 기준은 배열의 인덱스다.

```js
const arr= [1,2,3]

// ES6 배열 디스트럭처링 할당
// 변수 one, two, three를 선언하고 배열 arr을 디스트럭처링 하여 할당한다.
// 이때 할당 기준은 배열의 인덱스다.
const [one, twom three] = arr;

console.log(one, two, three) // 1 2 3
```

## 02. 객체 디스트럭처링 할당

- ES5에서 객체의 각 프로퍼티를 객체로부터 디스트럭처링하여 번수에 할당하기 위해서는 프로퍼티 키를 사용해야 한다.
- ES6의 객체 디스트럭처링 할당은 객체의 각 프로퍼티를 객체로부터 추출하여 1개 이상의 변수에 할당한다.
  - 이때 객체 디스트럭처링 할당의 대상(할당문의 우변)은 객체이어야 하며, 할당 기준은 프로퍼티 키다.
  - 즉, 순서는 의미가 없으며 선언된 변수 이름과 프로퍼티 키가 일치하면 할당된다.

```js
// ES5
var user = { firstName: "bokyeong", lastName: "Kim" };

var firstName = user.firstName;
var lastName = user.lastName;

console.log(firstName, lastName); // bokyeong Kim

// ES6 객체 디스트럭처링 할당
// 변수 lastName, firstName을 선언하고 user객체를 디스트럭처링하여 할당한다.
// 이때 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다. 순서는 의미가 없다.
const { lastName, firstName } = user;

console.log(firstName, lastName); // bokyeong Kim

// 프로퍼티 키를 기준으로 디스트럭처링 할당이 이루어진다.
// 프로퍼티 키가 lastName인 프로퍼티 값을 ln에 할당하고,
// 프로퍼티 키가 firstName인 프로퍼티 값을 fn에 할당한다.
const { lastNeme: ln, firstName: fn } = user;

cconsole.log(fn, ln); // bokyeong Kim
```
