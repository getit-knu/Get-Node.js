# GET IT SW교육 Node.js
## 5차시_express 웹 서버 만들기
> 기간 : 24.11.01~24.11.05

### 🗂️5차시 자료🗂️
[Node.js 5차시 자료](https://github.com/getit-knu/Get-Node.js/blob/main/5%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%205%EC%B0%A8%EC%8B%9C.pdf)

👉자료를 다운 받은 후 수강해주세요!

### 🎞️5차시 강의🎞️
[![Video Label](https://i9.ytimg.com/vi/juBHnOQAGbA/mqdefault.jpg?v=67a1cfd8&sqp=CIiOjb0G&rs=AOn4CLBX9-q81RYNOuAb1sTCxzq9blm2tA)](https://youtu.be/juBHnOQAGbA)

👉위 이미지를 누르면 강의를 들을 수 있습니다.

---

### 🚀목차🚀
1. Express 구조
2. 미들웨어
3. Router로 라우팅

---

#### 01. Express 구조
#### 1. Express
**http 모듈의 요청과 응답 객체 + 추가 기능**   
  - http 모듈만으로는 구현하는것에 어려움 있음   
  - 편리한 메소드 많음

#### 2. 설치
**Express-generator 사용하여 빠르게 설치**
  - express에는 많은 패키지 존재
  - 하나하나 다 다운받기보단 Express-generator를 사용하면 한번에 설치가능

  > 설치 명령어
  >> (sudo) npm i -g express-generator

  > express 프로젝트 생성
  >> express learn-express

  > npm 모듈 설치
  >> cd learn-express //폴더 들어간 후   
  >> npm i //npm 모듈 설치

  > 실행 명령어
  >> npm start

#### 3. express 구조
**요청이 들어오면 익스프레스 프레임 워크 내 미들웨어들이 요청을 처리하여 응답 전송**
  > **app.js - 서버 역할**   
  > **bin 폴더의 www파일 - 서버를 실행하는 스크립트**   
> public 폴더 - 외부(브라우저 등의 클라이언트)에서 접근 가능한 파일들을 모아둔 곳(이미지, 자바스크립트, CSS 파일)   
> routes 폴더 - 주소별 라우터들을 모아둔 곳, 서버의 로직 정리   
> views 폴더 - 템플릿 파일을 모아둔 곳, 화면 부분
> models 폴더 - 디비

---

#### 02. 미들웨어
#### 1. 미들웨어
**express의 핵심, 요청과 응답의 중간에 위치함**
 - 요청과 응답을 조작하여 기능 추가 및 나쁜 요청 걸러냄
 - app.use()와 함께 사용

##### 2. 커스텀 미들웨어
**미들웨어를 직접 만들어보며 이해해보자!!**

 - 여러 개의 미들웨어를 app.use 하나에 장착 가능
```js
app.use('/', function(req, res, next) {
  console.log('첫번째 미들웨어');
   next();
}, function(req, res, next) {
  console.log('두번째 미들웨어');
  next();
}, function(req, res, next) {
  console.log('세번째 미들웨어');
  next();
});
```

 - next를 사용안하면 생기는 성질을 이용할 수 있음
```js
app.use(function(req, res, next) {
  if(+new Date() % 2 === 0) {
   return res.status(404).send('50% 실패');
  } else {
     next();
  }
}, function(req, res, next) {
  console.log('50% 성공');
  next();
});
```

#### 3. morgan
**요청에 대한 정보를 콘솔에 기록**

```js
var logger = require('morgan');

app.user(logger('dev'));
// dev, short - 개발, common, combined - 배포
```
 - ex) GET / 200 51.267 - 1539   
    -> HTTP 요청, 주소, HTTP 상태코드, 응답속도, 응답바이트
   
#### 4. bodyparser
**요청의 본문을 해석해주는 미들웨어**

```js
var bodyParser = require('body-parser');
// json 데이터 형식
app.use(bodyParser.json());
// 주소 데이터 형식, 보통 폼 전송
// extended : false 노드의 querystring 모듈을 사용하여 쿼리스트링 해석
// true 노드의 qs 모듈을 사용하여 쿼리스트링 해석 (npm 패키지, 확장된 버전)
app.use(bodyParse.urlencoded({extended: false}));
// 버퍼 데이터 형식
app.use(bodyParser.raw());
// 텍스트 데이터 형식
app.use(bodyParser.text());
```
 - ex) json 형식인 {name: 'zerocho', book: 'nodejs'}이면 req.body에 그대로 들어감   
    주소 형식인 name=zerocho&book=nodejs이면 req.body에 json 형식으로 반환되어 들어감

#### 5. cookie-parser
**요청으로 온 쿠키 해석**

```js
var cookieParser = require('cookie-parser');
app.use(cookieParser());
```
 - 해석된 쿠키들은 req.cookies 객체로 들어감
 - ex) name=zerocho 쿠키는 req.cookies에 {name: 'zerocho'}로 들어감

