# GET IT SW교육 Node.js
## 7차시_Express로 SNS 서비스 만들기
> 기간 : 24.11.11~24.11.13

### 🗂️7차시 자료🗂️
[Node.js 7차시 자료](https://github.com/getit-knu/Get-Node.js/blob/main/7%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%207%EC%B0%A8%EC%8B%9C.pdf)

👉자료를 다운 받은 후 수강해주세요!

### 🎞️7차시 강의🎞️

[![Video Label](https://i9.ytimg.com/vi/4c_1rHztHH4/mqdefault.jpg?v=67a22a99&sqp=CNyLjb0G&rs=AOn4CLAXfQkQFing3Z5meGZ-7QFCfwF_vA)](https://youtu.be/4c_1rHztHH4)

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
            throuth: 'Follow',
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
        db.Post.belongsToMany(db.Hashtag, { through: 'PostHashtag' });
    }
...
```

#### STEP 3. config/config.json 수정
 - password 부분은 따로 추가
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

#### STEP 1. Passport 모듈과 app.js 연결
```js
// app.js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const passport = require('passport')

dotenv.config();
const pageRouter = require('./routes/page');
const { sequelize } = require('./models');
const passportConfig = require('./passport');


const app = express();
passportConfig();
app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
    express: app,
    watch: true,
});
sequelize.sync({ force: false });

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
app.use(passport.initialize());
app.use(passport.session());

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
    console.log(app.get('port'), '빈 포트에서 대기 중');
});
```

#### STEP 2. app.js와 연결한 Passport 모듈 작성
 - passport.setrializeUser: 로그인시 실행, req.session(세션) 객체에 어떤 데이터를 저장할지 정하는 메서드
    - 매개변수로 user를 받음
    - done 함수에 두 번째 인수로 user.id를 넘김(원하는 데이터 넣으면 됨)
    - done 함수의 첫 번째 인수는 에러 발생 시 사용, 두 번째 인수에는 저장하고 싶은 데이터 넣음
 - passport.deserializeUser: 매 요청 시 실행됨, passport.session 미들웨어가 이 메소드를 호출
    - done의 두 번째 인수가 desirializeUser의 매개변수가 됨
    - user.id를 받아 데이터베이스에서 사용자 정보를 조회함
 > -> serializeUser는 사용자 정보 객체를 세션에 아이디로 저장하는 것   
 > -> deserializeUser는 세션에 저장한 아이디를 토앻 사용자 정보 객체를 불러오는 것   
 - passport/index.js 작성
```js
// index.js
const passport = require('passport');
const local = require('./localStrategy');
const { User } = require('../models');

module.exports = () => {
    passport.serializeUser((user, done) => {
        done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
        User.findOne({ where: { id } })
            .then(user => done(null, user))
            .catch(err => done(err));
    });
    local();
};
```

#### STEP 3. 접근 권한을 제어하는 미들웨어 구현
 - 접근 권한을 제어하는 미들웨어가 필요
 - -> 로그인한 사용자가 회원가입과 로그인 라우터에 접근 금지
 - isAuthenticated 메서드: 미들웨어를 만들며 Passport가 req 객체에 추가해줌
 - routes/middlewares.js 작성
```js
// middlewares.js
exports.isLoggedIn = (req, res, next) => {
    if (req.isAuthenticated()) {
        next();
    } else {
        res.status(403).send('로그인 필요');
    }
};

exports.isNotLoggedIn = (req, res, next) => {
    if (!req.isAuthenticated()) {
        next();
    } else {
        const message = encodeURIComponent('로그인한 상태입니다.');
        res.redirect(`/?error${message}`);
    }
};
```

#### STEP 4. 접근 권한을 제어하는 미들웨어를 라우터에 적용
 - routes/page.js 내용 추가
```js
// page.js
const express = require('express');
const { isLoggedIn, isNotLoggedIn } = require('./middlewares');
const router = express.Router();

router.use((req, res, next) => {
    res.locals.user = req.user;
    res.locals.followerCount = 0;
    res.locals.followingCount = 0;
    res.locals.followerIdList = [];
    next();
});

router.get('/profile', isLoggedIn, (req, res) => {
    res.render('profile', { title: '내 정보 - NodeBird' });
});

router.get('/join', isNotLoggedIn, (req, res) => {
    res.render('join', { title: '회원가입 - NodeBird' });
});

