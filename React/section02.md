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
> 페이지마다 공통적으로 컴포넌트들이 있음.   
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

## SourceCode
+ /store/configureStore.js
```javascript
import { createWrapper } from 'next-redux-wrapper';
import { applyMiddleware, compose, createStore } from 'redux';
import { composeWithDevTools } from 'redux-devtools-extension';

import reducer from '../reducers';

const configureStore = () =>{
    const middlewares = [];
    const enhancer = process.env.NODE_ENV === 'production' ? compose(applyMiddleware(...middlewares)):composeWithDevTools(applyMiddleware(...middlewares))  //개발때는 devTools를 연동
    const store= createStore(reducer, enhancer); // enhancer를 추가하여 미들웨어를 설정 
    return store;
};

const wrapper = createWrapper(configureStore, {debug : process.env.NODE_ENV === 'development'});

export default wrapper;
```

+ /reducer/index.js
```javascript
import { HYDRATE } from 'next-redux-wrapper';  //HYDRATE 서버에서 생성한 스토어를 클라이언트에 합쳐주는 작업
import { combineReducers } from 'redux';    //리듀서 합치기 위한 기능 
//리듀스 쪼갠걸 가지고오기 
import user from './user';
import post from './post';

// (이전 상태, 액션) => 다음상태를 만듦.
const rootReducer = combineReducers({
    index : (state = {}, action) =>{
        switch (action.type){
            case HYDRATE: 
                console.log('HYDRATE', action);
                return {...state, ...action.payload};
            default:
                return state;
        }
    },
    user,
    post,
});

export default rootReducer;
```

+ /reducer/user.js
```javascript
//저장소
export const initialState= {
    isLoggedIn : false,
    user : null,
    signUpData : {},
    loginData : {},
}

//액션
export const loginAction = (data) =>{
    return {
        type: 'LOG_IN',
        data,
    }
}

export const logoutAction = () =>{
    return {
        type: 'LOG_OUT',
    }
}

const reducer = (state =initialState,action) =>{
    switch (action.type){
        case 'LOG_IN':
            return {
                ...state,
                isLoggedIn:true,
                user: action.data        
            };
        case 'LOG_OUT':
            return {
                ...state,
                isLoggedIn: false,
                user: null,
            };
        default:
            return state;
    }
};

export default reducer;
```

+ /reducer/post.js
```javascript
//가짜데이터
export const initialState= {
    mainPosts : [{
        id : 1,
        User: {
            id: 1,
            nickname : '주형준',
        },
        content : '첫 번째 게시글 #해시태그 #아이유',
        Images: [{
            src: 'https://blog.kakaocdn.net/dn/2nWai/btqWTXUwlWw/s1o3MnOMUgKqnKBED3mfsK/img.jpg',
        },{
            src: 'https://img1.daumcdn.net/thumb/R720x0.q80/?scode=mtistory2&fname=http%3A%2F%2Fcfile1.uf.tistory.com%2Fimage%2F99962D385EF4533135A720',
        },{
            src: 'https://mblogthumb-phinf.pstatic.net/MjAxOTA4MDdfNTYg/MDAxNTY1MTQ1NTE2Mzgx.DwYw1KMvvdGAcTSAjX7Dp7JQWFWGizsjKPaq517kO2sg.wIzay3Lt-ceJq99fNR3cZ-TLzO_D7Fyb_Ew-Y4ZOSiMg.JPEG.pola0216/%EC%95%84%EC%9D%B4%EC%9C%A0%EC%97%AC%EC%B9%9C%EC%A7%A412.jpg?type=w800',
        }],
        Comments: [{
            User: {
                nickname: 'nero',
            },
            contnet: '우와 이뻐요',
        }, {
            User: {
                nickname: 'baby',
            },
            contnet: '역시... 아이유!',
        }]
    }],
    imagePaths:[],  
    postAdded: false,
}

const ADD_POST = 'ADD_POST';
export const addPost ={
    type: ADD_POST,
}
// 더미데이터
const dummyPost = {
    id: 2,
    content : '더미데이터입니다.',
    User: {
        id:1,
        nickname:'주형준',
    },
    Images:[],
    Comments:[],
};

const reducer = (state =initialState,action) =>{
    switch (action.type){
        case ADD_POST:
            return{
                ...state,
                mainPosts: [dummyPost, ...state.mainPosts],
                PostAdded : true,
            }
        default:
            return state;
    }
};

export default reducer; 
```

+ /pages/index.js
```javascript
import React from 'react';
import { useSelector } from 'react-redux';
import AppLayout from '../components/AppLayout';
import PostForm from '../components/PostForm';
import PostCard from '../components/PostCard';
const Home = () =>{
    const { isLoggedIn } = useSelector((state)=>state.user);
    const { mainPosts } = useSelector((state)=>state.post);
    return (
        <AppLayout>
            { isLoggedIn && <PostForm /> }
            { mainPosts.map((post) => <PostCard key={post.id} post={post}/>)}
        </AppLayout>
    )
}

export default Home;
```

+ /components/PostFrom.js
```javascript
import React, { useCallback, useState, useRef } from 'react';
import { Form, Input, Button } from 'antd';
import { useSelector, useDispatch } from 'react-redux';
import { addPost } from '../reducers/post';
const PostForm =() =>{
    const { imagePaths } = useSelector((state)=>state.post);
    const imageInput = useRef(); // 실제 도메인에 접근
    const dispatch = useDispatch();
    const [text, setText] = useState('');
    const onChangeText = useCallback((e)=> {
        setText(e.target.value);
    }, []);
    const onSubmit = useCallback(()=>{
        dispatch(addPost);
        setText('');
    }, []);
    const onClickImageUpload = useCallback(()=>{
        imageInput.current.click();
    }, [imageInput.current]);
    return (
        <Form style={{margin:'10px 0 20px '}} encType="multipart/form-data" onFinish={onSubmit}>
            <Input.TextArea 
            value={text} 
            onChange={onChangeText} 
            maxLenth={140} 
            placeholder="어떤 신기한 일이 있었나요?"
            />
            <div>
                <input type="file" multiple hidden ref={imageInput}></input>
                <Button onClick={onClickImageUpload}>이미지 업로드</Button>
                <Button type="primary" style={{ float :'right'}} htmlType="submit">짹짹</Button>
            </div>
            <div>
                {imagePaths.map((v) => (
                    <div key={v} style={{display:'inline-block'}}>
                        <img src={v} style={{width:'200px'}} alt={v}/>
                        <div>
                            <Button>제거</Button>
                        </div>
                    </div>
                ))}
            </div>
        </Form>
    );
};

export default PostForm;
```
