# GET IT SW교육 Node.js
## 3차시_Node 기능 알아보기
> 기간 : 24.10.02~24.10.05

### 🗂️3차시 자료🗂️
[Node.js 3차시 자료]()

👉자료를 다운 받은 후 수강해주세요!

### 🎞️3차시 강의🎞️

👉위 이미지를 누르면 강의를 들을 수 있습니다.

---

### 🚀목차🚀
1. Node 내장 객체
2. Node 내장 모듈
3. 파일 시스템 접근
4. 이벤트 이해하기

---

#### 01. 🌿 Node 내장 객체
##### 1. console
   > console.time(), console.timeEnd()
   >> 코드 실행 시간 측정

   > console.log()
   >> 평범한 로그, 쉼표로 구분해 여러 값 출력 가능!

   > console.error()
   >> 에러 메시지

   > console.dir()
   >> 객체 출력할때 사용

   **보통 디버깅을 위해 사용**

##### 2. 타이머
   > setTimeout
   >> 정해진 시간(ms) 후에 실행

   > setInterval
   >> 정해진 시간(ms)마다 반복 실행

   > setImmediate
   >> 즉시 실행

   > clearImmediate
   >> Immediate 실행 못하게 막음

##### 3. __filename, __dirname
   > __filename
   >> 파일의 이름

   > __dirname
   >> 파일의 디렉토리

##### 4. process
**현재 실행되고 있는 Node 프로세스에 대한 정보**
   > version, arch, platform, pid, uptime(), execPath, cwd(), capUsage()

   > process.env
   >> 시스템 환경 변수  
   >> 주로 서비스의 중요한 키를 저장하는 공간 ( ex) db 비밀번호, api 키, ip 주소 등 ) 

   > process.nextTick(콜백)
   >> 이벤트 루프가 nextTick 안에 있는 콜백 함수를 우선으로 처리해줌

   > process.exit()
   >> 강제 종료  
   >> 0이면 정상 종료  
   >> 1이면 비정상 종료  

---

#### 02. Node 내장 모듈
##### 1. 모듈
   > **특정한 기능을 하는 함수나 변수들의 집합**
   >> 장점: 재사용 가능, 관리하기 편함  
   >> 단점: 여러개가 서로 엮이면 관리하기 어려워짐

   > module.export / exports 로 모듈 만들 수 있음
   >> 서로 같은 객체를 참조  
   >> exports는 객체만 가능 (함수 불가능!)  

