# 익스프레스(ExpressJS)기초

## 익스프레스(ExpressJS)소개  
```
expressjs.com   //문서를 통해서 미리 공부해보는 것도 좋습니다.
```
+ 어플리케이션   
> 익스프레스 인스턴스를 어플리케이션이라고 합니다.
> ```javascript
> const express=require('express');
> const app = express();     //app이 어플리케이션
> ```
> 서버에 필요한 기능인 미들웨어 어플리케이션에 추가한다.   
> 라우팅 설정을 할 수 있습니다.   
> 서버를 요청 대기 상태로 만들 수 있습니다.     

+ 미들웨어   
> 함수들의 연속입니다.   
> 세번째 파라미터인 next 함수를 호출하는게 중요합니다.   
> 미들웨어는 일반 미들웨어와 에러 미들웨어가 있습니다. 
> 일반 미들웨어는 파라미터를 3개 받습니다.   
> error 미들웨어는 파라미터를 4개 받습니다.

+ 라우팅   
> 요청 url에 대한 적절한 핸들러 함수로 연결해 주는 기능을 말합니다.
> 어플리케이션의 get(), post() 메소드로 구현할 수 있습니다.
> 라우팅을 위한 전용 Router 클래스를 사용할 수도 있습니다.

+ 요청객체 
> 클라이언트의 요청정보를 담은 객체 입니다.
> http 모듈의 request 객체를 매핑한 겁니다.

+ 응답객체   
> 클라이언트 응답 정보를 담은 객체를 응답객체라고 합니다.
> http 모듈의 Response객체를 매핑한 겁니다.

+ NPM을 활용하여 express, morgan 설치 (terminal)
>```
>// www.npmjs.com
>npm install express
>npm install morgan   //다른사람이 만든 미들웨어 사용하기 (써드파티 미들웨어 사용)
>```

```javascript
//express 모듈을 가지고 와서 express 객체를 만들어 app 에 할당
const express = require('express');
const morgan = require('morgan');
const app = express();

//Routing을 설정한 부분 입니다.
//get 메소드의 '/'경로로 들어왔을 경우 'Hello World'가 나오도록 코딩하였습니다. 
app.get('/', function(req,res){
  res.send('Hello World!');
});

//미들웨어는 인터페이스가 정의되어 있습니다. (request, response, next) 
//미들웨어 만들기 세번째 파라미터 next가 있어야 다음 작업을 진행합니다.
function logger(req,res,next){
  console.log('i am loggar');
  //next를 호출해야 다음 작업을 수행합니다. 반드시 호출을 해야합니다.
  next();
}

function logger2(req,res,next){
  console.log('i am logegr2');
  next();
}

function commonmw(req,res,next){
  console.log('commonmw');
  next(new Error('error occered')); //next에 에러가 있을 경우에 발생
}

//에러 미들웨어 에러가 나기 전에 넘긴 error 객체가 err에 쌓입니다.
function errormw(err,req,res,next){
  console.log(err.message);
  //전달 받은 에러를 처리하거나 에러 처리를 못하면 다음 미들웨어에게 에러를 넘깁니다.
  //next(err);   
  next();
}

//미들웨어를 추가할 때는 use 라는 함수를 사용합니다. 
app.use(logger);
app.use(logger2);
app.use(morgan('dev'));
app.use(commonmw);
app.use(errormw);

app.listen(3000, function(){
  console.log('Server is running);
});
```
>응답값
>Server is running
>i am logger
>i am logger2
>commonmw
>error ouccered
>GET / 404 5.775 ms - 139