router.get('/', (req, res, next) => {
    const twits = [];
    res.render('main', {
        title: 'NodeBird',
        twits,
    });
});
module.exports = router;
```

#### STEP 5. 회원가입, 로그인, 로그아웃 라우터 작성
 - routes/auth.js 작성
```js
// auth.js
const express = require('express');
const passport = require('passport');
const bcrypt = require('bcrypt');
const { isLoggedIn, isNotLoggedIn } = require('./middlewares');
const { User } = require('../models');

const router = express.Router();

router.post('/join', isNotLoggedIn, async (req, res, next) => {
    const { email, nick, password } = req.body;
    try {
        const exUser = await User.findOne({ where: { email } });
        if (exUser) {
            return res.redirect('/join?error-exist');
        }
        const hash = await bcrypt.hash(password, 12);
        await User.create({
            email,
            nick,
            password: hash,
        });
        return res.redirect('/');
    } catch (error) {
        console.error(error);
    }
});

router.post('/login', isNotLoggedIn, (req, res, next) => {
    passport.authenticate('local', (authError, user, info) => {
        if (authError) {
            console.error(authError);
            return next(authError);
        }
        if (!user) {
            return res.redirect(`/?loginError=${info.message}`);
        }
        return req.login(user, (loginError) => {
            if (loginError) {
                console.error(loginError);
                return next(loginError);
            }
            return res.redirect('/');
        });
    })(req, res, next);
});

router.get('/logout', isLoggedIn, (req, res) => {
    req.logout();
    req.session.destroy();
    res.redirect('/');
});

module.exports = router;
```

#### STEP 6. 로그인 전략 구현!
 > passport-local 모듈에서 Strategy 생성자를 불러와 사용   

1. 로그인 성공 시
 - done(null, exUser); -> possport.authenticate('local', (authError, user, info) => {
2. 로그인 실패 시
 - done(null, false, { message: '비밀번호가 일치하지 않습니다.' }); -> passport.authenticate('local', (authError, user, info) => {
3. 서버 에러 시
 - done(error) -> passport.authenticate('local', (authError, user, info) => {

**route/localStrategy.js 작성**
```js
// localStrategy.js
const passport = require('passport');
const localStrategy = require('passport-local').Strategy;
const bcrypt = require('bcrypt');

const { User } = require('../models');

module.exports = () => {
    passport.use(new localStrategy({
        usernameField: 'email',
        passwordField: 'password',
    }, async (email, password, done) => {
        try {
            const exUser = await User.findOne({ where: { email } });
            if (exUser) {
                const result = await bcrypt.compare(password, exUser.password);
                if (result) {
                    done(null, exUser);
                } else {
                    done(null, false, { message: '비밀번호가 일치하지 않습니다. '});
                }
            } else {
                done(null, false, { message: '가입되지 않은 회원입니다.'});
            }
        } catch (error) {
            console.error(error);
            done(error);
        }
    }));
};
```

#### STEP 7. app.js에 auth router 추가
**로그인 과정**   
1. 라우터를 통해 로그인 요청이 들어옴
2. 라우터에서 passport.authenticate 메서드 호출
3. 로그인 전략 수행
4. 로그인 성공 시 사용자 정보 객체와 함께 req.login 호출
5. req.login 메서드가 passport.serailizeUser 호출
6. 로그인 완료

**로그인 후**
1. 요청이 들어옴
2. 라우터에 요청이 도달하기 전에 passport.session 미들웨어가 passport.deserializeUser 메서드 호출
3. req.session에 저장된 아이디로 데이터베이스에서 사용자 조회
4. 조회된 사용자 정보를 req.user에 저장
5. 라우터에서 req.user 객체 사용 가능

**app.js 내용 추가**
```js
// app.js
...
dotenv.config();
const pageRouter = require('./routes/page');
const authRouter = require('./routes/auth');
const { sequelize } = require('./models');
const passportConfig = require('./passport');


const app = express();
passportConfig();
app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
    express: app,
    watch: true,
});
sequelize.sync({ force: false });

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
app.use(passport.initialize());
app.use(passport.session());

app.use('/', pageRouter);
app.use('/auth', authRouter);
...
```
---

#### 04. Multer 이미지 업로드 구현하기
#### STEP 1. multer 모듈 설치하기
 > npm i multer
 - routes/post.js 작성
```js
// post.js
const express = require('express');
const multer = require('multer');
const path = require('path');
const fs = require('fs');

