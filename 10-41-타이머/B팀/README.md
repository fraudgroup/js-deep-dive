# 41장 타이머

## 목차

1. [호출 스케줄링](#1-호출-스케줄링)
2. [타이머 함수](#2-타이머-함수)
3. [디바운스와 스로틀](#3-디바운스와-스로틀)

<br />

## 1. 호출 스케줄링

함수를 일정 시간이 경과된 이후에 호출되도록 함수 호출을 예약하려면 타이머 함수를 사용한다. 이를 호출 스케줄링이라 한다.

자바스크립트의 타이머 함수

- 타이머 생성: `setTimeout`, `setInterval`
- 타이머 제거: `clearTimeout`, `clearInterval`

타이머 함수는 ECMAScript 사양에 정의된 빌트인 함수가 아니라 브라우저 환경과 Node.js 환경에서 모두 전역 객체의 메서드로서 제공한다.

자바스크립트 엔진는 하나의 실행 컨텍스트 스택을 갖기 때문에 두 가지 이상의 태스크를 동시에 실행할 수 없는 **싱글 스레드**로 동작한다. 이런 이유로 `setTimeout`과 `setInterval`은 비동기 처리 방식으로 동작한다.

<br />

## 2. 타이머 함수

### 2-1. setTimeout / clearTimeout

`setTimeout` 함수의 콜백 함수는 두 번째 인수로 전달받은 시간 이후 단 한 번 실행되도록 호출 스케줄링된다.

```js
const timeoutId = setTimeout(func|code[, delay, param1, param2, ...]);
```

> ⚠️ **타이머 만료**
>
> 타이머 만료 시간인 `delay` 시간이 설정된 타이머가 만료되면 콜백 함수가 즉시 호출되는 것이 보장되지는 않는다.

`setTimeout` 함수가 반환한 `id`를 `clearTimeout` 함수의 인수로 전달하여 타이머를 취소할 수 있다.

<br />

### 2-2. setInterval / clearInterval

`setInterval` 함수의 콜백 함수는 두 번째 인수로 전달받은 시간이 경과할 때마다 반복 실행되도록 호출 스케줄링된다.

`setInterval` 함수가 반환한 `id`를 `clearInterval` 함수의 인수로 전달하여 타이머를 취소할 수 있다.

<br />

## 3. 디바운스와 스로틀

디바운스와 스로틀은 짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 과도한 이벤트 핸들러의 호출을 방지하는 프로그래밍 기법이다.

<br />

### 3-1. 디바운스

짧은 시간 간격으로 발생하는 이벤트를 그룹화해서 마지막에 한 번만 이벤트 핸들러가 호출되도록 한다.

```js
const debounce = (callback, delay) => {
  let timerId;
  // debounce 함수는 timerId를 기억하는 클로저를 반환한다.
  return (event) => {
    // delay가 경과하기 이전에 이벤트가 발생하면 이전 타이머를 취소하고 새로운 타이머를 재설정한다.
    // 따라서 delay보다 짧은 간격으로 이벤트가 발생하면 callback은 호출되지 않는다.
    if (timerId) clearTimeout(timerId);
    timerId = setTimeout(callback, delay, event);
  };
};
```

<br />

### 3-2. 스로틀

짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 일정 시간 단위로 이벤트 핸들러가 호출되도록 호출 주기를 만든다.

```js
const throttle = (callback, delay) => {
  let timerId;
  // throttle 함수는 timerId를 기억하는 클로저를 반환한다.
  return (event) => {
    // delay가 경과하기 이전에 이벤트가 발생하면 아무것도 하지 않다가
    // delay가 경과했을 때 이벤트가 발생하면 새로운 타이머를 재설정한다.
    // 따라서 delay 간격으로 callback이 호출된다.
    if (timerId) return;
    timerId = setTimeout(
      () => {
        callback(event);
        timerId = null;
      },
      delay,
      event
    );
  };
};
```
