# user.js channel.js 리팩토링

```
userId && conn.query(sql, userId, function())// 단축 평가
```
---
변수명에는 __userId__, 필드명에는 __user_id__ 다른 이유? 

• __userId:__ JavaScript 코드에서 사용되는 변수명 (camelCase).
• __user_id:__ SQL에서 사용되는 데이터베이스 필드명 (snake_case).
• __값:__ 동일한 값이 사용되며, 데이터베이스 필드에 맞게 매핑됩니다.

즉, 변수명은 자바스크립트의 코딩 스타일을 따르고, 데이터베이스 필드명은 데이터베이스 설계 스타일을 따르지만, 둘이 연결되는 값은 동일합니다.

---
## [과제] user.js 코드 리펙토링 결과 업로드
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbwEXAp%2FbtsJIpPR98X%2F1MGkaiUjKbxYcjWrM7Uk1K%2Fimg.png) 
---

<details>
    <summary> MySQL 워크벤치 연결 트러블슈팅</summary>

__문제 정의__

MySQL 워크벤치를 실행했을 때, 스키마가 표시되지 않는 문제가 발생했습니다. 확인해보니 docker 컨테이너가 실행되고 있지않은걸 확인하고는 실행하였더니 데이터베이스에 연결하려고 할 때 다음과 같은 오류 메시지가 나타났습니다

(HTTP code 500) server error - Ports are not available: 
exposing port TCP 0.0.0.0:3306 -> 0.0.0.0:0: listen tcp 0.0.0.0:3306: 
bind: address already in use
 

__원인 추론__


1. MySQL 워크벤치 삭제

• 기존의 MySQL 워크벤치를 삭제하여 문제를 일으키는 소프트웨어를 제거했습니다.

 

2. 3306 포트의 PID 확인 및 종료

• lsof 명령어를 사용하여 3306 포트를 사용하는 프로세스를 확인했습니다.

sudo lsof -i :3306
 

__문제 해결__

• 확인한 PID를 사용하여 프로세스를 종료했습니다.

sudo kill -9 <PID>
 

3. Docker 컨테이너 실행

• Docker를 사용하여 MySQL 컨테이너를 실행하고, 3306 포트를 바인딩하여 데이터베이스 서버를 구동했습니다.

 

4. MySQL 워크벤치 재설치

• MySQL 워크벤치를 새로 설치하여 최신 버전을 사용했습니다.

 

5. 연결 확인

• 재설치 후, MySQL 워크벤치에서 Docker 컨테이너에 정상적으로 연결할 수 있음을 확인했습니다.

 

__결과 기록__

위의 과정을 통해 MySQL 워크벤치의 연결 문제를 해결할 수 있었습니다. 3306 포트의 충돌 문제를 해결하고, Docker를 활용하여 간편하게 MySQL 서버를 운영할 수 있었습니다. 이 경험을 통해 데이터베이스 관리와 서버 운영의 중요성을 다시 한번 깨닫게 되었습니다.

</details>
