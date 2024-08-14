# 47장 에러 처리

## 1. 에러의 전파란 무엇인가요?

에러 전파란, throw 문에 의해 발생한 에러가 해당 코드를 감싼 try … catch 블록에서 처리되지 않으면, 호출자 방향으로 에러가 전달되는 것을 의미합니다. 에러는 호출 스택을 거슬러 올라가며 전파되며, 적절한 catch 블록에서 처리되지 않으면 결국 프로그램이 강제 종료됩니다.

### 에러의 전파와 관련된 예시(보경님 제공)

```js
export const postPaymentSuccess = async (payload: PaymentConfirmRequest) => {
  const token = await getCookie("accessToken");

  try {
    const res = await fetch(`${BASE_URL}/api/v1/payments/success`, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Authorization: `Bearer ${token}`,
      },
      body: JSON.stringify(payload),
    });

    const result = await res.json();

    if (res.ok) {
      return result;
    }

    throw new Error(
      result.message ||
        "결제 진행중 오류가 발생하였습니다. 잠시후 다시 시도해주세요."
    );
  } catch (error) {
    throw error;
  }
};
```

#### ==== 사용처 예시 ====

```js
const onSubmit: SubmitHandler<FieldValues> = (payload) => {
  putPaymentMutation(payload, {
    onSuccess: () => {
      setIsPutPaymentSucceed(true);
    },
    onError: (error) => {
      toast.error(error.message);
    },
  });
};
```

<br />

## 2. 아래의 코드에서 적절한 예외처리 코드를 작성해주세요.

```js
const $button = document.querySelector("button");

$button.classList.add("disabled");
```

### 답변

```js
if ($button !== null) {
  $button.classList.add("disabled");
} else {
  console.log("Button element not found in the DOM.");
}
```

또는 옵셔널 체이닝 사용

<br />

## 3. 에러 처리가 필요한 이유

- 발생한 에러에 대해 대처하지 않고 방치하면 프로그램은 강제 종료된다.
- 에러에 적절하게 대응하면 프로그램이 강제 종료되지 않고 계속해서 코드를 실행시킬 수 있다.
