#NodeJS기초
1. V8엔진
>자바스크립트는 브라우저에서 동작하는 언어이나 V8엔진이 나오면서 nodejs가 나오면서 브라우저 밖에서 실행 가능.    
>V8엔진은 자바스크립트를 해석해 주는 엔진.
2. 이벤트기반의 비동기 I/O 프레임워크
+ 이벤트
  + nodejs로 만든 application을 client가 사용한다고 가정했을 경우, client가 application으로 요청을 보내면 nodejs는 해당 요청을 event로 만들어 event queue에 쌓음.
  + Event queue에 쌓여있는 요청은 Event Loop에서 하나씩 뽑아서 요청을 실행하고 해당 요청에 대한 응답값을 client 에게 보내줌
  + Evnet Loop는 Single Thread로 이루어졌으며, nodejs가 Single Thread라는 말이 Event loop를 두고 하는 말임.
+ 비동기I/O
  + Event Loop가 요청을 처리할 때 바로 응답하여 처리할 수도 있지만, 바로 처리를 못하고 시간을 많이 필요한 요청이 있는 경우가 있음(예, 디스크의 파일을 읽거나 외부 네트워크를 통하는 작업)
  + 시간이 많이 필요한 요청같은 경우에는 다른 Thread에게 요청을 위임함. 이 때 위임받는 Thread가 Non-blocking Worker라는 부분이고, 해당 Non-blocking Worker가 요청을 수행함.
  + Non-blocking Worker가 요청을 수행하는 동안 Evnet Loop는 다른 요청을 수행하고 있으며, Non-blocking Worker가 요청을 완료한 후 응답값을 Evnet queue에 담고, Evnet Loop가 해당 Evnet queue를 실행하여 응답값을 client에게 보내줌.
  + 이렇게 비동기로 처리함.
  + Evnet 기반으로 일을 처리하고, 무거운 요청을 Work Thread를 통하여 비동기로 처리하는 부분이 이벤트기반의 비동기I/O 프레임워크를 말함.
