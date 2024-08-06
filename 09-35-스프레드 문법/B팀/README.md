## 목차

1. [스프레드 문법과 Rest 파라미터와 차이](#rest-파라미터와-차이)
2. [배열 리터럴 내부에서 사용](#배열-리터럴-내부에서-사용)
3. [일반 객체 내부에서 사용](#일반-객체-내부에서-사용)

<br />

- 스프레드 문법 사용 대상: Array, String, Map, Set 등 for … of 문으로 순회할 수 있는 이터러블
- 스프레드 문법의 결과는 값이 아니고, 값들의 목록이다. 따라서 변수에 할당할 수 없다.

⇒ 함수 호출문의 인수 목록, 배열 리터럴의 요소 목록, 객체 리터럴의 프로퍼티 목록에 사용할 수 있다.

## Rest 파라미터와 차이

- Rest 파라미터와 형태가 동일해 혼동할 수 있으므로 `주의`
- Rest 파라미터는 함수에 전달된 인수들의 목록을 배열로 전달받기 위해 매개변수 이름 앞에 …을 붙이는 것
- 스프레드 문법은 이터러블을 펼쳐서 개별 값의 목록을 만드는 것

## 배열 리터럴 내부에서 사용

```jsx
// 배열 복사
const origin = [1, 2];
const copy1 = [...origin];
const copy2 = origin.slice();

// 배열 결합
const arr = [1, 2].concat([3, 4]);
console.log(arr) / [1, 2, 3, 4];

const arr2 = [...[1, 2], ...[3, 4]];
console.log(arr2) / [1, 2, 3, 4];

const arr3 = [1, 4];
const arr4 = [2, 3];
arr3.splice(1, 0, ...arr4);
console.log(arr3); // [1, 2, 3, 4]
```

- 이터러블이 아닌 유사 배열 객체는 스프레드 문법 대상이 될 수 없다.

⇒ Array.from 메서드를 사용하면 유사 배열 객체를 배열로 바꿀 수 있다.

```jsx
const arrayLike = {
0: 1,
1: 2,
2: 3,
length: 3
};

const arr = [...arrayLike]; => 타입 에러

Array.from(arrayLike); // [1, 2, 3]
```

## 일반 객체 내부에서 사용

```jsx
// 객체 병합: 프로퍼티 중복 시 뒤에 위치한 프로퍼티가 우선권을 가진다.
const merged = { ...{ x: 1, y: 2 }, ...{ y: 10, z: 3 } };
console.log(merged); // {x: 1, y: 10, z: 3}

// 프로퍼티 추가
const added = { ...{ x: 1, y: 2 }, z: 0 };
console.log(added); // {x: 1, y: 2, z: 0};
```
