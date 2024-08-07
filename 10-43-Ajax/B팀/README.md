# 43장 Ajax

## 목차

1. [Ajax란?](#1-ajax란)
2. [JSON](#2-json)
3. [XMLHttpRequest](#3-xmlhttprequest)

<br />

## 1. Ajax란?

Ajax(Asynchronous JavaScript and XML)란 자바스크립트를 사용하여 브라우저가 서버에게 비동기 방식으로 데이터를 요청하고, 서버가 응답한 데이터를 수신하여 웹페이지를 동적으로 갱신하는 프로그래밍 방식을 말한다. 브라우저에서 제공하는 Web API인 `XMLHttpRequest` 객체를 기반으로 동작한다.

**장점**

1. 변경할 부분을 갱신하는 데 필요한 데이터만 서버로부터 전송받기 때문에 불필요한 데이터 통신이 발생하지 않는다.
2. 변경할 필요가 없는 부분은 다시 렌더링하지 않는다. 화면이 순간적으로 깜박이는 현상이 발생하지 않는다.
3. 클라이언트와 서버와의 통신이 비동기 방식으로 동작하기 때문에 서버에게 요청을 보낸 이후 블로킹이 발생하지 않는다.

<br />

## 2. JSON

JSON(JavaScript Object Notation)은 클라이언트와 서버 간의 HTTP 통신을 위한 텍스트 데이터 포맷이다.

<br />

### 2-1. JSON 표기 방식

JSON은 자바스크립트의 객체 리터럴과 유사하게 키와 값으로 구성된 **순수한 텍스트**다.

키는 반드시 큰따옴표(작은따옴표 사용 불가)로 묶어야 한다. 값은 객체 리터럴과 같은 표기법을 그대로 사용할 수 있다. 하지만 문자열은 반드시 큰따옴표(작은따옴표 사용 불가)로 묶어야 한다.

```json
{
  "name": "Lee",
  "age": 20,
  "alive": true,
  "hobby": ["traveling", "tennis"]
}
```

<br />

### 2-2. JSON.stringify

객체를 JSON 포맷의 문자열로 변환한다. 클라이언트가 서버로 객체를 전송하려면 객체를 문자열화해야 하는데 이를 직렬화(serializing)라 한다.

<br />

### 2-3. JSON.parse

JSON 포맷의 문자열을 객체로 변환한다. 서버로부터 클라이언트에게 전송된 JSON 데이터는 문자열이다. 이 문자열을 객체로서 사용하려면 JSON 포맷의 문자열을 객체화해야 하는데 이를 역직렬화(deserializing)라 한다.

<br />

## 3. XMLHttpRequest

자바스크립트를 사용하여 HTTP 요청을 전송하려면 XMLHttpRequest 객체를 사용한다.

<br />

### 3-1. XMLHttpRequest 객체 생성

`XMLHttpRequest` 생성자 함수를 호출하여 생성한다. 브라우저에서 제공하는 Web API이므로 브라우저 환경에서만 정상적으로 실행된다.

```js
const xhr = new XMLHttpRequest();
```

<br />

### 3-2. XMLHttpRequest 객체의 프로퍼티와 메서드

`XMLHttpRequest` 객체는 다양한 프로퍼티와 메서드를 제공한다.

<br />

### 3-3. HTTP 요청 전송

HTTP 요청을 전송하는 경우 다음 순서를 따른다.

1. `XMLHttpRequest.prototype.open` 메서드로 HTTP 요청을 초기화한다.
2. 필요에 따라 `XMLHttpRequest.prototype.setRequestHeader` 메서드로 특정 HTTP 요청의 헤더 값을 설정한다.
3. `XMLHttpRequest.prototype.send` 메서드로 HTTP 요청을 전송한다.

```js
// XMLHttpRequest 객체 생성
const xhr = new XMLHttpRequest();

// HTTP 요청 초기화
xhr.open('GET', '/users');

// HTTP 요청 헤더 설정
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');

// HTTP 요청 전송
xhr.send();
```

<br />

#### XMLHttpRequest.prototype.open

서버에 전송할 HTTP 요청을 초기화한다.

```js
xhr.open(method, url[, async]);
```

| 매개변수 |                        설명                         |
| :------: | :-------------------------------------------------: |
|  method  | HTTP 요청 메서드(`GET`, `POST`, `PUT`, `DELETE` 등) |
|   url    |               HTTP 요청을 전송할 URL                |
|  async   |          비동기 요청 여부. 기본값은 `true`          |

<br />

#### XMLHttpRequest.prototype.send

`open` 메서드로 초기화된 HTTP 요청을 서버에 전송한다. `GET`, `POST` 요청 메서드에 따라 전송 방식에 차이가 있다.

- GET: 데이터를 URL의 일부인 쿼리 문자열(query string)로 서버에 전송한다.
- POST: 데이터(페이로드)를 요청 몸체(request body)에 담아 전송한다.

⚠️ 페이로드가 객체인 경우 반드시 `JSON.stringify` 메서드를 사용하여 직렬화한 다음 전달해야 한다.

HTTP 요청 메서드가 `GET`인 경우 send 메서드에 페이로드로 전달한 인수는 무시되고 요청 몸체는 `null`로 설정된다.

<br />

#### XMLHttpRequest.prototype.setRequestHeader

HTTP 요청의 헤더 값을 설정한다. 반드시 `open` 메서드를 호출한 이후에 호출해야 한다.

클라이언트가 서버로 전송할 데이터 MIME 타입을 지정할 때는 `content-type`을 사용한다.

서버가 응답할 데이터 MIME 타입을 지정할 때는 `accept`를 사용한다.

```js
// 클라이언트가 서버로 전송할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('content-type', 'application/json');

// 서버가 응답할 데이터의 MIME 타입 지정: json
xhr.setRequestHeader('accept', 'application/json');
```

`accept` 헤더를 설정하지 않으면 `*/*`으로 전송된다.

<br />

### 3-4. HTTP 응답 처리

서버가 전송한 응답을 처리하려면 `XMLHttpRequest` 객체가 발생시키는 이벤트를 캐치해야 한다.

`XMLHttpRequest`가 갖는 이벤트 핸들러 프로퍼티(`onreadystatechange`, `onload`, `onerror`, ...)로 응답을 처리할 수 있다.
