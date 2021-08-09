# Hello World
***
+ Hello World 예제파일로 진행.
  + 참고사진9
  + 참고사진10
  + 코드를 작성하여 실행. Hello World를 출력.
    + http 모듈을 가지고 와서 http에 선언, hostname과 port 를 설정. 
    + http모듈의 method 중에 createServer라는 method를 실행함 createServer의 콜백함수는 클라이언트가 접속했을 때 동작함.
    + listen은 요청 대기상태로 만드는 함수로 서버가 클라이언트의 요청을 받을 수 있게 종료하지 않고 대기하도록 하는 함수.
      + listen은 파라미터를 3개 받음 hostname, port, 마지막 파라미터는 listen이라는 함수가 구동되면 나오는 메시지
      + 메시지를 보면 템플릿 문자열(`)을 통해서 변수(${변수})가 값으로 해석되어 문자열을 찍어줌
    + listen 상태에서 해당 hostname과 port를 호출하면 creatServer의 콜백함수를 실행.
***
+ 라우팅 추가 
  + 라우팅은 요청 경로를 실행.
  + 참고 사진 11
    + 요청 경로는 요청(req)의 url 에 포함되어있음. if문으로 해당 요청값에 따라 다른 응답값을 보내줄 수 있음. 
    + 요청에 따른 응답을 if문으로 처리하기 힘듦으로 express를 사용. 
