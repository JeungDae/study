# Hello, Next.js
>  코드스플리팅. 서버사이드렌더링을 사용   
>  프로젝트 생성
```
npm init 
```
> nextjs 설치(@는 버전을 지정할 수 있음)
```
npm i next@9
```
> react 와 react-dom 설치
```
npm i react react-dom
```
> prop-types 설치
```
npm i prop-types
```
+ Next는 라우팅을 알아서 잡아줌
> pages 폴더에 signup과 profile js파일을 생성하고,   
> 서버를 구동후 경로에 해당 페이지의 이름을 적어주면 알아서 페이지를 띄워줌.   
> 넥스트가 라우터 설정을 알아서 해줌.   
> 경로는 /파일이름 또는 /폴더/파일이름 형식임.
+ 예제코드
+ index.js, profile.js, signup.js , AppLayout.js
```javascript
// pages/index.js
import React from 'react';
import AppLayout from '../components/AppLayout';

const Home = () =>{
    return (
        <AppLayout>
            <div>Hello, Next!</div>
        </AppLayout>
    )
}
export default Home;
```
> 해당코드는 npm run dev 로 실행했을 경우에 index 페이지

```javascript
/// pages/profile.js
import React from 'react';
import AppLayout from '../components/AppLayout';

const Profile = () => {
    return <AppLayout>내 프로필</AppLayout>
};

export default Profile;
```
> localhost:3000/profile 경로의 소스

```javascript
import React from 'react';
import AppLayout from '../components/AppLayout';

const Signup = () => {
    return <AppLayout>회원가입 페이지</AppLayout>
};

export default Signup;
```
> localhost:3000/signup 경로의 소스

```javascript
// components/AppLayout.js
import React from 'react';
import propTypes from 'prop-types';
import Link from 'next/link';

const AppLayout = ({children})=> {
    return (
        <div>
            <div>
                <Link href='/'><a>노드버드</a></Link>
                <Link href='/profile'><a>프로필</a></Link>
                <Link href='/signup'><a>회원가입</a></Link>
            </div>
            {children}
        </div>
    )
};

AppLayout.propTypes = {
    children: propTypes.node.isRequired,
};

export default AppLayout;
```
> 공통 페이지

+ eslint로 코드롤 정하기
> eslint 설치-> 여러사람이 코딩을 해도 한사람이 코딩을 한 것처럼 코드롤을 정해줌
```
npm i eslint -D   // -D는 개발용으로만 쓰임
npm i eslint-plugin-import -D
npm i eslint-plugin-react-hooks -D
```
> .estlintrc 파일을 생성하여 소스코드 롤을 정할 수 있음
```javascript
{
    "parserOptions":{
        "ecmaVersion":2020,
        "sourceType": "module",
        "ecmaFeatures": {
            "jsx":true
        }
    },
    "env": {
        "browser":true,
        "node": true,
        "es6": true
    },
    "extends":[
        "eslint:recommended",
        "plugin:react/recommended"   
    ],
    "plugins": [
        "import",
        "react-hooks"
    ],
    "rules":{
        
    }
}

```
