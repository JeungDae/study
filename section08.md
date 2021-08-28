# 라우터 클래스
>index.js 파일을 분리 
***
- 파일구조
api   
> bin   
> >www.js
>   
> user   
> > index.js   
> > user.ctrl.js   
> > user.spec.js
   
+ api/user/index.js
```javascript
// 라우팅 설정
const express = require('express');
const router = express.Router();
const ctrl = require('./user.ctrl');

router.get('/', ctrl.index);
router.get('/:id', ctrl.show);
router.delete('/:id', ctrl.destroy);
router.post('/', ctrl.create);
router.put('/:id', ctrl.update);

module.exports = router;
```

+ api/user/user.ctrl.js
```javascript
// 설정한 라우팅이 실제로 작동하는 부분
var users = [
    {id: 1, name : 'alice'},
    {id: 2, name : 'back'},
    {id: 3, name : 'chris'},
];

const index = function (req,res){
    req.query.limit = req.query.limit || 10;
    const limit = parseInt(req.query.limit, 10);
    if(Number.isNaN(limit)){
        return res.status(400).end();
    }
    res.json(users.slice(0, limit));
}

const show = function(req,res) {
    const id = parseInt(req.params.id,10);
    if(Number.isNaN(id)) return res.status(400).end();
    const user = users.filter((user)=>    user.id === id)[0];   
    if(!user) return res.status(404).end();
    res.json(user);
}

const destroy = (req,res)=>{
    const id = parseInt(req.params.id, 10);
    if(Number.isNaN(id)) return res.status(400).end();

    users = users.filter(user=>user.id !== id);
    
    res.status(204).end();
}

const create = (req,res)=>{
    const name = req.body.name;
    if(!name) return res.status(400).end();

    const isConflict = users.filter(user=>user.name === name).length

    if(isConflict) return res.status(409).end();
    
    const id = Date.now();
    const user ={id, name};
    users.push(user);
    res.status(201).json(user);
}

const update = (req,res)=>{
    const id = parseInt(req.params.id, 10);

    if(Number.isNaN(id)) return res.status(400).end();
    
    const name = req.body.name;

    if(!name) return res.status(400).end();

    const isConflict = users.filter(user=>user.name === name).length;
    
    if(isConflict) return res.status(409).end();

    const user = users.filter(user=>user.id === id)[0];

    if(!user) return res.status(404).end();
    
    user.name = name;
    
    res.json(user);
}

module.exports = {
    index,
    show,
    destroy,
    create,
    update
}
```

+ api/user/user.spec.js
```javascript
//테스트
const request = require('supertest');
const should = require('should');
const app = require('../../index');

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
                .expect(400) 
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

+ api/bin/www.js
```javascript
const app = require('../../index');

app.listen(3000,()=>{
    console.log('Server is running on 3000 port')
});
```

기존 index.js 를 분할함.