#### 6. static
**정적인 파일들 제공해주는 미들웨어**

```js
app.use(express.static(path.join(__dirname, 'public')));
```
 - 함수의 인자에 정적 파일들이 담겨 있는 폴더를 지정
 - 요청에 부합하는 정적 파일을 발견한 후 응답으로 해당 파일 전송
 - ex) public/stylesheets/style.css 경로는   
    http://localhost:3000/stylesheets/style.css로 접근 가능

#### 7. express-session
**세션 관리용 미들웨어**

 > npm i express-session

```js
var session = require('express-session');
...
// cookie-parser 뒤에 사용하자 내부적으로 사용하기 때문에
app.use(session({
  // 요청이 왔을 때 세션에 수정사항이 생기지 않더라도 세션을 다시 저장할지에 대한 설정
  resave: false,
  // 세션에 저장할 내영이 없더라도 세션을 저장할지에 대한 설정
  // 보통 방문자를 추적할 때 사용
  saveUninitialized: false,
  // 필수항목, cookie-parser의 비밀 키와 같은 역할
  secret: 'secret code',
  // 세션 쿠키에 대한 설정
  cookie: {
    httpOnly: true,
    secure: false,
  },
}));
```

#### 8. connect-flash
**일회성 메시지 보낼 때 사용**

 > npm i connect-flash

```js
// app.js
var flash = require('connect-flash');
...
app.use(flash()); // express-session 뒤에 상요

//users.js
router.get('/flash', function(req, res) {
  req.session.message = '세션 메시지';
  req.flash('message', 'flash 메시지');
  res.redirect('/users/flash/result');
});

router.get('/flash/result', function(req, res) {
  res.send(`${req.session.message} ${req.flash('message')}`);
});
```

#### 9. 에러 처리 미들웨어
**템플릿 엔진으로 만들어진 페이지로 에러 처리**

```js
// app.js
app.use(function(err, req, res, next) {
  // res.locals.message && res.locals.error 넣어준 값을 함께 렌더링
  res.locals.message = err.message;
  // req.app.get(키)는 set(키)로 설정한 값을 가져옴
  // 시스템 환경이 development가 아닌 경우 에러 메시지 빈칸, 맞는 경우 err
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  res.status(err.status || 500);
  // 아래의 에러 페이지 렌더링
  res.render('error');
});

//views/error.jade
// 템플릿 엔진으로 만들어진 파일
// 템플릿 엔진은 자바스크립트를 사용해 HTML을 렌더링할 수 있게 해줌
extends layout

block content
  h1= message
  h2= error.status
  // 에러에 관한 상세한 메시지 표시
  pre #{error.stack}
```

 > **Express의 핵심인 미들웨어를 통해 요청과 응답을 체계적으로 관리 가능**

---

