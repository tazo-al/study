## REST(Path Variable vs Query Parameter)

### REST API란?

- REpresentational State Transfer의 약자로서, 어떤 자원에 대해 CRUD를 진행할 수 있게 HTTP Method(GET, POST, PUT, DELETE)를 사용하여 요청을 보내는 것으로 이 때, 요청을 위한 자원은 특정한 형태로 표현된다.
- 간단히 말하면 **URI를 통해 정보의 자원(only 자원만을)을 표현하고, 자원의 행위는 HTTP Method로 명시한다.**정도로 이해할 수 있다.
- 자원(Resource): URI
- 행위(Verb): HTTP Method
- 표현(Representations)

**GET/users/3/profile**

- 위 요청을 아무 지식이 없는 상태에서 예상해봐도 use 중에서 3번 아이디를 갖고 있는 사람의 프로필을 요청하는 것을 알 수 있다.
- 이게 REST API의 장정이라 할 수 있다.
- 규칙을 살펴보면

```
http://example.com/posts (O)
http://example.com/posts/ (X)
http://example.com/post (X)
http://example.com/get-posts (X)
```

- URI는 명사를 사용하고 소문자로 작성되어야 한다.
- 명사는 복수형을 사용한다.
- URI의 마지막에는 /를 포함하지 않는다.

```
http://example.com/post-list (O)
http://example.com/post_list (X)
```

- URI에는 언더바가 아닌 하이픈을 사용한다.

```
http://example.com/post/assets/example (O)
http://example.com/post/assets/example.png (X)
```

- URI에는 파일의 확장자를 표시하지 않는다.

#### RESTFul 하다는 것은 무엇일까?

- 위의 REST API의 까다로운 조건을 만족시킨 통신 설계 상태를 말한다.
- 누가보더라도 이해하기 쉽고, 사용하기 쉬운 REST API라면 RestFul하구나라고 할 수 있다.
- RestFul하지 못한 상황
  - CRUD의 기능을 모두 POST method로만 이용하는 경우
  - URI에 행위(method)에 대한 부분이 들어가는 경우(/classes/createPeople)

### Path Variable vs Query Parameter

- REST한 개발을 하다 보면, GET method를 이용하여 데이터를 가져올 때 등의 경우에서 Path Variable과 Query Parameter에 대한 이야기는 항상 같이 나온다.

#### Path Variable

> /users/10

- 이름에서도 유추할 수 있듯, 경로 자체에 변수(10)를 사용한 방법이다.
- 전체 데이터 또는 특정 하나의 데이터를 다룰 때처럼 리소스를 식별하기 위해 사용된다.

#### Query Parameter

> /users?user_id=10

- 데이터를 정렬하거나 필터링 하는 경우 더 적합하다.

#### 예시

```
/users # Fetch a list of users
/users?occupation=programer # Fetch a list programer user
/users/123 # Fetch a user who has id 123
```
