# 데이터베이스 및 DBMS 핵심 정리

1. **DBMS** (Database Management System)
   - 데이터베이스를 관리하고 운영하는 소프트웨어.
   - 대표적인 DBMS: 오라클, MySQL, MariaDB.

2. **SQL** (Structured Query Language)
   - 데이터베이스에서 데이터를 생성, 조회, 수정, 삭제하기 위해 사용되는 언어.
   - 주요 명령어: 
     - **INSERT**: 데이터 삽입
     - **SELECT**: 데이터 조회
     - **UPDATE**: 데이터 수정
     - **DELETE**: 데이터 삭제

3. **RDBMS** (Relational Database Management System)
   - 데이터 간 **관계**를 관리하는 데이터베이스 시스템.
   - 테이블 간 **1:1, 1:N, M:N** 관계를 설정 가능.
   - **Primary Key (PK)**: 유일한 값을 가지는 키.
   - **Foreign Key (FK)**: 다른 테이블의 데이터를 참조하는 키.

4. **테이블과 관계**
   - 테이블은 정규화를 통해 중복 데이터를 최소화하고, 연관된 데이터를 연결.
   - 예: 사용자 1명과 게시글 여러 개의 관계는 **1:N** 관계.

5. **날짜와 시간 타입**
   - **DATE**: 날짜 (YYYY-MM-DD)
   - **DATETIME**: 날짜 + 시간 (YYYY-MM-DD HH:MM:SS)
   - **TIMESTAMP**: 시스템 시간대로 자동 입력.

6. **Auto Increment & Lock Mode**
   - **AUTO_INCREMENT_LOCK_MODE**: 데이터 삽입 실패 시에도 증가된 ID는 건너뛰며 새로운 값을 할당.

7. **기본 제약 조건**
   - **NOT NULL**: 값이 반드시 입력되어야 함.
   - **DEFAULT**: 값이 입력되지 않으면 기본값으로 설정.

### 테이블 간 관계 예시
- **게시글 vs 사용자**
   - 사용자 1명: 게시글 여러 개 → **1:N 관계**
   - 게시글 1개: 사용자 1명 → **1:1 관계**

***

## MySQL workbench (테이블 생성, DB 연동, Timezone 설정)
***

### DB 연동
```
const mysql = require('mysql2');

const connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password : '1234',
    database: 'Youtube',
    dateStrings : true
});

connection.query(
    'SELECT * FROM `users`',
    function(err, results, fields) {
        let {id, email, name, created_at} = results[0]; 
        console.log(id);
        console.log(email);
        console.log(name);
        console.log(created_at);
    }
);
```
***

### timezone 설정 결과 업로드
![@@global, @@session에 timezone이 값에 맞게 설정 되었는지 확인](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbGCJoa%2FbtsJAQ1rG4D%2F2OckAoZS5VKuNTLKkjIFGK%2Fimg.png)
***
![users 테이블에 created_at 시간이 timezone 적용 되었는지 확인](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdlXDkx%2FbtsJCAbEixL%2FSkwaCgmJykDKAead5PuzvK%2Fimg.png)
***
![로컬에서 DB 연동 후 호출했을 때 시간 적용 되는지 확인](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FnH1zx%2FbtsJAL0frgv%2FWgRQnAv9PkBGNg8ikKjeZK%2Fimg.png)