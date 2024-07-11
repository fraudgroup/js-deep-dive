# 제어문

## 01. 블록문

- 0개 이상의 문을 중괄호로 묶은 것
- 자체 종결성을 가지므로 끝에 세미콜론을 붙이지 않음

```js
// 블록문
{
  var foo = 10;
}

// 제어문
var x = 1;
if (x < 10) {
  x++;
}

//함수 선언문
function sum(a, b) {
  return a + b;
}
```

## 02. 조건문

- 주어진 조건식의 평가 결과에 따라 코드 블록의 실행을 결정
- **if ... else** 문, **switch** 문

### if ... else 문

- 조건식의 평가 결과에 따라 실행할 코드 블록 결정
- if문의 조건식은 불리언 값으로 평가되어야 함
- true인 경우 if문 코드 블록이 실행
- false인 경우 else문 코드 블록이 실행 (옵션)
- 조건식을 추가하여 조건에 따라 실행될 코드 블록을 늘리고 싶으면 else if문 사용 (옵션)

```js
if (조건식) {
  // true인 경우
} else {
  // false인 경우
}
```

### switch 문

- 표현식을 평가하여 그 값과 일치하는 표현식을 갖는 case 문으로 실행 흐름을 옮김
- 일치하는 case 문이 없다면 default 문으로 이동
- default 문은 옵션
- break 문이 없으면 case 문의 표현식과 일치하지 않더라도 실행 흐름이 다음 case 문으로 연이어 이동(fall through)하기 때문에 주의할 것

```js
switch (표현식) {
  case 표현식1:
    실행문;
    break;
  case 표현식2:
    실행문;
    break;
  default:
    실행문;
}
```

### if ... else vs. switch

- if ... else

  - 논리적 참, 거짓으로 실행할 코드 블록 결정 시 사용

- switch

  - 다양한 상황에 따라 실행할 코드 블록 결정 시 사용

## 03. 반복문

- 조건식 평가 결과가 참인 경우 코드 블록 실행
- 조건식이 거짓일 때까지 반복
- **for** 문, **while** 문, **do ... while** 문
  > forEach메서드, for ... in 문, for ... of 문 등으로 반복문을 대체할 수 있는 다양한 기능 존재

### for 문

조건식이 거짓으로 평가될 때까지 코드 블록을 반복 실행

```js
// for 문
for (변수 선언문 또는 할당문; 조건식; 증감식) {
  실행문;
}

// 중첩 for 문
for (변수 선언문 또는 할당문; 조건식; 증감식) {
  for (변수 선언문 또는 할당문; 조건식; 증감식) {
    실행문;
  }
}
```

### while 문

주어진 조건식 평가 결과가 참이면 계속 반복 실행

> for 문은 반복 횟수가 명확할 때 주로 사용, while 문은 반복 횟수가 불명확할 때 주로 사용

조건식 평가 결과가 언제나 참이면 무한루프가 발생하므로 코드 내에 if 문으로 탈출 조건을 만들고 break 문으로 코드 블록을 탈출

```js
var count = 0;

// 무한루프
while (true) {
  console.log(count);
  count++;

  // 탈출 조건
  if (count === 3) break;
}
```

### do while 문

- 코드 블록을 먼저 실행하고 조건식을 평가
- 따라서 코드 블록은 무조건 한 번 이상 실행됨

```js
var count = 0;
do {
  console.log(count);
  count++;
} while (count < 3);
```

### break 문

- 코드 블록 탈출
- 레이블 문, 반복문, switch 문의 코드 블록 외에 break 문을 사용하면 SyntaxError 발생

### continue 문

- 반복문의 코드 블록 실행을 현 지점에서 중단하고 반복문의 증감식으로 실행 흐름을 이동
- 반복문을 탈출하진 않음

```js
// continue 문 사용 안할 시
for (var i = 0; i < string.length; i++) {
  if (string[i] === search) {
    count++;
  }
}

// continue 문 사용 시
for (var i = 0; i < string.length; i++) {
  if (string[i] !== search) continue;
  count++;
}
```
