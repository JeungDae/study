# antd 사용해 SNS 화면 만들기
> antd 와 styled-components 설치   
> antd : 디자인을 위해서 사용   
> styled-components : 스타일 넣기위해 사용
```
npm i antd styled-components @ant-design/icons
```
> 공식문서를 활용하여 디자인을 변경.   
> https://ant.design/

+ 공통적으로 모든 페이지에 적용할 때는 "_app.js" 를 수정

+ 리렌더링
> style에 객체를 넣어서 style을 직접 입히면 페이지가 리렌더링됨.   
> style의 객체는 변경되지 않는데 리렌더링을 하면 쓸데없이 자원을 소모함.   
> 리렌더링을 막기 위해서는 "useMemo"로 선언하여 style을 적용하거나 styled component로 선언하여 적용.

+ noopener, noreferrer
> noopener : target="_blank"로 새로운 창을 열때 noopener 속성을 사용하지 않으면, 이전 객체가 같이 넘어가 이전 페이지에 대한 권한을 가지고 있을 수 있음.   
> noreferrer : 링크를 클릭했을 때 참조자 정보를 숨김. noreferrer를 사용하지 않으면 새로열린 창에서 객체를 조작할 수 있음.   

+ hooks
> 반복되는 부분은 커스텀훅으로 처리함.   

## SourceCode
+ _app.js
```javascript
//공통으로 적용하고 싶을 때 
import React from "react";
import propTypes from 'prop-types';
import Head from 'next/head';
import 'antd/dist/antd.css';

const NodeBird = ({ Component }) =>{
    return (
        <>
            <Head>
                <meta charSet = "utf-8"/>
                <title>NodeBird</title>
            </Head>
            <Component />
        </>
    );
};

NodeBird.propTypes = {
    Component : propTypes.elementType.isRequired,
}

export default NodeBird;
```
+ index.js
```
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
+ useInput.js
```javascript
import { useState, useCallback } from 'react';


export default (initialValue = null)=> {
    const [value, setValue] = useState(initialValue);
    const handler = useCallback((e)=>{
        setValue(e.target.value);
    },[]);

    return [value, handler];
}
```
+ AppLayout.js
```javascript
import React, {useState , useMemo} from 'react';
import propTypes from 'prop-types';
import Link from 'next/link';
import { Menu, Input, Row, Col  } from 'antd';
import styled from 'styled-components';

import UserProfile from '../components/UserProfile';
import LoginForm from '../components/Loginform';

const AppLayout = ({children})=> {
    const [isLoggedIn, setIsLoggedIn] = useState(false);

    const style = useMemo(()=>({verticalAlign:'middle'}), []);

    return (
        <div>
            <Menu mode="horizontal">
                <Menu.Item>
                    <Link href='/'><a>노드버드</a></Link>
                </Menu.Item>
                <Menu.Item>
                    <Link href='/profile'><a>프로필</a></Link>
                </Menu.Item>
                <Menu.Item>
                    <Input.Search style={style} enterButton/>
                </Menu.Item>
                <Menu.Item>
                    <Link href='/signup'><a>회원가입</a></Link>
                </Menu.Item>
            </Menu>
            <Row gutter={8}>
                <Col xs={24} md={6}>
                    {isLoggedIn ? <UserProfile setIsLoggedIn={setIsLoggedIn}/> : <LoginForm setIsLoggedIn={setIsLoggedIn}/>}
                </Col>
                <Col xs={24} md={12}>
                    {children}   
                </Col>
                <Col xs={24} md={6}>
                    <a herf="https://www.naver.com" target="_blank" rel="noreferrer noopener">Made by Hyeongjun</a>
                </Col>
            </Row>
        </div>
    )
};

AppLayout.propTypes = {
    children: propTypes.node.isRequired,
};

export default AppLayout;
```
+ profile.js
```javascript
import React from 'react';
import Head from 'next/head';

import AppLayout from '../components/AppLayout';
import NicknameEditForm from '../components/NicknameEditForm';
import FollowList from '../components/FollowerList';

const Profile = () => {
    const followerList = [{ nickname : '주형준'}, { nickname : '바보'}, { nickname : '노드버드오피셜'}];
    const followingList = [{ nickname : '주형준'}, { nickname : '바보'}, { nickname : '노드버드오피셜'}];
    return (
        <>
            <Head>
                <title>내 프로필 | NodeBird</title>
            </Head>
            <AppLayout>
                <NicknameEditForm/>
                <FollowList header="팔로잉 목록" data ={followingList}/>
                <FollowList header="팔로워 목록" data = {followerList}/>
            </AppLayout>
        </>
    );
};

export default Profile;
```
+ signup.js
```javascript
import React, { useCallback, useState } from 'react';
import Head from 'next/head';
import { Form, Input, Checkbox, Button } from 'antd';
import styled from 'styled-components';

import AppLayout from '../components/AppLayout';
import useInput from '../hooks/useInput';

const ErrorMessage = styled.div`
    color: red;
`;

