# 테스트 주도개발(TDD)
>mocha, should, superTest

+ Mocha
>모카(mocha)는 테스트 코드를 돌려주는 테스트 러너입니다.   
>mochajs.org 라는 홈페이지가 있습니다.   
>테스트 수트 : 테스트 환경으로 모카에서는 describe()으로 구현합니다.   
>테스트 케이스 : 실제 테스트를 말하며 모카에서는 it()으로 구현합니다.

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
