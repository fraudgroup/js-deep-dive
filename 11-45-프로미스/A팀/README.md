# 45장 프로미스

## 목차

1. [비동기 처리를 위한 콜백 패턴의 단점](#1-비동기-처리를-위한-콜백-패턴의-단점)
2. [프로미스 생성](#2-프로미스-생성)
3. [프로미스 후속 처리 메서드](#3-프로미스-후속-처리-메서드)
4. [프로미스의 에러 처리](#4-프로미스의-에러-처리)
5. [프로미스 체이닝](#5-프로미스-체이닝)
6. [프로미스의 정적 메서드](#6-프로미스의-정적-메서드)
7. [마이크로 태스크 큐](#7-마이크로-태스크-큐)
8. [fetch](#-fetch)

<br />

## 1. 비동기 처리를 위한 콜백 패턴의 단점

### 1. 콜백 헬
```js
  const get = usl => {
    const xhr = new XMLHttpRequest();
    ~

    xhr.onload = () => {
      ~
    }
  }

  get(~);
```
- 위의 코드는 비동기 함수입니다. get() 함수가 호출된 이후, onload가 함수 평가 단계에서 테스크 큐에 저장됩니다. 이후, 전역 실행 컨텍스트가 종료되면, 이벤트 루프에 의해 콜 스택으로 푸시되어 실행됩니다.

- 이는 실행 컨텍스트가 기존의 동기 함수처럼 동작하지 않습니다. 기존의 전역 실행 컨텍스트의 스코프 체인으로 동작하던 동기 함수와 달리, 전역 실행 컨텍스트가 콜 스택에서 제거된 이후에 동작하기 때문에, 스코프 체인을 이룰 수 없게 됩니다.

- 이러한 동작을 극복하기 위해, 비동기 함수의 결과는 비동기 함수를 다시금 호출해 테스크 큐의 순서를 누적시켜 후속 처리를 진행해야 합니다. 이렇게 콜백 함수를 누적 시켜, 함수의 복잡도가 높아지는 형상을 **콜백 헬**이라고 합니다.

### 2. 에러 처리의 한계
- 함수의 에러처리의 경우, 자신을 호출한 호출자 방향으로 전파합니다. 그러나, 비동기 함수의 경우, 이미 콜 스택에서 모든 실행 컨텍스트가 제거된 이후에 푸시되므로, 자신을 호출한 호출자 방향이 없습니다. 이러한 에러 처리의 한계가 발생했습니다. 이를 극복하기 위해, 프로미스가 등장했습니다.

<br />

## 2. 프로미스 생성
- 프로미스는 Promise 생성자 함수의 new 연산자를 통해 생성합니다. 사용법은 아래와 같습니다.
```js
  const promise = new Promise((resolve, reject) => {
    if (~) {
      resolve('resolve');
    } else {
      reject('reject');
    }
  });
```
- 프로미스 생성자 함수는 기본적으로 콜백 함수를 받습니다. 전달 받은 콜백 함수는 resolve, reject 2개의 인수(2개의 인수 역시 함수)를 받습니다.
- 만약 비동기 처리가 성공하면, 비동기 처리 결과를 resolve 함수의 인수로 전달합니다. 실패하면, reject 함수의 인수로 전달합니다.

- 프로미스 객체 내부를 보면 status, result 2가지 프로퍼티가 있습니다. 최초, status는 pending 상태를 유지합니다. 
- 성공 시, status는 fulfilled, result는 resolve 함수의 인수로 전달한 값을 할당 받습니다.
- 실패 시, status는 rejected, result는 reject 함수의 인수로 전달한 값을 할당 받습니다.

```js
function Promise(executor) {
    let state = 'pending';  // 'pending', 'fulfilled', 'rejected' 중 하나
    let value;  // 프로미스가 이행되거나 거부될 때 저장되는 값
    let handlers = [];  // .then()과 .catch()에서 사용되는 핸들러 저장

    function resolve(result) {
        if (state !== 'pending') return;
        state = 'fulfilled';
        value = result;
        processHandlers();
    }

    function reject(error) {
        if (state !== 'pending') return;
        state = 'rejected';
        value = error;
        processHandlers();
    }

    function processHandlers() {
        // 비동기적으로 핸들러를 처리
        queueMicrotask(() => {
            handlers.forEach(handle);
            handlers = [];  // 핸들러 초기화
        });
    }

    function handle(handler) {
        if (state === 'fulfilled') {
            handler.onFulfilled(value);
        } else if (state === 'rejected') {
            handler.onRejected(value);
        } else {
            handlers.push(handler);  // 아직 이행되지 않았으면 핸들러 저장
        }
    }

    this.then = function(onFulfilled, onRejected) {
        return new Promise((resolve, reject) => {
            handle({
                onFulfilled: function(value) {
                    try {
                        const result = onFulfilled(value);
                        resolve(result);
                    } catch (err) {
                        reject(err);
                    }
                },
                onRejected: function(value) {
                    try {
                        const result = onRejected(value);
                        resolve(result);
                    } catch (err) {
                        reject(err);
                    }
                }
            });
        });
    };

    this.catch = function(onRejected) {
        return this.then(null, onRejected);
    };

    try {
        executor(resolve, reject);
    } catch (err) {
        reject(err);
    }
}
```

- 이는 Promise 생성자 함수의 대략적인 모식도 함수 입니다. (정확하지는 않습니다.) 2번의 중첩된 콜백 함수 (executor(resolve(~), reject(~)))를 통해, **클로저 함수**로 구성된다는 것을 알 수 있습니다.


<br />

## 3. 프로미스 후속 처리 메서드
- 프로미스의 상태 결과에 따라 무언가를 수행해야만 합니다(결과 처리, 에러 처리). 이 처리 상태의 변화에 따라, 후속 처리 메서드에 인수로 전달한 콜백 함수가 선택적으로 호출됩니다.
- 위에서 this.then, this.catch에서 알 수 있듯, Promise의 메서드라는 것을 알 수 있습니다.

### 1. Promise.prototype.then
- 프로미스 객체의 메서드로 호출합니다. 인수는 2개를 받습니다.

```js
  new Promise(resolve => resolve('fulfilled'), reject => reject(new Error('error')))
    .then(v => console.log(v), e => console.error(e));
```

- 프로미스의 resolve 내부 함수의 인수 fulfilled 가 프로미스 fulfilled 상태가 되었을 때, value로 저장됩니다. 후속 처리 then에 의해, value는 consol.log(v)에 의해 출력됩니다.
- 에러 역시 유사한 흐름입니다.

### 2. catch
- catch 메서드는 에러에 대한 인수만 받습니다.

```js
  new Promise(resolve => resolve('fulfilled'), reject => reject(new Error('error')))
    .catch(e => console.error(e));
```

### 3. finally
- finally는 프로미스의 상태에 상관 없이 한 번만 호출됩니다.


<br />

## 4. 프로미스의 에러 처리
- 후속 처리 메서드를 통해, 기존 콜백 패턴의 비동기 함수 에러 문제를 해결할 수 있습니다.

<br />

## 5. 프로미스 체이닝
- 프로미스 패턴은 콜백 헬을 다음과 같은 형태로 전환합니다.

```js
    const url = 'http://~'

    promiseGet(`${url}/post/1`)
      .then(({userId}) => promiseGet(`${url}/users/${userId}`))
      .then(userInfo => console.log(userInfo))
      .catch(err => console.error(err));
```
- 이처럼, 프로미스 결과를 연쇄적으로 연결해야 하는 경우를 **프로미스 체이닝**이라고 합니다. 이는 비동기 함수의 코드 복잡성을 극복했습니다. 다만, 코드의 가독성을 저해할 수 있습니다. 코드의 가독성 역시 확복한 새로운 문법은 async/await 구문입니다.

<br />

## 6. 프로미스의 정적 메서드
### 1. Promise.resolve / Promise.reject
- 기존의 콜백 함수 인수로 받는 것이 아니라, 메서드로 호출해 사용합니다. 기존에 존재하는 값, 혹은 에러를 프로미스로 전환할 경우 사용합니다.

### 2. Promise all
- 여려 개의 비동기 처리를 모두 병렬 처리할 때 사용합니다. 모든 프로미스가 fulfilled 상태가 될 경우, resolve로 전달합니다.

### 3. Promise.race
- all 메서드와 유사하지만, 모든 프로미스가 fulfilled 되는 것을 기다리지 않고, 먼저 fulfilled 상태가 된 프로미스를 처리 결과를 resolve로 전달합니다.

### 4. Promise.allSettled
- 모든 프로미스가 settled (fulfilled 또는 rejected)된 상태일 때, 결과를 배열로 리턴합니다.

<br />

## 7. 마이크로 태스크 큐
- 프로미스의 후속 처리 메서드의 콜백 함수는 마이크로 태스크 큐에 저장됩니다. 마이크로 태스크 큐는 태스크 큐보다 우선 순위가 높아, 먼저 실행 됩니다.


<br />

## 8. fetch
- fetch는 XHR 객체의 사용법을 Promise 기반으로 개선한 새로운 함수입니다. fetch 함수는 Http 응답인 Response 객체를 래핑한 Promise 객체를 반환 합니다. 즉, new Promise 내부 프로퍼티 value에 저장된 값이 Response의 참조 값인 셈입니다.


<br />


