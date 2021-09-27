# 테스트 주도개발(TDD)
>mocha, should, superTest
***
## Mocha
>모카(mocha)는 테스트 코드를 돌려주는 테스트 러너입니다.   
>mochajs.org 라는 홈페이지가 있습니다.(mocha 관련 된 문서를 확인할 수 있습니다.)   
>테스트 수트 : 테스트 환경으로 모카에서는 describe()으로 구현합니다.   
>테스트 케이스 : 실제 테스트를 말하며 모카에서는 it()으로 구현합니다.   
>모카 테스트 파일은 노드가 실행시킬 수 없고, 모카가 실행을 합니다.   

+ Mocha 설치
>npm i mocha --save-dev   
>-dev옵션은 "개발환경에서 필요하다"라는 의미입니다.   
>-dev 옵션을 통해서 설치를 하면 pakage.json의 devDependencies 목록이 생성됩니다.   

```javascript
{
  "name": "node-api",
  "version": "1.0.0",
  "main": "index.js",
  "dependencies": {
    "express": "4.17.1",
    "morgan": "1.10.0"
  },
  "devDependencies": {
    "mocha": "9.0.3"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node index.js"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```


+ 모카테스트 진행하기
+ utils.js
```javascript
function capitialize(str){
    return str;
}

module.exports = {
    capitialize: capitialize
}
```
+ utils.spec.js
```javascript
const utils = require('./utils');
const assert = require('assert');

describe('utils.js모듈의 capitalize() 함수는 ', ()=>{
    it('문자열의 첫번째 문자를 대문자로 변환한다', ()=>{
        const result = utils.capitalize('hello');
        assert.equal(result,'Hello');
    })
})
```

+ 실행결과 fail
> node_modules\.bin\mocha utils.spec.js 해당 명령어로 테스트를 진행합니다.   
>> utils.js모듈의 capitalize() 함수는   
>>    1) 문자열의 첫번째 문자를 대문자로 변환한다   
>>   
>>   
>>  0 passing (14ms)   
>>  1 failing   
>>   
>>   1) utils.js모듈의 capitalize() 함수는   
>>       문자열의 첫번째 문자를 대문자로 변환한다:   
>>   
>>      AssertionError [ERR_ASSERTION]: 'hello' == 'Hello'   
>>      + expected - actual   
>>   
>>      -a   
>>      +Hello   
>>   
>>      at Context.it (utils.spec.js:8:16)   

+ 실행결과 sucess
> 정상적인 값이 나오도록 수정 후 실행을 하면 다음과 같이 나옵니다.   
>>   utils.js모듈의 capitalize() 함수는   
>>    √ 문자열의 첫번째 문자를 대문자로 변환한다   
>>   
>>   
>>  1 passing (16ms)   
***
## Should
> 노드 공식 문서에서는 assert 를 테스트 코드에서는 사용하지 말고, 서드파티 라이브러리를 사용하라고 합니다.   
> 서드파티 라이브러리인 슈드(should)는 검증(assertion)라이브러리입니다.   
> 슈드를 사용하면 가독성 높은 테스트 코드를 만들 수 있습니다.(영어문장을 읽는 듯한 느낌)   
> shouldjs.github.io 라는 문서가 있습니다.(참고)

+ Should 설치
> npm i should --save-dev 명렁어로 설치할 수 있습니다.

+ Should를 이용항 코딩
```javascript
const utils = require('./utils');
const should = require('should');

describe('utils.js모듈의 capitalize() 함수는 ', ()=>{
    it('문자열의 첫번째 문자를 대문자로 변환한다', ()=>{
        const result = utils.capitialize('hello');
        result.should.be.equal(result,'Hello');
    })
})
```
+ 실행 결과
>   utils.js모듈의 capitalize() 함수는   
>    √ 문자열의 첫번째 문자를 대문자로 변환한다   

***
## 슈퍼테스트(superTest)
> mocha 와 should를 적용한 테스트는 단위(함수의 기능 테스트) 입니다.   
> superTest는 통합테스트(API의 기능 테스트) 입니다.   
> 슈퍼테스트는 익스프레스 통합 테스트용 라이브러리입니다.   
> 내부적으로 익스프레스 서버를 구동시켜 실제 요청을 보낸 뒤 결과를 검증합니다.
> https://github.com/visionmedia/supertest 페이지의 Example에서 예제를 살펴볼 수 있습니다.

+ supertest 설치
>npm i supertest --save-dev

+ 테스트 진행
+ index.js
```javascript
var express = require('express');
var app = express();

var users = [
    {id: 1, name : 'alice'},
    {id: 2, name : 'back'},
    {id: 3, name : 'chris'},
];

app.get('/users', function (req,res){
    res.json(users);
});

//마지막에 module키워드를 추가합니다.
module.exports=app;
```

+ index.spec.js
```javascript
const app = require('./index');
const request = require('supertest');

//supertest가 잘 돌아가는지 테스트만 진행합니다.
describe('CET /users', ()=>{
    //api는 비동기로 되어있기 때문에 함수의 파라미터에 done을 추가합니다.
    it('...',(done)=>{
        request(app)
            .get('/users')
            .end((err,res)=>{
                //response의 body에 접근하면 users의 값에 접근할 수 있습니다.
                console.log(res.body);
                done();
            })
    })
})

```
+ 실행 결과
> node_modules\.bin\mocha index.spec.js 해당 명렁어로 실행합니다.
>>  GET /users   
>>[ { id: 1, name: 'alice' },   
>>  { id: 2, name: 'back' },   
>>  { id: 3, name: 'chris' } ]   
>>    √ ...   
>>   
>>   
>>  1 passing (47ms)   