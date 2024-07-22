- 봉찬 :

  ```js
  var str = "";
  var length = str && str.length;
  console.log(length);
  var length2 = str?.length;
  console.log(length2);
  ```

  출력값은?

  - 유경 : undefined, 0
  - 지원 : '', 0
  - 은수 : 0, 0
  - **정답 : '', 0**

- 봉찬 :

  ```js
  var foo = "" || "default string";
  console.log(foo);

  var foo = "" ?? "default string";
  console.log(foo);
  ```

  - 유경 : 'default string', 'default string'
  - 은수 : '', ''
  - 지원 : 'default string', 몰라요
  - **정답 : 'default string', ''**

- 유경 : Symbol이 암묵적 형변환되지 않는 이유를 Symbol의 특징을 근거로 설명해주세요.

  - **정답 : 유일무이한 값이라서 문자열이나 자동변환 ... 어쩌고**
