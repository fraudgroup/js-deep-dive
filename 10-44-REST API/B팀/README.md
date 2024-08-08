# 44장 REST API

## 목차

1. [REST API의 구성](#1-rest-api의-구성)
2. [REST API 설계 원칙](#2-rest-api-설계-원칙)

<br />

## 들어가며

REST는 HTTP를 기반으로 클라이언트가 서버의 리소스에 접근하는 방식을 규정한 아키텍처고, REST API는 REST를 기반으로 서비스 API를 구현한 것을 의미한다.

REST의 기본 원칙을 성실히 지킨 서비스 디자인을 RESTful이라고 표현한다.

<br />

## 1. REST API의 구성

REST는 자체 표현 구조로 구성되어 REST API만으로 HTTP 요청의 내용을 이해할 수 있다.

REST API를 구성하는 3가지 요소.

|       구성 요소       |              내용              |    표현 방법     |
| :-------------------: | :----------------------------: | :--------------: |
|    자원(resource)     |              자원              | URI(엔드포인트)  |
|      행위(verb)       |        자원에 대한 행위        | HTTP 요청 메서드 |
| 표현(representations) | 자원에 대한 행위의 구체적 내용 |     페이로드     |

<br />

## 2. REST API 설계 원칙

REST에서 가장 중요한 기본적인 원칙은 두 가지다.

- URI는 리소스를 표현해야 한다.
- 행위에 대한 정의는 HTTP 요청 메서드로 표현한다.

<br />

**1. URI는 리소스를 표현해야 한다.**

URL는 리소스를 표현하는 데 중점을 두어야 한다. 리소스를 식별할 수 있는 이름은 동사보다는 명사를 사용한다.

```bash
# bad
GET /getTodos/1
GET /todos/show/1

# good
GET /todos/1
```

<br />

**2. 행위에 대한 정의는 HTTP 요청 메서드로 표현한다.**

HTTP 요청 메서드는 클라이언트가 서버에게 요청의 종류와 목적을 알리는 방법이다.

주로 5가지 요청 메서드(GET, POST, PUT, PATCH, DELETE 등)를 사용하여 CRUD를 구현한다.

| HTTP 요청 메서드 |      종류      |         목적          | 페이로드 |
| :--------------: | :------------: | :-------------------: | :------: |
|       GET        | index/retrieve | 모든/특정 리소스 취득 |    ❌    |
|       POST       |     create     |      리소스 생성      |    ✅    |
|       PUT        |    replace     |  리소스의 전체 교체   |    ✅    |
|      PATCH       |     modify     |  리소스의 일부 수정   |    ✅    |
|      DELETE      |     delete     | 모든/특정 리소스 삭제 |    ❌    |

리소스에 대한 행위는 HTTP 요청 메서드를 통해 표현하며 URI에 표현하지 않는다.

```bash
# bad
GET /todos/delete/1

# good
DELETE /todos/1
```
