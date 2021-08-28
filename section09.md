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

