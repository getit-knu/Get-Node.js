# GET IT SW교육 Node.js
## 7차시_Express로 SNS 서비스 만들기
> 기간 : 24.11.11~24.11.13

### 🗂️7차시 자료🗂️
[Node.js 7차시 자료](https://github.com/getit-knu/Get-Node.js/blob/main/7%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%207%EC%B0%A8%EC%8B%9C.pdf)

👉자료를 다운 받은 후 수강해주세요!

### 🎞️7차시 강의🎞️

[![Video Label](http://img.youtube.com/vi/6BTZNc6qHcY/0.jpg)](https://www.youtube.com/watch?app=desktop&v=6BTZNc6qHcY&feature=youtu.be)

👉위 이미지를 누르면 강의를 들을 수 있습니다.

---

### 🚀목차🚀
1. 프로젝트 구조 갖추기
2. 데이터베이스 세팅하기
3. Passport 로그인 구현하기
4. Multer 이미지 업로드 구현하기

---

#### 01. 프로젝트 구조 갖추기
#### STEP 0. nodebird 폴더 만들기 & package.json 생성
```json
{
    "name": "nodebird",
    "version": "0.0.1",
    "description": "express로 만드는 SNS 서비스",
    "main": "app.js",
    "scripts": {
        "start": "nodemon app"
    },
    "author": "chan",
    "license": "MIT"
}
```

#### STEP 1. MYSQL 사용하기 위해 시퀄라이즈 준비
 > npm i sequelize mysql2 sequelize-cli   
 > sequelize init   

#### STEP 2. 나머지 폴더 생성 & app.js 생성
 > passport, public, routes, views, app.js

#### STEP 3. npm 패키지 설치 및 app.js, .env 작성
 > npm i express cookie-parser express-session morgan   
 > npm i multer dotenv nunjucks bcrypt   
 > npm i -D nodemon

 - .env파일에는 민감한 내용들 저장(github 올릴때 제외시키고 올리기)
 - app.js 작성
```js
// app.js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');

dotenv.config();
const pageRouter = require('./routes/page');
const app = express();
app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
    express: app,
    watch: true,
});

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(express.json());
app.use(express.urlencoded({ extended: false }));
app.use(cookieParser(process.env.COOKIE_SECRET));
app.use(session({
    resave: false,
    saveUninitialized: false,
    secret: process.env.COOKIE_SECRET,
    cookie: {
        httpOnly: true,
        secure: false,
    }
}));

app.use('/', pageRouter);

app.use((req, res, next) => {
    const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
    error.status = 404;
    next(error);
});

app.use((err, req, res, next) => {
    res.locals.message = error.message;
    res.locals.error = process.env.NODE_ENV !== 'production' ? err : {};
    res.status(err.status || 500);
    res.render('error');
});

app.listen(app.get('port'), () => {
    console.log(`http://localhost:${app.get('port')}`);
    console.log(app.get('prot'), '빈 포트에서 대기 중');
});
```

### STEP 4. routes/page.js 작성
 - view에 렌더링할 페이지 작성 (제공된 html 사용)
    - layout.html -> 기본 틀
    - main.html -> 게시물 업로드 화면
    - profile.html -> 사용자의 팔로워와 팔로우중인 목록 띄워줌
    - join.html -> 회원가입 화면
    - error.html -> 에러 처리
 - public에 페이지 꾸미는 css 작성 (제공된 css 사용)
    - main.css -> 페이지 꾸미는 기능
 - routes/page.js 작성
```js
const express = require('express');
const router = express.Router();

router.use((req, res, next) => {
    res.locals.user = null;
    res.locals.followerCount = 0;
    res.locals.followingCount = 0;
    res.locals.followerIdList = [];
    next();
});

router.get('/profile', (req, res) => {
    res.render('profile', { title: '내 정보 - NodeBird' });
});

router.get('/join', (req, res) => {
    res.render('join', { title: '회원가입 - NodeBird' });
});

router.get('/', (res, req, next) => {
    const twits = [];
    res.render('main', {
        title: 'NodeBird',
        twits,
    });
});
```

---

#### 02. 데이터베이스 세팅하기
#### STEP 0. 필요한 모델 생각하기
 - 로그인 기능을 위해 사용자 정보 저장용 - user.js
 - 게시글 저장용 - post.js
 - 해시태그 사용하므로 - hashtag.js

#### STEP 1. models 폴더 안에 user.js, post.js, hashtag.js 작성
 - 로그인 기능을 위해 사용자 정보 저장용 - user.js
```js
// user.js
const Sequelize = require('sequelize');

module.exports = class User extends Sequelize.Model {
    static init(sequelize) {
        return super.init({
            email: {
                type: Sequelize.STRING(40),
                allowNull: true,
                unique: true,
            },
            nick: {
                type: Sequelize.STRING(15),
                allowNull: false,
            },
            password: {
                type: Sequelize.STRING(100),
                allowNull: true,
            },
            provider: {
                type: Sequelize.STRING(10),
                allowNull: false,
                defaultValue: 'local',
            },
            snsId: {
                type: Sequelize.STRING(30),
                allowNull: true,
            },
        }, {
            sequelize,
            timestamps: true,
            underscored: false,
            modelName: 'User',
            tableName: 'users',
            paranoid: true,
            charset: 'utf8',
            collate: 'utf8_general_ci',
        });
    }
    static associate(db) {}
}
```
 
 - 게시글 저장용 - post.js
```js
// post.js
const Sequelize = require('sequelize');

module.exports = class Post extends Sequelize.Model {
    static init(sequelize) {
        return super.init({
            content: {
                type: Sequelize.STRING(140),
                allowNull: false,
            },
            img: {
                type: Sequelize.STRING(200),
                allowNull: true,
            },
        }, {
            sequelize,
            timestamps: true,
            underscored: false,
            modelName: 'Post',
            tableName: 'posts',
            paranoid: false,
            charset: 'utf8',
            collate: 'utf8_general_ci',
        });
    }
    static associate(db) {}
}
```
 
 - 해시태그 사용하므로 - hashtag.js
```js
// hashtag.js
const Sequelize = require('sequelize');

module.exports = class Hashtag extends Sequelize.Model {
    static init(sequlize) {
        return super.init({
            title: {
                type: Sequelize.STRING(15),
                allowNull: false,
                unique: true,
            },
        }, {
            sequelize,
            timestamps: true,
            underscored: false,
            modelName: 'Hashtag',
            tableName: 'hashtags',
            paranoid: true,
            charset: 'utf8mb4',
            collate: 'utf8mb4_general_ci',
        });
    }
    static associate(db) {}
}
```


#### STEP 2. 자동으로 코드 작성된 index.js 수정 & 관계 정의
 - index.js 내용 지우고 새로 작성
```js
// index.js
const Sequelize = require('sequelize');
const env = process.env.NODE_ENV || 'development';
const config = require('../config/config')[env];
const User = require('./user');
const Post = require('./post');
const Hashtag = require('./hashtag');

const db = {};
const sequelize = new Sequelize(
  config.database,
  config.username,
  config.password,
  config,
);

db.sequelize = sequelize;
db.User = User;
db.Post = Post;
db.Hashtag = Hashtag;

User.init(sequelize);
Post.init(sequelize);
Hashtag.init(sequelize);

User.associate(db);
Post.associate(db);
Hashtag.associate(db);

module.exports = db;
```

- user.js, post.js 관계 정의 부분 추가
```js
// app.js
...
    static associate(db) {
        db.User.hasMany(db.Post);
        db.User.belongsToMany(db.User, {
            foreignKey: 'followingId',
            as: 'Followers',
            throuth: 'Follow'
        })
        db.User.belongsToMany(db.User, {
            foreignKey: 'followerId',
            as: 'Followings',
            through: 'Follow',
        })
    }
...
```

```js
// post.js
...
    static associate(db) {
        db.Post.belongsTo(db.User);
        db.Post.belongsTo(db.Hashtag, { through: 'PostHashtag' });
    }
...
```

#### STEP 3. config/config.json 수정
 - password 부분은 따로 추
```json
{
  "development": {
    "username": "root",
    "password": "",
    "database": "nodebird",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "test": {
    "username": "root",
    "password": null,
    "database": "database_test",
    "host": "127.0.0.1",
    "dialect": "mysql"
  },
  "production": {
    "username": "root",
    "password": null,
    "database": "database_production",
    "host": "127.0.0.1",
    "dialect": "mysql"
  }
}

```


#### STEP 4. 데이터베이스 만들기
 > sequelize db:create

#### STEP 5. 모델을 서버와 연결
 - app.js에 추가
```js
// app.js
...
dotenv.config();
const pageRouter = require('./routes/page');
const { sequelize } = require('./models');

const app = express();
app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
    express: app,
    watch: true,
});
sequelize.sync({ force: false });
...
```
---

#### 03. Passport 로그인 구현하기
#### STEP 0. 필요한 모듈 다운받기
 > npm i passport passport-local

---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(총 1개의 프로젝트를 압축해서 Notion 페이지에 올려주세요!)   
(app.js에 CRUD 작업 중 Read와 Update는 본인이 코드를 45~48 페이지를참고하며 작성해주세요!)   

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)
