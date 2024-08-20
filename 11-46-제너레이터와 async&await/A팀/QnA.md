# 질문 1. 해당 코드의 절차를 설명해 주세요. 프로미스와 태스크큐, 콜 스택을 연결해서 설명해 주세요.

## 답. 
```js
async function run() {
  console.log("A. Before calling asyncTask");

  await asyncTask();  // asyncTask가 완료될 때까지 대기합니다.

  console.log("C. After calling asyncTask");
}

run();
```

- console(B) async function => await 키워드 console.log("A.Before calling asyncTask"); 
- await asyncTask();//
- console.log("C. After calling asyncTask");

- 콜 스택: asyncTask() 함수가 호출되어 콜 스택에 추가됩니다.
- 실행: asyncTask 함수가 실행되기 시작하고, await 키워드를 만나면 해당 - 비동기 작업(즉, asyncTask의 프로미스)이 해결될 때까지 run 함수의 실행이 일시 중지됩니다.
- 프로미스 생성: asyncTask가 반환하는 프로미스가 생성되고, 비동기 작업이 테스크 큐에 추가될 준비가 됩니다.
- 콜 스택: asyncTask 함수의 실행이 일시 중지되면서 콜 스택에서 제거됩니다.
- 실행 중지: run 함수도 await에서 대기 상태에 들어가면서 콜 스택에서 제거됩니다.


## 질문 2. async, await와 프로미스의 차이를 설명해주세요.

## 답. 
- async 함수는 항상 프로미스를 반환하며, 함수 내부에서 await 키워드를 사용해 프로미스 결과를 기다립니다.
- await는 비동기 처리가 끝날 때까지 함수 실행을 일시 중단시키고, 결과값이 나오면 다시 실행을 계속합니다.
- 프로미스는 .catch() 메서드로 에러를 처리하고, async, await는 프로미스의 후속 처리 메서드 없이 try/catch 문을 사용해 에러를 처리합니다.
- 프로미스는 콜백 패턴을 사용해 가독성이 좋지 않습니다. 반면, async,await를 사용하면 중첩된 .then() 호출을 피할 수 있어 가독성이 좋은 코드를 작성할 수 있습니다.
- async, await는 일반적인 동기 코드 흐름을 가지고 있어 디버깅이 더 수월할 수 있습니다.