#### 03. Router로 라우팅
#### 1. REST API
**요청이 주소를 통해 들어오는데 서버가 이해하기 쉬운 주소를 사용하자**   
 > **REpresentational State Transfer**
 >> 서버의 자원을 정의 + 자원에 대한 주소를 지정하는 방법

 > **HTTP 요청 메서드**
 >> GET: 서버 자원을 가져오고자 할 때 사용   
> > POST: 서버 자원을 새로 등록하고자 할 때 사용   
> > PUT: 서버 자원을 요청에 들어 있는 자원으로 치환하고자 할 때 사용   
> > PATCH: 서버 자원의 일부만 수정하고자 할 때 사용   
> > DELETE: 서버 자원을 삭제하고자 할 때 사용   

 - **주소와 메소드만 보고 요청의 내용을 명확하게 알 수 있음!!**

|HTTP 메서드|주소|역할|
|---|---|---|
|GET|/|restFront.html 파일 제공|
|GET|/about|about.html 파일 제공|
|GET|/users|사용자 목록 제공|
|GET|기타|기타 정적 파일 제공|
|POST|/users|사용자 등록|
|PUT|/users/사용자id|해당 id의 사용자 수정|
|DELETE|/users/사용자id|해당 id의 사용자 제거|

#### 2. Express에서?
**http모듈만 사용해서 REST API 구현하면 코드가 복잡해짐**   
**반대로 Express의 router로 깔끔하게 라우팅 관리 가능**   

```js
// app.js
...
var indexRouter = require('./routes/index');
var usersRouter = require('./routes/users');
...
app.use('/', indexRouter);
app.use('/users', usersRouter);
...
```

```js
// routes/index.js
var express = require('express');
var router = express.Router();

/* GET home page. */
// router에 get, use, post, put, patch, delete 같은 메서드 붙일 수 있음
// router에 여러 개 미들웨어 장착 가능
// router.get('/', middleware1, middleware2, middleware3);
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' }); // 템플릿 엔진
});

module.exports = router; // router를 모듈로 만듬
```

```js
// routes/user.js
var express = require('express');
var router = express.Router();
// var flash = require('connect-flash');

/* GET users listing. */
router.get('/', function(req, res, next) { // /users/ 로 요청 처리
  res.send('respond with a resource');
});

module.exports = router;
```

**요청에 대한 응답은 필수**   
 - 요청이 오면 응답을 하거나 에러 처리를 해야함   
 - 브라우저가 기다리는 동안 다음 동작 수행할 수 없음   
 > send: 버퍼 데이터나 문자열 전송, HTML 코드 전송, JSON 데이터 전송 가능   
> sendFile: 파일을 응답으로 보내주는 메서드   
> json: JSON 데이터 보내줌   
> redirect: 응답을 다른 라우터로 보냄   
> render: 템플릿 엔진 랜더링할 때 사용   

**next('route')**
 - 라우터에 연결된 나머지 미드웨어들을 건너뛰고 싶을 때 사용
```js
router.get('/', function(req, res, next) {
  next('route');
}, function(req, res, next) {
  console.log('실행되지 않습니다.');
  next();
}, function(req, res, next) {
  console.log('실행되지 않습니다.');
  next();
});

router.get('/', function(req, res) {
  console.log('실행됩니다');
  res.render('index', { title: 'Express'});
});
```

**/user/:id ?**
 - 라우터 주소의 특수한 패턴임
 - :id는 문자 그대로 의미하는 게 아님, 다른 값을 넣을 수 있음
 - req.params로 조회가능
```js
router.get('/users/:id', function(req, res) {
  console.log(req.params, req.query);
});

// /users/123?limit=5&skip=10
// { id: '123' }{ limit: '5', skip: '10' }
```
**router도 미들웨어... 즉 Express는 미들웨어가 핵심!!**

---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(총 1개의 프로젝트를 압축해서 Notion 페이지에 올려주세요!)

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)
