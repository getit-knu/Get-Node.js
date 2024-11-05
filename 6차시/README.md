# GET IT SW교육 Node.js
## 6차시_MySQL 설치하기, MySQL 이해하기
> 기간 : 24.11.06~24.11.10

### 🗂️6차시 자료🗂️
[Node.js 6차시 자료(MySQL 설치하기)](https://github.com/getit-knu/Get-Node.js/blob/main/6%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%206%EC%B0%A8%EC%8B%9C_%EC%84%A4%EC%B9%98.pdf)

[Node.js 6차시 자료(MySQL 이해하기)](https://github.com/getit-knu/Get-Node.js/blob/main/6%EC%B0%A8%EC%8B%9C/GETIT%205%EA%B8%B0%20SW%20Node%EA%B5%90%EC%9C%A1%206%EC%B0%A8%EC%8B%9C.pdf)

👉자료를 다운 받은 후 수강해주세요!

### 🎞️6차시 강의🎞️

[![Video Label](http://img.youtube.com/vi/t-frczEDS8I/0.jpg)](https://youtu.be/t-frczEDS8I)

[![Video Label](http://img.youtube.com/vi/WKGrgNTZWIM/0.jpg)](https://youtu.be/WKGrgNTZWIM)

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
#### 01. 데이터베이스 및 테이블 생성하

---

### 📢과제
##### 강의 중 진행한 실습 노션에 업로드하기
(총 1개의 프로젝트를 압축해서 Notion 페이지에 올려주세요!)

👉 6기 노션 > SW과제제출 > Node.js > 해당 차시 > 본인이름 > 과제제출란

👉 [6기 SW 과제제출 링크](https://www.notion.so/SW-8502eeef321b43e2ad13ece0f626be33)
