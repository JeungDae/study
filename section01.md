#NodeJS기초
1. V8엔진
>자바스크립트는 브라우저에서 동작하는 언어이나 V8엔진이 나오면서 nodejs가 나오면서 브라우저 밖에서 실행 가능.    
>V8엔진은 자바스크립트를 해석해 주는 엔진.
2. 이벤트기반의 비동기 I/O 프레임워크
+ 이벤트
  + nodejs로 만든 application을 client가 사용한다고 가정했을 경우, client가 application으로 요청을 보내면 nodejs는 해당 요청을 event로 만들어 event queue에 쌓음.
  + Event queue에 쌓여있는 요청은 Event Loop에서 하나씩 뽑아서 요청을 
