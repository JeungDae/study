# Redux 연동하기
+ Next Redux Wrapper
> nextreduxwrapper로 next와 redux 를 연동
```
npm i next-redux-wrapper    //버전은 6.0.2
npm i redux
npm i react-redux           //react 와 redux를 연결해줌.
npm i redux-devtools-extension    // 브라우저 개발자 도구와 연동
```
> 해당 next-redux-wrapper를 설치하면 createWrapper를 사용할 수 있음.

+ redux를 사용하는 이유
> 페이지마다 공통적인 컴포넌트들이 있음.   
> 컴포넌트들을 부모부터 자식까지 각자 만들기가 귀찮고 어려움.   
> rdeux에서는 중앙데이터저장소를 두어서 컴포넌트들을 관리함.   
> 중앙저장소의 양이 많아지면 쪼개서 관리할 수 있음.
> redux는 코드량이 많지만 에러를 추적하기 쉬움.   

+ Redux 원리
> 리덕스는 리듀스에서 이름을 따옴.   
> 데이터 저장소에서 각 컴포넌트에서 필요할 때 가져다 쓸 수 있음.   
> 액션을 통해서 저장소에 접근, 수정, 삭제를 함.   
> 중앙저장소와 액션사이에는 리듀서가 있음.   
> 리듀서에서 액션에 대한 실질적인 작업을 진행.   
> 액션을 통해서 기록을 남겨서 작업의 흐름이나 에러를 잡기 쉬움.  

+ 미들웨어
> enhancer 를 추가하여 미들웨어를 설정할 수 있음.   
> redux-devtools-extension을 설치하여 브라우저 개발툴 redux 에서 데이터가 어떻게 바뀌고 있는지 확인 가능.

