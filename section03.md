# 익스프레스(ExpressJS)기초

## 익스프레스(ExpressJS)소개  
```
expressjs.com   //문서를 통해서 미리 공부해보는 것도 좋은
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

+ 라우팅   

+ 요청객체 

+ 응답객체   

+ NPM을 활용하여 express, morgan 설치 (terminal)
>```
>// www.npmjs.com
>npm install express
>npm install morgan   //다른사람이 만든 미들웨어 사용하기
>```

```javascript
const express = require('express');
const morgan = require('morgan);
const app = express();


//미들웨어 만들기 세번째 파라미터 next가 있어야 다음 작업을 진행
function logger(req,res,next){
  console.log('i am loggar');
  //next를 호출해야 다음 작업을 수행합니다. 반드시 호출을 해야합니다.
  next();
}
function logger2(req,res,next){
  console.log('i am logegr2');
  next();
}

app.use(logger);
app.use(logger2);
app.use(morgan('dev'));

app.listen(3000, function(){
  console.log('Server is running);
});
```
