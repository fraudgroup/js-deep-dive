# 호출 스케줄링

: 함수를 명시적으로 호출하지 않고, 일정 시간이 경과된 이후에 호출되도록 함수 호출을 예약할 때 타이머 함수를 사용하는 것

- 타이머 함수는 호스트 객체다. (브라우저 환경과 Node.js 환경에서 모두 제공)
- 자바스크립트 엔진은 단 하나의 실행 컨텍스트 스택을 가지기 때문에 두 가지 이상의 태스크를 동시에 실행할 수 없음 (싱글 스레드)
  ⇒ 타이머 함수는 비동기 처리 방식으로 동작함

# 타이머 함수

## 1. `setTimeout` / `clearTimeout`

| 매개변수            | 설명                                                                                                                                                                                                                                                                                                                                                                        |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| func                | 타이머가 만료된 뒤 호출될 콜백 함수 \* 콜백 함수 대신 문자열을 전달할 수 있다. 이때 코드 문자열은 타이머가 만료된 뒤 해석되고 실행된다. 이는 흡사 eval 함수와 유사하며 권장하지는 않는다.                                                                                                                                                                                   |
| delay               | 타이머 만료 시간(밀리초(ms)단위). setTimeout 함수는 delay 시간으로 단 한 번 동작하는 타이머를 생성한다. 인수 전달을 생략한 경우 기본값 0이 지정된다. - delay 시간이 설정된 타이머가 만료되면 콜백 함수가 즉시 호출되는 것이 보장되지는 않는다. delay 시간은 태스크 큐에 콜백 함수를 등록하는 시간을 지연할 뿐이다. - delay가 4ms 이하인 경우 최소 지연 시간 4ms가 지정된다. |
| param1, param2, ... | 호출 스케줄링된 콜백 함수에 전달해야할 인수가 존재하는 경우 세 번째 이후의 인수로 전달할 수 있다. \* IE9 이하에서는 전달할 수 없다.                                                                                                                                                                                                                                         |

---

<br />

- `setTimeout` 함수는 생성된 타이머를 식별할 수 있는 고유한 타이머 id를 반환한다.
  - 브라우저 환경이면 숫자
  - Node.js 환경이면 객체
- `setTimeout` 함수가 반환한 타이머 id를 `clearTimeout` 함수의 인수로 전달하여 타이머를 취소할 수 있다.

## 2. `setInterval` / `clearInterval`

: 두 번째 인수로 전달받은 시간(ms, 1/1000초) 으로 반복 동작하는 타이머를 생성한다.

- 첫 번째로 전달받은 콜백 함수를 타이머가 만료될 때마다 반복 호출한다.
  - `clearInterval`로 취소될 때까지
- 나머지 동작은 `setTimeout`, `clearTimeout`과 동일하게 사용한다.

# 디바운스와 스로틀

- 짧은 시간 간격으로 연속해서 발생하는 이벤트(`scroll`, `resize`, `input`, `mousemove`)를 그룹화해서 과도한 이벤트 핸들러의 호출을 방지하는 프로그래밍 기법

```html
<!DOCTYPE html>
<body>
  <button>click me</button>
  <pre>일반 클릭 이벤트 카운터 <span class="normal-msg">0</span></pre>
  <pre>디바운스 클릭 이벤트 카운터 <span class=".debounce-msg">0</span></pre>
  <pre>스로틀 클릭 이벤트 카운터 <span class="throttle-msg">0</span></pre>
  <script>
    const $button = document.querySelector('button');
    const $normalMsg = document.querySelector('.normal-msg');
    const $debounceMsg = document.querySelector('.debounce-msg')
    const $throttleMsg = document.querySelector('.throttle-msg');

    const debounce = (callback, delay) => {
      let timerId;
      return event => {
        if(timerId) clearTimeout(timerId);
        timerId = setTimeout(callback, delay, event);
      };
    };

    const throttle = (callback, delay) => {
      let timerId;
      return event => {
        if(timerId) return;
        timerId = setTimeout(()=> {
          callback(event);
          timerId = null;
        }, delay, event);
      }
    }

    $button.addEventListener('click', ()=> {
      $normalMsg.textContent = $normalMsg.textContent + 1;
    });

    $button.addEventListener('click', debounce(()=> {
      $debounceMsg.textContent = $debounceMsg.textContent + 1;
    }, 500));

    $button.addEventListener('click', ()=>{
      $throttleMsg.textContent = $throttleMsg.textContent + 1;
    }, 500);
  </script>
</body>
</html>
```

> 일반 클릭 이벤트 카운터 20
> 디바운스 클릭 이벤트 카운터 1
> 스로틀 클릭 이벤트 카운터 6

## 디바운스

: 짧은 시간 간격으로 이벤트가 연속해서 발생하면 이벤트 핸들러를 호출하지 않다가 일정 시간이 경과한 이후에 이벤트 핸들러가 한 번만 호출되도록 한다.

즉, 디바운스는 짧은 시간 간격으로 발생하는 이벤트를 그룹화해서 마지막에 한 번만 이벤트 핸들러가 호출되도록 한다.

- resize 이벤트 처리나 input 요소에 입력된 값으로 ajax 요청하는 입력 필드 자동완성 UI 구현, 버튼 중복 클릭 방지 처리 등에 유용하게 사용된다.

### 예시

: input 이벤트가 300ms 안에 여러 번 일어나는 경우

```html
<!DOCTYPE html>
<body>
  <input type="text">
  <div class="msg"></div>
  <script>
    const $input = document.querySelector("input");
    const $msg = document.querySelector(".msg");

    const debounce = (callback, delay) => {
      let timerId;
     //debounce 함수는 timerId를 기억하는 클로저를 반환한다
     return (event) => {
       // delay가 경과하기 이전에 이벤트가 발생하면 이전 타이머를 취소하고 새로운 타이머를 재설정한다
        // 따라서 delay보다 짧은 간격으로 이벤트가 발생하면 callback은 호출되지 않는다
       if (timerId) clearTimeout(timerId);
       timerId = setTimeout(callback, delay, event);
     };
    };

    // debounce 함수가 반환하는 클로저가 이벤트 핸들러로 등록된다
    // 300ms 보다 짧은 간격으로 input 이벤트가 발생하면 debounce 함수의 콜백 함수는
    // 호출되지 않다가 300ms 동안 input 이벤트가 더 이상 발생하지 않으면 한 번만 호출된다
    $input.oninput = debounce((e) => {
      $msg.textContent = e.target.value;
    }, 300);
  </script>
</body>
</html>
```

- 실무에서는 Underscore의 debounce 함수나 Lodash의 debounce 함수를 사용하는 것이 권장된다.

## 스로틀

: 짧은 시간 간격으로 이벤트가 연속해서 발생하더라도 일정 시간 간격으로 이벤트 핸들러가 최대 한 번만 호출되도록 한다.

즉, 짧은 시간 간격으로 연속해서 발생하는 이벤트를 그룹화해서 일정 시간 단위로 이벤트 핸들러가 호출되도록 호출 주기를 만든다.

- scroll 이벤트 처리나 무한 스크롤 UI 구현에 유용하게 사용된다.
