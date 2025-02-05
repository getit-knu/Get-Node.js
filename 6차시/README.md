# GET IT SW교육 Node.js
## 6차시_MySQL 설치하기, MySQL 이해하기
> 기간 : 24.11.06~24.11.10

### 🗂️6차시 자료🗂️
[Node.js 6차시 자료(MySQL 설치하기)](https://github.com/getit-knu/Get-Node.js/blob/main/6%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%206%EC%B0%A8%EC%8B%9C_%EC%84%A4%EC%B9%98.pdf)

[Node.js 6차시 자료(MySQL 이해하기)](https://github.com/getit-knu/Get-Node.js/blob/main/6%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%206%EC%B0%A8%EC%8B%9C.pdf)

👉자료를 다운 받은 후 수강해주세요!

### 🎞️6차시 강의🎞️

[![Video Label](https://i9.ytimg.com/vi/juBHnOQAGbA/mqdefault.jpg?v=67a1cfd8&sqp=CIiOjb0G&rs=AOn4CLBX9-q81RYNOuAb1sTCxzq9blm2tA)](https://youtu.be/HjHuMYfMpFQ)

[![Video Label](https://i9.ytimg.com/vi/XcvzxWWdcuY/mqdefault.jpg?v=67a22478&sqp=CIiOjb0G&rs=AOn4CLBwq2won5qo3HvRLPpkybW_ae3XAg)](https://youtu.be/XcvzxWWdcuY)

👉위 이미지를 누르면 강의를 들을 수 있습니다.

---

### 🚀목차🚀
**MySQL 설치하기**
1. MySQL 설치하기
2. 워크벤치 설치하기

**MySQL 이해하기**
1. 데이터베이스 및 테이블 생성하기
2. CRUD 작업하기
3. 시퀄라이즈 사용하기

---

### MySQL 설치하기
#### 01. MySQL 설치하기
### Windows
 - https://dev.mysql.com/downloads/installer/ 접속
    - 강의 영상 따라 설치 설정 후, 설치
 - MySQL 접속하기
    - MySQL이 설치된 폴더로 이동후, 다음 명령어 입력하여 접속
> mysql -h localhost -u root -p
 - 비밀번호 입력
> Enter passward : [ 비밀번호 입력 ]
 - 다시 콘솔로 돌아가기
> mysql> exit

### macOS
 - Homebrew 설치하기
> /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
 - MySQL 설치하기
> $ brew update   
> $ brew install mysql   
> $ brew services start myhsql   
> $ mysql_secure_installation
 - MySQL 접속하기
    - Window와 다르게 굳이 MySQL이 설치된 폴더로 이동하지 않아도 됨.
> $ mysql -h localhost -u root -p   
> Enter passward : [ 비밀번호 입력 ]

---

#### 02. 워크벤치 설치하기
#### 1. 워크벤치
 - 데이터베이스 내부에 저장된 데이터를 시각적으로 관리할 수 있어 편리함
#### 워크벤치 설치하기_macOS
 > Windows는 앞에서 MySQL과 함꼐 워크벤치를 설치했으므로 넘어감
 - https://dev.mysql.com/downloads/wordbench/ 접속
    - 강의영상 따라 설치 설정 후, 설치

#### 2. 커넥션 생성하기
**워크벤치를 실행하여 커넥션을 생성해 봅시다**
1. 워크벤치 실행
2. 새 커넥션 생성
3. Connection Name을 localhost 입력 후 OK 클릭
4. MySQL 설치할때 설정했던 비밀번호 입력
> 강의영상 보면서 따라가기

---

### MySQL 이해하기
#### 01. 데이터베이스 및 테이블 생성하기
#### 1. 데이터베이스
**관련성을 가지며 중복이 없는 데이터들의 집합**
 - 데이터의 저장소라고 생각하면 편함
 - DBMS
    - 데이터베이스 관리 시스템
    - SQL을 사용 여부로 나뉨 (ex) SQL -> MySQL, ORACLE / NOSQL -> mongoDB

#### 2. 데이터베이스 생성하기
 - MySQL 접속
 - CREATE SCHEMA [데이터베이스명]
    - 데이터 베이스를 생성하는 명령어
 > SQL구문을 입력할 땐, 마지막에 세미콜론(;)을 붙여야 실행됨.
 - CREATE TABLE [데이터베이스명.테이블명]
    - 테이블을 생성하는 명령어
```msyql
mysql> CREATE TABLE nodejs.users (
    -> id INT NOT NULL AUTO_INCREMENT,
    -> name VARCHAR(20) NOT NULL,
    -> age INT UNSIGNED NOT NULL,
    -> married TINYINT NOT NULL,
    -> comment TEXT NULL,
    -> created_at DATETIME NOT NULL DEFAULT now(),
    -> PRIMARY KEY(id),
    -> UNIQUE_INDEX name_UNIQUE (name ASC))
    -> COMMENT = '사용자 정보'
    -> DEFAULT CHARSET=utf8
    -> ENGINE=InnoDB;
```
 > 콤마(,)로 구분해 컬럼을 만들어 둠.   
 > 컬럼을 정의해두면 데이터를 넣을때 컬럼 규칙에 맞는 정보들만 넣을 수 있음

 - 컬럼과 로우
    - 컬럼과 로우가 교차하는 칸 하나 -> 필드
    - 테이블에 데이터를 넣을때는 미리 컬럼을 정의해두고 컬럼에 맞춰 데이터를 넣으면 됨
 - 테이블 확인
```mysql
mysql> DESC users;
```
 > DESC [테이블명] -> 만들어진 테이블을 확인하는 명령어
 - 테이블 제거
```mysql
mysql> DROP TABLE users;
```
 > DROP TABLE [테이블명] -> 테이블 제거(테이블을 잘못 만들었을 경우 사용)

#### 테이블 생성
**사용자의 댓글을 저장하는 테이블을 만들어봅시다**
 - 다른 테이블의 기본 키를 저장하는 컬럼 -> 외래 키
 > CONSTRAINT [제약조건명] FOREIGN KEY [컬럼명] REFERENCES [참고하는 컬럼명]

 - 강의영상 따라가면서 테이블 생성하기
 - 테이블 확인
```mysql
mysql> SHOW TABLES;
```

---

#### 02. CRUD 작업하기
#### 1. CRUD 란?
**Crete, Read, Update, Delete**
#### 2. CREATE(생성)
 - **데이터를 생성해서 데이터베이스에 넣는 작업**
 > INSERT INTO [테이블명] ([컬럼1], [컬럼2], .... ) VALUES ([값1]. [값2], ...)   
 >  -> 데이터를 넣는 명령어
#### 3. READ(조회)
 - **데이터베이스에 있는 데이터를 조회하는 작업**
 > SELECT * FROM [테이블명]
 >  -> 해당 이름의 테이블의 모든 데이터를 조회하는 SQL문
 - 특정 컬람만 조회할 수 있음
    - 조회를 원하는 컬럼은 SELECT 다음에 넣기
 - WHERE 절을 사용하면 특정 조건을 가진 데이터만 조회가능
 - AND, OR로 여러 조건을 묶어줄 수도 있음
    - AND는 조건들을 모두 만족하는 데이터를 찾음
    - OR은 조건들 중 어느 하나라도 만족하는 데이터를 찾음
 - OREDER BY [컬럼명] [ASC | DESC]
    - 정렬 기능
    - DESC: 내림차순
    - ASC: 오름차순
 - LIMIT [숫자]
    - 조회할 로우 개수 설정
    - SQL문 끝에 붙임
  - OFFSET [건너뛸 숫자]
    - 건너뛸 개수 설정
    - SQL문 끝에 붙임 
    - 게시판 등의 페이지 기능 구현할때 유용함. (첫 페이지 게시물 개수만큼 건너뛰고 다음 페이지 게시물 조회)
#### 4. UPDATE(수정)
 - **데이터베이스에 있는 데이터를 수정하는 작업**
 - UPDATE [테이블명] SET [칼럼명 = 바꿀값] WHERE [조건]
    - 조건도 AND나 OR로 여러개를 동시에 사용할 수 있음.
#### 5. DELETE(삭제)
 - **데이터베이스에 있는 데이터를 삭제하는 작업**
 - DELETE FROM [테이블명] WHERE [조건]
    - 조건도 AND나 OR로 여러개를 동시에 사용할 수 있음.
---

#### 03.시퀄라이즈 사용하기
#### 1. 시퀄라이즈
 - **MySQL 작업을 쉽게 할 수 있도록 도와주는 라이브러리**
 - 자바스크립트 구문을 알아서 SQL로 바꿔줌
 - Express 프로젝트 생성
> $ express learn-sequelize --view=pug
 - npm 패키지 설치
    -learn-sequelize 폴더로 이동해 npm 패키지들을 설치
> $ cd learn-sequelize   
> $ npm i
 - sequelize와 mysql2 패키지 설치
> $ npm i sequelize mysql2
> $ npm i -g sequelize-cli
> $ sequelize init
macOS는 명령어 앞에 sudo 붙여야함   
 - models/index.js 수정
    - sequelize-cli가 자동으로 생성해주는 코드는 그대로 사용했을때 에러 발생, 또한 필요없는 부분 많음
```js
const path = require('path');
const Sequelize = require('sequelize');

const env = process.env.NODE_ENV || 'development';
const config = require(path.join(__dirname, '..', 'config', 'config.json'))[env];
const db = {};

const sequelize = new Sequelize(config.database, config.username, config.password, config);

db.sequelize = sequelize;
db.Sequelize = Sequelize;

module.exports = db;
```

#### 2. MySQL 연결하기
 - **시퀄라이즈를 통해 익스프레스 앱과 MySQL 연결**
 - app.js에서 추가
```js
...
var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');

var sequelize = require('./models').sequelize;

var app = express();
sequelize.sync();
...
```

#### 3. 모델 정의하기
 - **MySQL에서 정의한 테이블을 시퀄라이즈에서도 정의**
 - 시퀄라이즈 -> 모델과 MySQL의 테이블을 연결해주는 역할
 - 기본적으로 모델이름은 단수형으로, 테이블 이름은 복수형으로
 - **User 모델 만들기**
```js
module.exports = (sequelize, DataTypes) => {
    return sequelize.define('user', {
        name: {
            type: DataTypes.STRING(20),
            allowNull: false,
            unique: true,
        },
        married: {
            type: DataTypes.BOOLEAN,
            allowNull: false,
        },
        comment: {
            type: DataTypes.TEXT,
            allowNull: true,
        },
        created_at: {
            type: DataTypes.DATE,
            allowNull: false,
            defaultValue: sequelize.literal('now()'),
        },
    }, {
        timestamps: false,
    });
};
```
 - **Comment 모델 만들기**
```js
module.exports = (sequelize, DataTypes) => {
    return sequelize.define('comment', {
        comment: {
            type: DataTypes.STRING(100),
            allowNull: false,
        },
        created_at: {
            type: DataTypes.DATE,
            allowNull: false,
            defaultValue: sequelize.literal('now()'),
        },
    }, {
        timestamps: false,
    });
};
```
 - 모델 생성 후 models/index.js와 연결
```js
...
db.sequelize = sequelize;
db.Sequelize = Sequelize;

db.User = require('./user')(sequelize, Sequelize);
db.Comment = require('./comment')(sequelize, Sequelize);

module.exports = db;
```
 - config.json 수정하기
    - development.password와 development.database를 현재 MySQL 커넥션과 일치하게 수정
    - test와 production 쪽은 나중에 사용
```json
{
  "development": {
    "username": "root",
    "password": "[root 비밀번호]",
    "database": "nodejs",
    "host": "127.0.0.1",
    "dialect": "mysql",
    "operatorsAliases": false
  },
...
}
```

#### 4. 관계 정의하기
 - **1:N 관계**
   - User 하나(1)는 Comment 여러개(n) 가질 수 있음.
   - Comment들(n)은 각각 한명의 User(1)을 가짐.
   - 시퀄라이즈에서는 1:N 관계를 hasMany라는 매서드로 표현.
   - 반대로 belongsTo 메서드도 있음.
```js
db.User.hasMany(db.Comment, { foreignKey: 'commenter', sourceKey: 'id' });
db.Comment.belongsTo(db.User, { foreignKey: 'commenter', targetKey: 'id' });
```
 - **1:1 관계**
   - User 하나(1)는 Info 하나(1)를 가짐.
   - Info 하나(1)는 User 하나(1)를 가짐.
   - 시퀄라이즈에서는 1:1 관계를 hasOne라는 매서드로 표현.
   - 반대로 belongsTo 메서드도 있음.
   - 일대일이기에 belongsTo와 hasOne이 반대여도 상관없음.
```js
db.User.hasOne(db.Info, { foreignKey: 'user_id', sourceKey: 'id' });
db.Info.belongsTo(db.User, { foreignKey: 'user_id', targetKey: 'id' });
```
 - **N:M 관계**
    - 게시물 하나(1)에는 해시태그 여러개(n) 달릴 수 있음
    - 해시태그 하나(1)는 많은 게시물(m)에서 사용.
    - 시퀄라이즈에서는 N:M 관계를 belongsToMany 메서드로 표현함.
```js
db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' });
db.Hashtag.belongsToMany(db.Post, { through: 'PostHashtag' });
```

#### 5. 쿼리 알아보기
 - 로우를 생성하는 쿼리
 > INSERT INTO nodejs.users (name, age, married, comment) VALUES ('zero', 24, 0, '자기소개1');
```js
const { User } = require('../models');
User.create({
  name: 'zero',
  age: 24,
  married: false,
  comment: '자기소개1',
});
```

 - 로우를 조회하는 쿼리들
 > SELECT * FROM nodejs.users;
```js
User.findAll({});
```
 > SELECT * FROM nodejs.users LIMIT 1;
```js
User.find({});
```
 > SELECT name, married FROM nodejs.users;
```js
// attributes 옵션을 사용해서 원하는 컬럼만 가져 올 수 있음.
User.findAll({
 attributes: ['name', married'],
});
```
 > SELECT id, name FROM users WHERE married = 0 OR age > 30;
```js
// where 옵션을 사용해서 조건을 나열함.
User.findAll({
 attributes: ['id', name'],
 where: {
   [Op.or]: [{ married: 0 }, { age: { [Op.gt]: 30 } }],
 },
});
```
 > <<자주 쓰이는 연산자>>   
 > Op.gt(초과), Op.gte(이상), Op.It(미만), Op.Ite(이하), Op.ne(같지않음), Op.or(또는), Op.in(배열 요소 중 하나), Op.notIn(배열요소와 모두 다름)   

 - 정렬과 로우 개수를 설정하는 방법
    - order 옵션으로 정렬 가능
    - limit 옵션으로 가능함.
    - OFFSET도 offset 속성으로 구현함.
 > SELECT id, name FROM users ORDER BY age DESC LIMIT 1;
```js
// LIMIT 1인 경우에는 findAll대신 find 메서드를 사용해도 됨.
User.findAll({
  attributes: ['id', name'],
  order: ['age', 'DESC'],
  limit: 1,
});
```

 - 로우를 수정하는 쿼리
 > UPDATE nodejs.users SET comment = '바꿀 내용' WHERE id = 2;
```js
// update 메서드를 사용, 첫번째 인자는 수정할 내용, 두번째 인자는 수정대상 로우를 찾는 조건(where 옵션에 조건들을 적어줌)
User.update({
  comment: '바꿀 내용',
}, {
  where: { id: 2 },
});
```

 - 로우를 삭제하는 쿼리
 > DELETE FROM nodejs.users WHERE id = 2;
```js
// destroy 메서드로 삭제함, where 옵션에 조건들을 적어줌.
User.destroy({
  where: { id: 2 },
});
```

---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(총 1개의 프로젝트를 압축해서 Notion 페이지에 올려주세요!)   
(app.js에 CRUD 작업 중 Read와 Update는 본인이 코드를 45~48 페이지를참고하며 작성해주세요!)   

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)
