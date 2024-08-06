# 35장 스프레드 문법

- 스프레드 문법은 하나로 뭉쳐 있는 여러 값을의 집합을 펼쳐서 개별적인 값들의 목록을 만든다.
- 스프레드 문법을 사용할 수 있는 대상은 `Array, Sting, Map, Set, DOM 컬렉션, arguments`와 같이 `for...of`문으로 순회할 수 있는 이터러블에 한정된다.
- 스프레드 문법의 결과물은 값으로 사용할 수 없고, 쉼표로 구분한 값의 목록을 사용하는 문맥에서만 사용할 수 있다.
  - 함수 호출문의 인수 목록
  - 배열 리터럴의 요소 목록
  - 객체 리터럴의 프로퍼티 목록

```js
// ...[1, 2, 3]은 [1, 2, 3]을 개별 요소로 분리한다. (1, 2, 3)
console.log(...[1, 2, 3]); // 1 2 3

// 문자열은 이터러블이다.
console.log(..."Hello"); // H e l l o

// Map과 Set은 이터러블이다.
console.log(
  ...new Map([
    ["a", "1"],
    ["b", "2"],
  ])
); // ['a', '1'] ['b', '2']
console.log(...new Set([1, 2, 3])); // 1 2 3

// 이터러블이 아닌 일반 객체는 스프레드 문법의 대상이 될 수 없다.
console.log(...{ a: 1, b: 2 }); // TypeError
```

## 01. 함수 호출문의 인수 목록에서 사용하는 경우

- 스프레드 문법이 제공되기 이전에는 배열을 펼쳐서 요소들의 목록을 함수의 인수로 전달하고 싶은 경우 `Function.prototype.apply`를 사용했다.
- 스프레드 문법을 사용하면 더 간결하고 가독성이 좋다.

```js
var arr = [1, 2, 3];

// apply 함수의 2번째 인수(배열)는 apply 함수가 호출하는 함수의 인수 목록이다.
// 따라서 배열이 펼쳐져서 인수로 절단되는 효과가 있다.
var max = Math.max.apply(null, arr); // 3

// 스프레드 문법을 사용하여 배열 arr을 1, 2, 3으로 펼쳐서 Math.max에 전달한다.
const max = Math.max(...arr); // 3
```

> ##### 스프레드 문법은 Rest파라미터와 형태가 동리하여 혼동할 수 있으므로 주의가 필요하다.
>
> - Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받기 위해 매개변수 이름 앞에 ...을 붙이는 것이다.
> - 스프레드 문법은 여러 개의 값이 하나로 뭉쳐 있는 배열과 같은 이터러블을 펼쳐서 개별적인 값들의 목록을 만드는 것이다.
>   따라서 Rest 파라미터와 스프레드 문법은 서로 반대의 개념이다.

## 02. 배열 리터럴 내부에서 사용하는 경우

### 02-01. concat

- ES5에서 2개의 배열을 1개의 배열로 결합하고 싶은 경우 배열 리터럴만으로 해결할 수 없고 concat 메서드를 사용해야 한다.
- 스프레드 문법을 사용하면 별도의 메서드를 사용하지 않고 배열 리터럴만으로 2개의 배열을 1개의 배열로 결합할 수 있다.

```js
// ES5
var arr = [1, 2].concat([3, 4]);
console.log(arr); // [1, 2, 3, 4]

// ES6
const arr = [...[1, 2], ...[3, 4]];
console.log(arr); // [1, 2, 3, 4]
```

### 02-02. splice

- ES5에서 어떤 배열의 중간에 다른 배열의 요소들을 추가하거나 제거하려면 splice 메서드를 사용한다.
  - 이때 splice 메서드의 세번째 인수로 배열을 전달하면 배열 자체가 추가된다.
- 스프레드 문법을 사용하면 더욱 간결하고 가독성 좋게 표현할 수 있다.

```js
// ES5
var arr1 = [1, 4];
var arr2 = [2, 3];

/*
apply 메서드의 2번째 인수(배열)는 apply 메서드가 호출한 splice 메서드의 인수 목록이다.
apply 메서드의 2번째 인수 [1,0].concat(arr2)는 [1, 0, 2, 3]으로 평가된다.
따라서 splice 메서드에 apply 메서드의 2번째 인수 [1, 0, 2, 3]이 해체되어 전달된다.
즉, arr1[1]부터 0개의 요소를 제거하고 그 자리(arr[1])에 새로운 요소(2, 3)를 삽입한다.
*/
Array.prototype.splice.apply(arr1, [1, 0].concat(arr2));
console.log(arr1); // [1, 2, 3, 4]

// ES6
arr1.splice(1, 0, ...arr2);
console.log(arr1); // [1, 2, 3, 4]
```

### 02-03. 배열 복사

- ES5에서 배열을 복사하려면 slice 메서드를 사용한다.
- 스프레드 문법을 사용하면 더욱 간결하고 가독성 좋게 표현할 수 있다.
- 이때 두 방식 모두 원본 배열의 각 요소를 `얕은 복사(shallow copy)` 하여 새로운 복사본을 생성한다.

```js
// ES5
var origin = [1, 2];
var copy = origin.slice();

console.log(copy); // [1, 2]
console.log(copy === origin); // false

// ES6
const origin = [1, 2];
const copy = [...origin];

console.log(copy); // [1, 2]
console.log(copy === origin); // false
```

### 02-04. 이터러블을 배열로 전환

- ES5에서 이터러블을 배열로 변환하려면 `Function.prototype.apply`또는 `Function.prototype.call` 메서드를 사용하여 slice 메서드를 호출해야 한다.
  - 이 방법은 이터러블뿐만 아니라 이터러블이 아닌 유사 배열 객체도 배열로 변환할 수 있다.

```js
// ES5
function sum() {
  // 이터러블이면서 유사 배열 객체인 arguments를 배열로 변환
  var args = Array.prototype.slice.call(arguments);

  return args.reduce(function (pre, cur) {
    return pre + cur;
  }, 0);
}

console.log(sum(1, 2, 3)); // 6

// ES6
// 스프레드 문법을 사용하면 좀 더 간편하게 이터러블을 배열로 변환할 수 있다.
function sum() {
  // 이터러블이면서 유사 배열 객체인 arguments를 배열로 변환
  return [...arguments].reduce((pre, cur) => pre + cur, 0);
}

console.log(sum(1, 2, 3)); // 6
```

## 03. 객체 리터럴 내부에서 사용하는 경우

- Rest 프로퍼티와 함께 스프레드 프로퍼티를 사용하면 객체 리터럴의 프로퍼티 목록에서도 스프레드 문법을 사용할 수 있다.
- 스프레드 프로퍼티가 제안도기 이전에는 ES6에서 도입된 `Object.assign`메서드를 사용하여 여러 개의 객체를 병합하거나 특정 프로퍼티를 변경 또는 추가 했다.

```js
// 스프레드 프로퍼티
// 객채 복사(얕은 복사)
const obj = { x: 1, y: 2 };
const copy = { ...obj };
console.log(copy); // { x: 1, y: 2 }
console.log(obj == copy); // false

// 객체 병합
const merged = { x: 1, y: 2, ...{ a: 3, b: 4 } };
console.log(merged); // { x: 1, y: 2, a: 3, b: 4 }
```
