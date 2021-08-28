# 데이터베이스
## 데이터베이스소개
- SQL
> 테이블 형식의 데이터베이스 (MySQL, PostgreSQL, Aurora, Sqlite)
- NoSQL
> Document 형식의 데이터베이스 -> json형식 (MongoDB, DynamoDB)
- In Memory DB
> 메모리 안에 있는 데이터베이스, 사용이유 => 서비스의 성능 향상 (인증토큰, 세션 등) (Redis, Memcashed)

## ORM소개
- 데이터베이스를 객체로 추상화해 놓은 것 ORM(Object Relational Mapping)
- 쿼리를 직접 작성하지 않고 ORM의 메소드로 데이터 관리할 수 있음.
- 노드에서 SQL ORM은 시퀄라이져(Sequelize)가 있음

## 모델정의
- 모델 : 데이터베이스 테이블을 ORM으로 추상화한것을 모델이라 함.
```javascript
//시퀄라이져랑 sqlite 설치
npm i sequelize sqlite3 --save
```
- 모델 정의 (models.js)
```javascript
const Sequelize = require('sequelize');
const sequelize = new Sequelize({
    dialect : 'sqlite',
    Storage: './db.sqlite'
});

const User = sequelize.define('User', {
    name : Sequelize.STRING //varchar 255 
});

module.exports = {Sequelize, sequelize, User};
```
- DB 연결 (bin/sync-db.js)
```javascript
const models = require('../models');

module.exports= ()=>{
    return models.sequelize.sync({force:true});
}
```

## API-디비 연동 및 테스트
- api/index.js
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
- api/user.ctrl.js
```javascript
const models = require('../../models');

const index = function (req,res){
    req.query.limit = req.query.limit || 10;
    const limit = parseInt(req.query.limit, 10);
    if(Number.isNaN(limit)){
        return res.status(400).end();
    }

    models.User
        .findAll({
            limit:limit
        })
        .then(users =>{
            res.json(users);
        });
}

const show = function(req,res) {
    const id = parseInt(req.params.id,10);
    if(Number.isNaN(id)) return res.status(400).end();
    models.User
        .findOne({
            where : {id}
        }).then(user => {
            if(!user) return res.status(404).end();
            res.json(user);
        });
}

const destroy = (req,res)=>{
    const id = parseInt(req.params.id, 10);
    if(Number.isNaN(id)) return res.status(400).end();
    
    models.User.destroy({
        where:{id}
    }).then(()=>{
        res.status(204).end();
    });
}

const create = (req,res)=>{
    const name = req.body.name;
    if(!name) return res.status(400).end();

    models.User.create({name})
        .then(user=>{
            res.status(201).json(user);
        })
        .catch(err=>{
            if(err.name ==='SequelizeUniqueConstraintError')
                return res.status(409).end();
        })
}

const update = (req,res)=>{
    const id = parseInt(req.params.id, 10);
    if(Number.isNaN(id)) return res.status(400).end();
    
    const name = req.body.name;
    if(!name) return res.status(400).end();

    models.User.findOne({where:id})
        .then(user =>{
            if(!user) return res.status(404).end();

            user.name = name;
            user.save()
                .then(()=> {
                    res.json(user);
                })
                .catch(err => {
                    if(err.name ==='SequelizeUniqueConstraintError')
                    return res.status(409).end();
                })
        }) 
}

module.exports = {
    index,
    show,
    destroy,
    create,
    update
}
```
- api/user.spec.js
```javascript
//테스트
const request = require('supertest');
const should = require('should');
const app = require('../../index');
const models = require('../../models');

describe('GET /users는', ()=>{
    const users = [{name:'alice'}, {name:'beck'},{name:'chris'}];
    before(()=>models.sequelize.sync({force:true}));
    before(()=>models.User.bulkCreate(users));
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
});

describe('GET /users/:id는', ()=>{
    const users = [{name:'alice'}, {name:'beck'},{name:'chris'}];
    before(()=>models.sequelize.sync({force:true}));
    before(()=>models.User.bulkCreate(users));
    describe('성공시', ()=>{
        it('id가 1인 유저 객체를 반환한다', (done)=>{
            request(app)
                .get('/users/1')
                .end((err,res)=>{
                    res.body.should.have.property('id',1);
                    done();
                });
        });
    });
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
});

describe('DELETE /users/:id', ()=>{
    const users = [{name:'alice'}, {name:'beck'},{name:'chris'}];
    before(()=>models.sequelize.sync({force:true}));
    before(()=>models.User.bulkCreate(users));
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
});

describe('POST /users', ()=>{
    const users = [{name:'alice'}, {name:'beck'},{name:'chris'}];
    before(()=>models.sequelize.sync({force:true}));
    before(()=>models.User.bulkCreate(users));
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
    const users = [{name:'alice'}, {name:'beck'},{name:'chris'}];
    before(()=>models.sequelize.sync({force:true}));
    before(()=>models.User.bulkCreate(users));
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
                .send({name : 'beck'})
                .expect(409)
                .end(done);
            })        
    })

})
```
