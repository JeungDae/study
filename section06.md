# 테스트 주도개발(TDD)
>mocha, should, superTest

+ Mocha
>모카(mocha)는 테스트 코드를 돌려주는 테스트 러너입니다.   
>mochajs.org 라는 홈페이지가 있습니다.   
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