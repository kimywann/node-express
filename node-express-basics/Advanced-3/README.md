# MySQL workbench (테이블 생성, DB 연동, Timezone 설정)

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
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbGCJoa%2FbtsJAQ1rG4D%2F2OckAoZS5VKuNTLKkjIFGK%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FdlXDkx%2FbtsJCAbEixL%2FSkwaCgmJykDKAead5PuzvK%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FnH1zx%2FbtsJAL0frgv%2FWgRQnAv9PkBGNg8ikKjeZK%2Fimg.png)