const { Post, Hashtag, User } = require('../models');
const { isLoggedIn } = require('./middlewares');

const router = express.Router();

try {
    fs.readdirSync('uploads');
} catch (error) {
    console.error('uploads 폴더가 없어 uploads 폴더를 생성합니다.');
    fs.mkdirSync('uploads');
}

const upload = multer({
    storage: multer.diskStorage({
        destination(req, file, cb) {
            cb(null, 'uploads/');
        },
        filename(req, file, cb) {
            const ext = path.extname(file.originalname);
            cb(null, path.basename(file.originalname, ext) + Date.now() + ext);
        },
    }),
    limits: { fileSize: 5 * 1024 * 1024 },
})

router.post('/img', isLoggedIn, upload.single('img'), (req, res) => {
    console.log(req.file);
    res.json({url : `/img/${req.file.filename}`});
});

const upload2 = multer();
router.post('/', isLoggedIn, upload2.none(), async (req, res, next) => {
    try {
        const post = await Post.create({
            content: req.body.content,
            img: req.body.url,
            UserId: req.user.id,
        });

        const hashtags = req.body.content.match(/#[^\s#]*/g);
        if (hashtags) {
            const result = await Promise.all(hashtags.map(tag => Hashtag.findOrCreate({
                where: { title: tag.slice(1).toLowerCase() },
            })));
            await post.addHashtags(result.map(r => r[0]));
        }
        res.redirect('/');
    } catch (error) {
        console.error(error);
        next(error);
    }
});

module.exports = router;
```

#### STEP 2. page.js의 설정 변경
```js
// page.js
const express = require('express');
const { isLoggedIn, isNotLoggedIn } = require('./middlewares');
const { Post, User } = require('../models');
const router = express.Router();

router.use((req, res, next) => {
    res.locals.user = req.user;
    res.locals.followerCount = 0;
    res.locals.followingCount = 0;
    res.locals.followerIdlist = [];
    next();
});

router.get('/profile', isLoggedIn, (req, res) => {
    res.render('profile', { title: '내 정보 - NodeBird' });
});

router.get('/join', isNotLoggedIn, (req, res) => {
    res.render('join', { title: '회원가입 - NodeBird' });
});

router.get('/', async (req, res, next) => {
    try {
        const post = await Post.findAll({
            include: {
                model: User,
                attributes: ['id', 'nick'],
            },
            order: [['createdAt', 'DESC']],
        });
        res.render('main', {
            title: 'NodeBird',
            twits: post,
        });
    } catch (error) {
        console.error(error);
        next(error);
    }
});

module.exports = router;
```

#### STEP 3. app.js에 post router 추가
```js
// app.js
const express = require('express');
const cookieParser = require('cookie-parser');
const morgan = require('morgan');
const path = require('path');
const session = require('express-session');
const nunjucks = require('nunjucks');
const dotenv = require('dotenv');
const passport = require('passport')

dotenv.config();
const pageRouter = require('./routes/page');
const authRouter = require('./routes/auth');
const postRouter = require('./routes/post');
const { sequelize } = require('./models');
const passportConfig = require('./passport');


const app = express();
passportConfig();
app.set('port', process.env.PORT || 8001);
app.set('view engine', 'html');
nunjucks.configure('views', {
    express: app,
    watch: true,
});
sequelize.sync({ force: false });

app.use(morgan('dev'));
app.use(express.static(path.join(__dirname, 'public')));
app.use('/img', express.static(path.join(__dirname, 'uploads')));
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
app.use(passport.initialize());
app.use(passport.session());

app.use('/', pageRouter);
app.use('/auth', authRouter);
app.use('/post', postRouter);

app.use((req, res, next) => {
    const error = new Error(`${req.method} ${req.url} 라우터가 없습니다.`);
    error.status = 404;
    next(error);
});

app.use((err, req, res, next) => {
    res.locals.message = err.message;
    res.locals.error = process.env.NODE_ENV !== 'production' ? err : {};
    res.status(err.status || 500);
    res.render('error');
});

app.listen(app.get('port'), () => {
    console.log(`http://localhost:${app.get('port')}`);
    console.log(app.get('port'), '빈 포트에서 대기 중');
});
```

---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(프로젝트를 실행해서 메인화면, 로그인된 화면, 이미지랑 게시글 업로드 한 화면을 스크린샷 찍어서 Notion 페이지에 올려주세요!)   
(강의 자료에서 예시 참고)   

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)
