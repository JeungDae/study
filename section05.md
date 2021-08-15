# REST API란
> REST API 는 HTTP를 사용합니다.

## 요청형식
+ GET
> GET의 경로에는 명사만 사용합니다.
> GET 은 자원을 조회할 때 사용합니다.

+ POST
> 자원을 생성할 때 사용합니다.

+ PUT
> 자원을 갱신할 때 사용합니다.(수정할 때 사용)

+ DELETE
> 자원을 삭제할 때 사용합니다.

+ GET, POST, PUT, DELETE 는 익스프레스 어플리케이션의 메소드로 구현되어 있습니다.

## HTTP 상태코드
+ 1xx : 아직 처리중
+ 2xx : 성공
> 200 : 성공(sucess), GET, PUT   
> 201 : 작성됨(created), POST   
> 204 : 내용없음(No content), DELETE   
+ 4xx : 클라이언트의 문제
> 400 : 잘못된 요청(Bad Request)   
> 401 : 권한 없음(Unauthorized)   
> 404 : 찾을 수 없음(Not found)   
> 409 : 충돌(Conflict)   
+ 5xx : 서버 문제
> 500 : 서버에러(Internal server error)

## 첫번째 API 만들기
+ GET /users 
> 사용자 목록을 조회하는 기능을 만들겠습니다.

```javascript
var express = require('express');
var app = express();
var morgan = require('morgan');
//DB연결 대신 users 라는 목록을 만들어 줍니다.
var users = [
    {id: 1, name : 'alice'},
    {id: 2, name : 'back'},
    {id: 3, name : 'chris'},
];

app.use(morgan('dev'));

//GET으로 /users라는 경로로 들어올 경우 users라고 만들어준 값을 응답합니다.
app.get('/users', function (req,res){
    res.json(users);
});

app.listen(3000, function(){
    console.log('Exapmle app listening on port 3000');
});
```
>서버를 구동합니다.   
>curl -X GET 127.0.0.1:3000/users -v 
>라는 명령어를 실행합니다.   
+ 결과값
>* Trying 127.0.0.1...   
>* TCP_NODELAY set   
>* Connected to 127.0.0.1 (127.0.0.1) port 3000 (#0)   
> GET /users HTTP/1.1   
> Host: 127.0.0.1:3000   
> User-Agent: curl/7.55.1   
> Accept: */*   
>   
> HTTP/1.1 200 OK   
> X-Powered-By: Express   
> Content-Type: application/json; charset=utf-8   
> Content-Length: 72   
> ETag: W/"48-HpJ0Bb8cZgYveumJSkz4whbAEL4"   
> Date: Sun, 15 Aug 2021 12:36:54 GMT   
> Connection: keep-alive   
>   
> [{"id":1,"name":"alice"},{"id":2,"name":"back"},{"id":3,"name":"chris"}]* Connection #0 to host 127.0.0.1 left intact