##### 2. path
   > **폴더와 파일의 경로를 쉽게 조작하도록 도와주는 모듈**

   > path.sep
   >> 경로의 구분자 (Windows 는 \, POSIX는 /

   > path.delimiter
   >> 환경 변수의 구분자  
   >> process.env.PATH를 입력하면 여러 개의 경로가 이 구분자로 구분되어 있다.  
   >> Windows는 ;, POSIX는 :

   > path.dirname()
   >> 파일이 위치한 폴더 경로

   > path.extname()
   >> 파일의 확장자

   > path.basename()
   >> 파일의 이름(확장자 포함)

   > path.parse()
   >> 파일 경로를 root, dir, base, ext, name으로 분리

   > path.format()
   >> path.parse()한 객체를 파일 경로로 합침

   > path.normalize()
   >> 파일 경로 중에 잘못한거 있어도 고쳐줌

##### 3. url
   > **인터넷 주소를 쉽게 조작하도록 도와주는 모듈**

   > url.parse()
   >> url 문자열을 입력하면 여러 요소로 분리하여 객체 반환

   > url.format()
   >> parse된 url 객체를 다시 문자열로 조합하여 반환

   > querystring.parse()
   >> parse된 url 객체 중 query 부분을 한번 더 분해하여 이를 요소로 객체 반환

   > whatwg 방식
   >> protocol과 host에 origin 속성 가짐

   > serchParams 메소드
   >> url 객체를 key와 value로 다룰 수 있음

   > searchParams.getAll()
   >> 해당 요소의 모든 값을 불러옴

   > searchParams.get()
   >> 해당 요소의 값을 불러옴

   > searchParams.has()
   >> 해당 요소에 값이 있는지를 알려줌(true, false)

   > searchParams.keys()
   >> key들을 반환

   > searchParams.values()
   >> value들을 반환

   > searchParams.append(key, value)
   >> 해당 key와 value를 추가

   > searchParams.set(key, value)
   >> 해당 key의 값을 value로 설정

   > searchParams.delete(key)
   >> 해당 key를 삭제

   > searchParams.toString()
   >> url 객체를 다시 문자열로 만들어서 반환

##### 4. crypto
   > **암호화를 위한 모듈**
   > 단방향 암호화
   >> 복호화할 수 없는 암호화 방식  
   >> 원래 문자열(키)를 찾을 수 없음  
   >> 해시 기법 사용
 
   > 양방향 암호화
   >> 키를 사용하므로 복호화 가능

---

#### 3. 파일 시스템 접근
##### 1. 파일 읽기, 쓰기
   > fs.readFile('파일경로', (err, data) => {})
   >> 해당 파일경로의 data를 가져옴  
   >> err로 에러 처리

   > fs.writeFile('파일경로', '적을내용', (err) => {})
   >> 해당 파일경로로 파일 생성 후 내용 작성 
   >> err로 에러 처리

##### 2. 버퍼
   > **메모리에 저장된 데이터**
   >> 원본 데이터를 다른 곳으로 전송할 때 버퍼의 크기만큼 메모리를 옮김

   > Buffer.form()
   >> 문자열을 버퍼로 변환
   
   > .length
   >> 버퍼의 길이

   > .toString()
   >> 버퍼를 문자열으로 반환

##### 3. 스트림
   > **버퍼를 나눠서 보내는 법**
   >> 버퍼를 작게 쪼개서 여러번 전송  
   >> 한번에 보내는 것보다 빠르게 전송 가능

   > fs.createReadStream('파일경로', { highWaterMark : 16 })
   >> stream을 이용하여 파일을 읽음  
   >> highWaterMark: chunk의 크기 (chunk는 잘게 쪼개진 조각)


   > fs.createWriteStream('파일경로')
   >> stream을 이용하여 파일을 생성

   > .write('적을내용')
   >> 파일에 내용 작성

##### 4. pipe
   > **스트림을 연결해주는 pipe, 파일을 읽고 바로 전달받아 쓸 수 있도록 해줌**

   > readStream.pipe(writeStream)
   >> readStream에서 읽은 내용을 writeStream에서 받은 파일경로에 작성

##### 5. zlib
   > **파일 압축을 위한 내장 모듈**

   > zlib.createGzip()
   >> 압축 stream 생성  

   > readStream.pipe(zlibStream).pipe(writeStream)
   >> readStream에서 읽은 내용을 writeStream에서 받은 파일경로에 zlibStream을 이용하여 압축

---

#### 04. 이벤트 이해하기
##### 1. 이벤트
   > **이벤트를 만들고, 호출하고, 삭제해보자!!**

   > const myEvent = new Event();
   >> 이벤트 객체 생성

   > myEvent.addListener('이벤트 이름', () => { 이벤트 내용 })
   >> 이벤트 추가

   > myEvent.emit('이벤트 이름')
   >> 해당 이벤트 발동

   > myEvent.once('이벤트 이름', () => { 이벤트 내용 })
   >> 이벤트 추가 (한 번만 발동가능)

   > myEvent.removeAllListeners('이벤트 이름')
   >> 해당 이벤트 삭제

   > myEvent.on('이벤트 이름', 함수)
   >> 이벤트 추가, 이벤트 내용을 함수를 불러와서 사용가능

---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(총 3개의 txt 파일과 23개의 js 파일들을 압축해서 Notion 페이지에 올려주세요!)

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)