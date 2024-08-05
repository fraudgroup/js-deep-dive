## 목차

1. [Set](#371-set)
2. [Map](#372-map)

## 37.1 Set

- 중복되지 않는 유일한 값들의 집합
- 배열과 유사하지만 요소 순서에 의미가 없고, 인덱스로 요소에 접근할 수 없다.
- 교집합, 합집합, 차집합, 여집합 등을 구현할 수 있다.
- Set 생성자 함수는 이터러블을 인수로 전달받아 Set 객체를 생성한다.
- `Set.prototype.size`: Set 객체의 요소 개수를 확인할 때 사용 / 변경 불가
- `Set.prototype.add`: Set 객체에 요소를 추가할 때 사용 / 중복 요소 추가X
- `Set.prototype.has`: 특정 요소의 존재 여부를 나타내는 불리언 값 반환
- `Set.prototype.delete`: 특정 요소를 삭제 시 사용, 삭제 여부를 불리언 값 반환 / 삭제하려는 요소값을 인수로 전달해야 한다.(인덱스X)
- `Set.prototype.clear`: 모든 요소 일괄 삭제
- `Set.prototype.forEach`(현재 순회 중인 요소값, 현재 순회 중인 요소값, 현재 순회 중인 Set 객체 자체)
- for of 문으로 순회할 수 있다.
- 집합 연산
  - 교집합
  ```jsx
  Set.prototype.intersection = function (set) {
    return new Set([...this].filter((v) => set.has(v)));
  };
  ```
  - 차집합
  ```jsx
  Set.prototype.difference = function (set) {
    return new Set([...this].filter((v) => !set.has(v)));
  };
  ```
  - 합집합
  ```jsx
  Set.prototype.union = function (set) {
    return new Set([...this, ...set]);
  };
  ```
  - 부분 집합
  ```jsx
  Set.prototype.isSuperset = function (subset) {
    const supersetArr = [...this];
    return [...subset].every((v) => supersetArr.includes(v));
  };
  ```

## 37.2 Map

- 키와 값의 쌍으로 이뤄진 컬렉션이다.
- **이터러블이고, 객체를 포함한 모든 값을 키로 사용할 수 있다. (객체는 문자열 또는 심벌 값만 키로 사용할 수 있다.)**
- 중복된 키를 가진 요소가 존재할 수 없다.
- `Map.prototype.size`: 요소 개수 확인
- `Map.prototype.set`: 요소 추가 / 연속 호출 가능
- `Map.prototype.get`: Map 객체에서 특정 키를 갖는 값을 반환한다. 인수로 전달한 키를 갖는 요소가 존재하지 않으면 undefined를 반환한다.
- `Map.prototype.has`: 특정 요소의 존재 여부를 나타내는 불리언 값 반환
- `Map.prototype.delete`: 삭제 성공 여부를 나타내는 불리언 값 반환
- `Map.prototype.clear`: Map 객체 요소 일괄 삭제
- `Map.prototype.forEach`(현재 순회 중인 요소값, 현재 순회 중인 요소키, 현재 순회 중인 Map 객체 자체)
- Map 객체는 이터러블이면서 동시에 이터레이터인 객체를 반환하는 메서드를 제공한다.
  - Map.prototype.keys: Map 객체에서 요소키를 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
  - Map.prototype.values: Map 객체에서 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
  - Map.prototype.keys: Map 객체에서 요소키와 요소값을 값으로 갖는 이터러블이면서 동시에 이터레이터인 객체를 반환한다.
