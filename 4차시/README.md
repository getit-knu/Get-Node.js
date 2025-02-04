# GET IT SW교육 Node.js
## 4차시_http 모듈로 웹 서버 만들기
> 기간 : 24.10.06~24.10.09

### 🗂️4차시 자료🗂️
[Node.js 4차시 자료](https://github.com/getit-knu/Get-Node.js/blob/main/3%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%203%EC%B0%A8%EC%8B%9C.pdf)

👉자료를 다운 받은 후 수강해주세요!

### 🎞️4차시 강의🎞️
[![Video Label](http://img.youtube.com/vi/vYOBr20ITYM/0.jpg)](https://www.youtube.com/watch?v=XXExKn4O3GE)

👉위 이미지를 누르면 강의를 들을 수 있습니다.

---

### 🚀목차🚀
1. 요청과 응답 이해
2. 쿠키와 세션 이해
3. https와 http2
4. cluster

---

#### 01. 🌿 요청과 응답 이해
##### 1. 클라이언트와 서버
**요청과 응답을 보내는 관계**   
    > Client가 HTTP request를 보내면, Server는 해당 request를 처리 후 HTTP response를 돌려줌   
    > 서버에는 요청을 받는 부분, 응답을 보내는 부분이 있어야 함

##### 2. 요청과 응답
**이벤트리스너를 등록해서 요청이 오면 처리**
  > http.createServer(콜백)
  >> 콜백의 매개변수 req(요청), res(응답) & listen, on, error 메서드   
  >> html 코드를 적는 것보다 파일을 보내보는게 더 효율적

```js
const http = require('http');
const fs = require('fs');

http.createServer((req, res) => {
    fs.readFile('./4-1-3.html', (err, data) => {
        if(err) {
            throw err;
        }
        res.end(data);
    });
}).listen(8081, () => {
    console.log('http://localhost:8081/');
    console.log('8081번 포트에서 서버 대기 중입니다.');
});
```

**서버가 모든 클라이언트에게 같은 응답을 줌 - 서버가 클라이언트별로 다르게 응답하는 법을 알아보자**

---

#### 02. 쿠키와 세션 이해
##### 1. 필요성
**클라이언트에서 요청을 보낼 때 누가 보냈는지 알수가 없음**   
**-> 따라서 로그인 서비스가 필요하며, 쿠키와 세션의 개념 이해가 필요**

##### 2. 쿠키
**클라이언트가 누군지 알기 위해 서버가 지속적으로 보내는 것**

   > cookie
   >> 단순 '키-값' 형태의 쌍, 요청과 응답의 헤더에 저장

   > 서버의 역할
   >> 클라이언트로 쿠키를 보내기
```js
res.writeHead(200, {'Set-Cookie' : 'mycookie=test'});
```

   > HTTP 상태코드
   >> 2xx: 성공 (200, 201)   
   >> 3xx: 리다이렉션 (301, 302)   
   >> 4xx: 요청 오류 (401, 403, 404)   
   >> 5xx: 서버 오류 (500, 502, 503)   

   > 서버의 역할2
   >> 받은 쿠키로 사용자 확인하기
```js
res.writeHead(200, {'Content-Type': 'text/html; charset=utf-8'});
res.end(`${cookies.name}님 안녕하세요`);
```

   > cookie 옵션
   >> 쿠키명 = 쿠키값: 기본적인 쿠키의 값   
>   > Expires = 날짜: 만료 기한 -> 기한 지나면 쿠키 제거   
>   > Max-age = 초: Expire와 비슷, 날짜 대신 초 입력, 해당 초 지나면 만료, Expires 보다 우선   
>   > Domain = 도메인명: 쿠키가 전송될 도메인을 특정할 수 있음   
>   > Path = URL: 쿠키가 전송될 URL을 특정할 수 있음, 기본값 '/'   
>   > Secure: HTTPS일 경우에만 쿠키 전송   
>   > HttpOnly: 설정 시 js에서 쿠키에 접근 못함, 쿠키 조작 방지를 위해 설정

**현재 방식은 쿠키가 노출되므로 세션을 활용해서 사용자 정보 관리해보자**

##### 3. 세션
**서버에 사용자 정보를 저장하고 클라이언트와는 세션 아이디로만 소통**

```js
res.writeHead(200, {'Content-Type': 'text/html; charset=utf-8'});
res.end(`${session[cookies.session].name}님 안녕하세요`);
```

**최종 코드**
```js

const http = require('http');
const fs = require('fs');
const url = require('url');
const qs = require('querystring');
const { error } = require('console');

const parseCookies = (cookie = '') =>
    cookie
        .split(';')
        .map(v => v.split('='))
        .map(([k, ...vs]) => [k, vs.join('=')])
        .reduce((acc, [k, v]) => {
            acc[k.trim()] = decodeURIComponent(v);
            return acc;
        }, {});

const session = {}; // 세션을 저장할 객체

http.createServer((req, res) => {
    const cookies = parseCookies(req.headers.cookie);
    if(req.url.startsWith('/login')) { //  주소가 /login으로 시작하는 경우
        const { query } = url.parse(req.url); // url을 파싱 query를 가져옴
        const { name } = qs.parse(query); // querystring을 객체로 변환
        const expires = new Date(); // 현재 시간
        expires.setMinutes(expires.getMinutes() + 5); // 현재시간 + 5분 -> 만료시간
        const randomInt = +new Date(); // 세션에 사용할 랜덤 숫자
        session[randomInt] = {
            name,
            expires,
        };
        res.writeHead(302, {
            Location: '/',
            'Set-Cookie': `session=${randomInt}; Expires=${expires.toGMTString()}; HttpOnly; Path=/`,

        });
        res.end();
    } else if(cookies.session && session[cookies.session].expires > new Date()) { // 쿠키가 있는 경우, 만료 기간이 지나지 않았다면
        res.writeHead(200, {'Content-Type': 'text/html; charset=utf-8'});
        res.end(`${session[cookies.session].name}님 안녕하세요`);
    } else { // 쿠키가 없는 경우
        fs.readFile('./4-2-2.html', (err, data) => {
            if (err) {
                throw err;
            }
            res.end(data);
        });
    }
}).listen(8084, () => {
    console.log('http://localhost:8084/');
    console.log('8084번 포트에서 서버 대기 중입니다.');
});
```

---

#### 03. https와 http2
##### 1. https
**http + SSL 암호화**   
**요청에 오고 가는 데이터 암호화 필수, 암호화 적용을 위해 인증해줄 수 있는 기관도 필요**

##### 2. http2
**multiplexing 사용해 효율적인 요청 방식**

---

#### 04. cluster
##### 1. cluster
**싱글 스레드인 노드가 CPU 코어를 모두 사용할 수 있게 해줌**

```js
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length; // CPU 개수 구하기

if(cluster.isMaster){ // 마스터 프로세스일 경우
    console.log(`마스터 프로세스 아이디: ${process.pid}`);
    // CPU 개수만큼 워커를 생산
    for(let i=0; i<numCPUs; i++) {
        cluster.fork();
    }
    // 워커가 종료되었을 때
    cluster.on('exit', (worker, code, signal) => {
        console.log(`${worker.process.pid}번 워커가 종료되었습니다.`);
        console.log('code', code, 'signal', signal);
        cluster.fork(); // 워커가 종료되면 다시 하나 생성
    })
} else {
    // 워커들이 포트에서 대기
    http.createServer((req, res) => {
        res.write('<h1>Hello Node1</h1>');
        res.end('<p>Hello Cluster!</p>');
        setTimeout(() => { // 워커 존재 여부 확인을 위해 1초마다 강제 종료
            process.exit(1);
        }, 1000);
    }).listen(8085);

    console.log(`${process.pid}번 워커 실행`);
}
```

**포트를 공유하는 노드 프로세스를 여러 개 둘 수 있음 -> 즉, 병렬 처리 가능!!!**
---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(총 2개의 html 파일과 7개의 js 파일들을 압축해서 Notion 페이지에 올려주세요!)

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)

##### 강의 중 진행한 실습 노션에 업로드하기
(총 2개의 html 파일과 7개의 js 파일들을 압축해서 Notion 페이지에 올려주세요!)

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)
