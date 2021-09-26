# NodeJS기초
***
1. V8엔진
>자바스크립트는 브라우저에서 동작하는 언어이나 V8엔진이 나오면서 nodejs가 나오면서 브라우저 밖에서 실행 가능.    
>V8엔진은 자바스크립트를 해석해 주는 엔진.
***
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
***
3. 모듈 시스템
+ 브라우저에서는 모듈시스템을 구현하기 위해서 윈도우 컨텍스트를 사용함.
  + 브라우저에서 전역객체는 window 이고 window를 이용하여 모듈시스템을 구현했었음.
  + window 객체를 이용하여 모듈을 추가하고 호출하였음
+ 브라우저는 RequireJS 같은 라이브러리의 도움을 받아 모듈을 구현함. 
+ nodejs는 CommonJS로 모듈을 관리함.
  + CommonJs는 파일 형태로 파일을 관리함. nodejs는 서버에서 돌아가기 때문에 파일을 읽을 수 있음. 파일 하나하나를 모듈로 만들어서 관리.
  + 기본모듈 
 
  + ![참고사진3](https://user-images.githubusercontent.com/4956783/128732222-81935da6-4210-4302-988b-db833476509e.jpg)
    + node에는 http라는 기본 모듈이 있음. 기본 모듈을 불러오기 위해서는 require이라는 함수를 사용함. http라는 모듈을 가지고 와서 http 변수에 선언을 한 것임.

  + 사용자 정의 모듈

  + ![참고사진4](https://user-images.githubusercontent.com/4956783/128732330-c8df3894-bff8-4a48-9045-584a79c475a2.jpg)
    + 모듈을 직접 만들 수도 있음. sum이라는 function을 만들고 module 이라는 키워드를 사용하여 모듈을 만듦. 

  + ![참고사진5](https://user-images.githubusercontent.com/4956783/128732424-0cb1d91a-3713-445f-b653-24f3ebf807c7.jpg)
    + 사용자 모듈 실행 예시
***
4. 비동기 세계( ReadFileSync() vs ReadFile() )
+ ![참고사진6](https://user-images.githubusercontent.com/4956783/128732447-71ae6dca-09c0-416a-a062-11ecec5c125a.jpg)    
    + 데이터 파일 생성

+ ![참고사진7](https://user-images.githubusercontent.com/4956783/128732474-149aba82-3626-45a6-b588-c63367100c14.jpg)
  + ReadFileSync()는 동기로 동작
  + 콜백함수 없이 실행

+ ![참고사진8](https://user-images.githubusercontent.com/4956783/128732502-151fd1d1-2f2f-4f0e-b2bd-3e39810996cd.jpg)
  + ReadFile()는 비동기로 동작
  + 비동기로 작동할 경우 파일을 읽을동안 다른 일을 처리하고 파일을 다 읽은 후에 데이터를 찍어줌.
  + 비동기를 처리할 때는 콜백함수를 사용.  
