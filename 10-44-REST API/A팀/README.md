REST는 HTTP/1.0과 1.0의 스펙 작성에 참여했고 아파치 HTTP 서버 프로젝트의 공동 설립자인 로이 필딩의 2000년 논문에서 처음 소개되었다.

- 발표 당시의 웹이 HTTP를 제대로 사용하지 못하고 있는 상황을 보고 HTTP의 장점을 최대할 활용할 수 있는 아키텍처로서 REST를 소개했고, 이는 HTTP 프로토콜을 의도에 맞게 디자인하도록 유도하고 있다.
- REST의 기본 원칙을 성실히 지킨 서비스 디자인을 “RESTful”이라고 표현한다.

⇒ REST는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처고, REST API는 REST를 기반으로 서비스 API를 구현한 것을 의미한다.

# REST API의 구성

- 자원 (resource)
- 행위 (verb)
- 표현 (representations)

의 3가지 요소로 구성된다.

⇒ REST는 자체의 표현구조로 구성되어 REST API만으로도 HTTP 요청의 내용을 이해할 수 있다.

| 구성 요소 | 내용                           | 표현 방법        |
| --------- | ------------------------------ | ---------------- |
| 자원      | 자원                           | URI (엔드포인트) |
| 행위      | 자원에 대한 행위               | HTTP요청 메서드  |
| 표현      | 자원에 대한 행위의 구체적 내용 | 페이로드         |

# REST API 설계 원칙

REST에서 가장 중요한 기본 원칙은 두 가지다.

1. URI는 리소스를 표현하는데 집중할 것
2. 행위에 대한 정의는 HTTP 요청 메서드를 통해 하는 것

## URI는 리소스를 표현해야 한다.

### URI는 리소스를 표현하는데 중점을 두어야 한다.

- 리소스를 식별할 수 있는 이름은 동사보다는 명사를 사용한다.
- 따라서 이름에 get 같은 행위에 대한 표현이 들어가서는 안된다.

```json
# Bad
GET /getTodos1
GET /todosshow/1

# good
GET /todos/1
```

## 리소스에 대한 행위는 HTTP 요청 메서드로 표현한다.

주로 5가지 요청 메서드를 사용하여 CRUD를 구현한다.

- 리소스에 대한 행위는 HTTP 요청 메서드를 통해 표현하며, URI에 표현하지 않는다.

| HTTP 요청 메서드 | 종류           | 목적                  | 페이로드 |
| ---------------- | -------------- | --------------------- | -------- |
| GET              | index/retrieve | 모든/특정 리소스 취득 | X        |
| POST             | create         | 리소스 생성           | O        |
| PUT              | replace        | 리소스의 전체 교체    | O        |
| PATCH            | modify         | 리소스의 일부 수정    | O        |
| DELETE           | delete         | 모든/특정 리소스 삭제 | X        |

# JSON Server을 이용한 REST API 실습

HTTP 요청을 전송하고 응답을 받으려면 서버가 필요하다. JSON Server를 사용해 가상 REST API 서버를 구축하여 HTTP 요청을 전송하고 응답을 받는 실습을 진행해보자.

### 1. JSON Server 설치

npm을 사용하여 JSON Server을 설치한다.

### 2. db.json 파일 생성

프로젝트 루트 폴더(/json-server-exam)에 다음과 같이 db.json 파일을 생성한다.

- db.json 파일은 리소스를 제공하는 데이터베이스 역할을 한다.

### 3. JSON Server 실행

- 터미널에서 명령어를 입력하여 JSON Server를 실행한다.
- JSON Server가 데이터베이스 역할을 하는 db.json 파일의 변경을 감지하게 하려면 watch 옵션을 추가한다.

```json
## 기본 포트(3000) 사용 / watch 옵션 적용
$ json-server --watch db.json(--port 5000_포트 변경 시 추가)
```

포트를 변경하려면 port 옵션을 추가하면 된다.

⇒ 위와 같이 매번 명령어를 입력하는 것이 번거로우니, package.json 파일의 scripts를 다음과 같이 수정하여 JSON Server를 실행할 수 있다.

```json
{
  "name": "json-server-exam",
  "version": "1.0.0",
  "script": {
    "start": "json-server --watch db.json"
  },
  "devDependencies": {
    "json-server": "^0.16.1"
  }
}
```

- npm start 명령어로 JSON Server를 실행한다.

### GET 요청

todos 리소스에서 모든 todo를 취득(index) 한다.

### POST 요청

todos 리소스에 새로운 todo를 생성한다.

- POST 요청 시에는 setRequestHeader 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 한다.
- 페이로드가 객체인 경우 JSON.stringify로 변환을 한 후 전송해야 한다.

### PUT 요청

특정 리소스 전체를 교체할 때 사용한다.

- PUT 요청 시에는 setRequestHeader 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해줘야 한다.

### PATCH 요청

특정 리소스의 일부를 수정할 때 사용한다.

- setRequestHeader 메서드를 사용하여 요청 몸체에 담아 서버로 전송할 페이로드의 MIME 타입을 지정해야 한다.

### DELETE 요청

id를 사용하여 특정 데이터를 삭제한다.