const Signup = () => {
    const [id, onChangeId] = useInput('');
    const [nickname, onChangeNickname] = useInput('');
    const [password, onChangePassword] = useInput('');
    const [passwordCheck, setPasswordCheck] = useState('');

    const [passwordError, setPasswordError] = useState(false);
    const onChangePasswordCheck = useCallback((e)=>{
        setPasswordCheck(e.target.value);
        setPasswordError(e.target.value !== password);
    }, [password]);

    const [term, setTerm] = useState('');
    const [termError, setTermError] = useState(false);
    const onChangeTerm = useCallback((e) => {
        setTerm(e.target.checked);
        setTermError(false);
    }, []);

    const onSubmit = useCallback(()=>{
        if (password !== passwordCheck){
            return setPasswordCheck(true);
        }
        if (!term){
            return setTermError(true);
        }
        console.log(id, nickname, password);
    }, [password,passwordCheck,term]);
    return (
        <AppLayout>
            <Head>
                <title>회원가입 | NodeBird</title>
            </Head>
            <Form onFinish={onSubmit}>
                <div>
                    <label htmlFor="user-id">아이디</label>
                    <br />
                    <Input name="user-id" value={id} required onChange={onChangeId} />
                </div>
                <div>
                    <label htmlFor="user-nickname">닉네임</label>
                    <br />
                    <Input name="user-nickname" value={nickname} required onChange={onChangeNickname} />
                </div>
                <div>
                    <label htmlFor="user-password">비밀번호</label>
                    <br />
                    <Input name="user-password" type="password" value={password} required onChange={onChangePassword} />
                </div>
                <div>
                    <label htmlFor="user-password-check">비밀번호체크</label>
                    <br />
                    <Input name="user-password-check"
                    type="password"
                    value={passwordCheck} 
                    required 
                    onChange={onChangePasswordCheck} />
                    {passwordError && <ErrorMessage> 비밀번호가 일치하지 않습니다.</ErrorMessage>}
                </div>
                <div>
                    <Checkbox name="user-term" checked={term} onChange={onChangeTerm}>형준님 말을 잘 들을 것을 동의합니다.</Checkbox>
                    {termError && <ErrorMessage>약관에 동의합니다.</ErrorMessage>}
                </div>
                <div style={{marginTop:10}}>
                    <Button type="primary" htmlType="submit">가입하기</Button>
                </div>
            </Form>
        </AppLayout>
    );
};

export default Signup;
```

+ Loginform.js
```javascriptimport React, { useState, useCallback, useMemo } from 'react';
import { Form, Input, Button } from 'antd';
import Link from 'next/link';
import styled from 'styled-components';
import PropTypes from 'prop-types';
import useInput from '../hooks/useInput';

const ButtonWrapper = styled.div`
    margin-top : 10px;
`;

const FormWrapper = styled(Form)`
    padding : 10px;
`;

const LoginForm = ({ setIsLoggedIn }) =>{
    const [id, onChangeId] = useInput('');
    const [password, onChangePassword] = useInput('');
    
    const style = useMemo(()=> ({marginTop:10}), []);

    const onSubmitForm = useCallback(() => {
        console.log(id, password);
        setIsLoggedIn(true);
    }, [id, password]);
    
    return (
        <FormWrapper onFinish={onSubmitForm}>
            <div>
                <label htmlfor="user-id">아이디</label>
                <br/>
                <Input name="user-id" 
                    value={id} 
                    onChange ={onChangeId} 
                    required
                />
            </div>
            <div>
            <label htmlfor="user-password">비밀번호</label>
                <br/>
                <Input name="user-password" 
                    value={password} 
                    onChange ={onChangePassword} 
                    required
                />
            </div>
            <ButtonWrapper style={style}>
                <Button type="primary" htmlType="submit" loading={false}>로그인</Button>
                <Link href="/signup"><a><Button>회원가입</Button></a></Link>
            </ButtonWrapper>
            <div>

            </div>
        </FormWrapper>
    );
};

LoginForm.proTypes = {
    setIsLoggedIn : PropTypes.func.isRequired,
}
export default LoginForm;
```
+ UserProfile.js
```javascript
import React, { useCallback } from 'react';
import { Card, Avatar, Button } from 'antd';

const UserProfile= ({setIsLoggedIn}) =>{
    const onLogOut = useCallback(()=>{
        setIsLoggedIn(false);
    }, []);
    return(
        <Card
            actions={[
                <div key="twit">짹짹<br/>0</div>,
                <div key="followings">팔로잉<br/>0</div>,
                <div key="followings">팔로워<br/>0</div>
            ]}>
            <Card.Meta
                avatar = {<Avatar>HJ</Avatar>}
                title = "HyeongKun"
            />
            <button onClick={onLogOut}>로그아웃</button>
        </Card>
    );
};

export default UserProfile;
```
+ NicknameEditForm.js
```javascript
import React, { useMemo } from "react";
import { Form, Input } from 'antd';

const NicknameEditForm = () =>{
    const style = useMemo(() => ({marginBottom : '20px', border : '1px solid #d9d9d9', padding: '20px'}), []);

    return (
        <Form style = {style}>
            <Input.Search addonBefore="닉네임" enterButton="수정"/>
        </Form>
    );
};

export default NicknameEditForm;
```
+ FollowerList.js
```javascript
import React from 'react';
import PropTypes from 'prop-types';
import { List, Card, Button } from 'antd';
import { StopOutlined } from '@ant-design/icons';

const FollowList = ({ header, data }) =>{
    return (
        <List
            style = {{ marginBottom: 20 }}
            grid = {{ gutter: 4, xs: 2, md: 3 }}
            size = "small"
            header= {<div>{header}</div>}
            loadMore = {<div style= {{ textAlign:'center',margin:'10px 0' }}><Button>더 보기 </Button></div>}
            bordered
            dataSource={data}
            renderItem={(item)=>(
                <List.Item style ={{marginTop:20}}>
                    <Card actions={[<StopOutlined key="stop"/>]}>
                        <Card.Meta description={item.nickname}/>
                    </Card>
                </List.Item>
            )}
        />
    );
};

FollowList.propTypes = {
    header: PropTypes.string.isRequired,
    data: PropTypes.array.isRequired,
};

export default FollowList;
```
