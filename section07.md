# TDD로 하는 API서버 개발

+ NPM 테스트 스크립트 추가하기
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
    "mocha": "9.0.3",
    "should": "13.2.3",
    "supertest": "6.1.5"
  },
  "scripts": {
    "test": "mocha index.spec.js",  //경로 대신 mocha로 입력해서 테스트 스크립트를 만들 수 있습니다.
    "start": "node index.js"
  },
  "author": "",
  "license": "ISC",
  "description": ""
}
```
> npm test

+ body-parser 추가
> npm i body-parser --save

+ API 테스트 만들기.

+ index.js
```javascript
var express = require('express');
var bodyParser = require('body-parser');
var app = express();

var users = [
    {id: 1, name : 'alice'},
    {id: 2, name : 'back'},
    {id: 3, name : 'chris'},
];
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended:true}));
app.get('/users', function (req,res){
    req.query.limit = req.query.limit || 10;
    const limit = parseInt(req.query.limit, 10);    //문자열이 들어왔을경우 그대로 들어갑니다.
    //숫자인지 아닌지 체크합니다.
    if(Number.isNaN(limit)){
        return res.status(400).end();
    }
    res.json(users.slice(0, limit));    // limit 값만큼만 반환합니다.
});

app.get('/users/:id', function(req,res) {
    const id = parseInt(req.params.id,10);
    //숫자인지 아닌지 체크합니다.
    if(Number.isNaN(id)) return res.status(400).end();
    const user = users.filter((user)=>    user.id === id)[0];   //해당 숫자의 값을 반환합니다.
    //해당 숫자의 유저가 있는지 없는지 체크합니다.
    if(!user) return res.status(404).end();
    res.json(user);
});
//콜백함수를 arrow function을 사용해도 무방합니다.
app.delete('/users/:id', (req,res)=>{
    const id = parseInt(req.params.id, 10);
    //숫자인지 아닌지 체크합니다.
    if(Number.isNaN(id)) return res.status(400).end();

    // id를 제외하고 users를 만들어줍니다.
    users = users.filter(user=>user.id !== id);
    
    res.status(204).end();
})

app.post('/users', (req,res)=>{
    const name = req.body.name;
    // 이름이 있는지 없는지 체크합니다.
    if(!name) return res.status(400).end();

    const isConflict = users.filter(user=>user.name === name).length
    //중복이 있는지 체크합니다.
    if(isConflict) return res.status(409).end();
    
    const id = Date.now();
    const user ={id, name};
    users.push(user);
    res.status(201).json(user);
})

app.put('/users/:id',(req,res)=>{
    const id = parseInt(req.params.id, 10);
    //숫자인지 체크합니다.
    if(Number.isNaN(id)) return res.status(400).end();
    
    const name = req.body.name;
    // 이름이 있는지 체크합니다.
    if(!name) return res.status(400).end();

    const isConflict = users.filter(user=>user.name === name).length;
    // 중복인지 체크합니다.
    if(isConflict) return res.status(409).end();

    const user = users.filter(user=>user.id === id)[0];
    //없는 유저인지 확인합니다.
    if(!user) return res.status(404).end();
    
    user.name = name;
    
    res.json(user);
})
module.exports=app;
```

+ index.spec.js
```javascript
const request = require('supertest');
const should = require('should');
const app = require('./index');

//API Test를 만들겠습니다.
describe('GET /users는', ()=>{
    describe('성공시', ()=>{
        it('유저 객체를 담은 배열로 응답한다', (done)=>{
            request(app)
                .get('/users')
                .end((err,res) =>{
                    res.body.should.be.instanceOf(Array);
                    done();
                });
        })
        it('최대 limit 갯수만큼 응답한다', (done)=>{
            request(app)
                .get('/users?limit=2')
                .end((err,res)=>{
                    res.body.should.be.lengthOf(2)
                    done();
                });
        })
    })
    describe('실패시',()=>{
        it('limit이 숫자형이 아니면 400을 응답한다',(done)=>{
            request(app)
                .get('/users?limit=two')
                .expect(400) //검증하는 상태코드
                .end(done);
        })
    })
})

describe('GET /users/:id는', ()=>{
    describe('성공시', ()=>{
        it('id가 1인 유저 객체를 반환한다', (done)=>{
            request(app)
                .get('/users/1')
                .end((err,res)=>{
                    res.body.should.have.property('id',1);
                    done();
                });
        })
    })
    describe('실패시', ()=>{
        it('id가 숫자가 아닐경우 400으로 응답한다',(done)=>{
            request(app)
                .get('/users/one')
                .expect(400)
                .end(done);
        });
        it('id로 유저를 찾을 수 없을 경우 400을 응답한다', (done)=>{
            request(app)
                .get('/users/999')
                .expect(404)
                .end(done);
        });
    })
})

describe('DELETE /users/:id', ()=>{
    describe('성공시', ()=>{
        it('204를 응답한다', (done)=>{
            request(app)
                .delete('/users/1')
                .expect(204)
                .end(done);
        })
    })
    describe('실패시', ()=>{
        it('id가 숫자가 아닐경우 400으로 응답한다', (done)=>{
            request(app)
                .delete('/users/one')
                .expect(400)
                .end(done);
        })
    })
})

describe('POST /users', ()=>{
    describe('성공시', ()=>{
        let name = 'daniel', 
            body;
        before(done=>{
            request(app)
            .post('/users')
            .send({name})
            .expect(201)
            .end((err,res)=>{
                body=res.body;
                done();
            });
        });
        it('생성된 유저 객체를 반환한다', ()=>{
           body.should.have.property('id');
           
        })
        it('입력한 name을 반환한다', ()=>{
            body.should.have.property('name',name)
        })
    })
    describe('실패시', ()=>{
        it('name 파라미터 누락시 400을 반환한다', (done)=>{
            request(app)
                .post('/users')
                .send({})
                .expect(400)
                .end(done);
        })
        it("name이 중복일 경우 409를 반환한다", done=>{
            request(app)
                .post('/users')
                .send({name: 'daniel'})
                .expect(409)
                .end(done);
        })
    })
})

describe('PUT /users/:id', ()=>{
    describe('성공시', ()=>{
        it('변경된 name을 응답한다', (done)=>{
            const name = 'chally';
            request(app)
                .put('/users/3')
                .send({name})
                .end((err,res)=>{
                    res.body.should.have.property('name',name);
                    done();
                })
        })
    })
    describe('실패시',()=>{
        it('정수가 아닌 id일 경우 400을 응답한다', done=>{
            request(app)
                .put('/users/one')
                .expect(400)
                .end(done);
            })
        it('name이 없을 경우 400을 응답한다.', done=>{
            request(app)
                .put('/users/1')
                .expect(400)
                .end(done);
            })
        it('없는 유저일 경우 404를 응답한다', done=>{
            request(app)
                .put('/users/999')
                .send({name:'foo'})
                .expect(404)
                .end(done);
            })
        it('이름이 중복일 경우 409를 응답한다', done=>{
            request(app)
                .put('/users/3')
                .send({name : 'back'})
                .expect(409)
                .end(done);
            })        
    })
})
```
