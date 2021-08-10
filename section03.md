# 익스프레스(ExpressJS)기초

## 익스프레스(ExpressJS)소개
>expressjs.com   
>>어플리케이션   
>>> 익스프레스 인스턴스를 어플리케이션이라고 합니다.
>>> ```javascript
>>> const express=require('express');
>>> const app = express();     //app이 어플리케이션
>>> ```
>>> 서버에 필요한 기능인 미들웨어 어플리케이션에 추가한다.   
>>> 라우팅 설정을 할 수 있습니다.   
>>> 서버를 요청 대기 상태로 만들 수 있습니다.     
>> 미들웨어   
>>> 함수들의 연속입니다.   
>> 라우팅   
>> 요청객체   
>> 응답객체   

>NPM을 활용하여 express 설치
>>```
>>npm install express
>>```

```javascript
const express = require('express');
const app = express();

function logger(req,res,next){
  console.log('i am loggar');
  next();
}

app.use(logger);

app.listen(3000, function(){
  console.log('Server is running);
});
```
