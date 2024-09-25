# 인증과 인가, JWT

• 인가(Authorization): JWT로 로그인된 사용자에게 특정 자원에 접근할 수 있는 권한을 부여하는 것.

• 인증(Authentication): 사용자가 본인이 맞는지(로그인 여부)를 확인하는 것.

---

### 쿠키 vs 세션 vs JWT
 
#### __#1 cookie란,__

- 웹에서 서버와 클라이언트가 주고받는 데이터 중 하나

- 정확히는 생성은 웹 서버가 해서 웹 브라우저 주면, 브라우저가 자기 메모리에 저장해두고, 다음에 같은 웹서버 방문할 때 쿠키 들고 요청하러 감.


장점 : 서버가 저장 X => 서버 저장 공간, HTTP, Stateless => RESTful

단점 : 보안 취약
 
***

#### __#2 Session이란,__

- 쿠키에 중요한 정보를 담지 말고, 중요한 정보는 서버에 저장해두고 그 정보가 어딨는지 주소만 적어서 쿠키에 담다.

- 쿠키에 넣어서 보내기엔 너무 중요한 내용은 서버가 가진 금고에 넣어두고 그 금고 번호만 쿠키에 넣어서 통신함.


1) 로그인 -> 서버가 금고 만든후, 정보 저장 그 금고 번호를 줍니다.

2) 사용자 <-> 서버가 번호만 가지고 대화


장점 : 보안 비교적 쿠키 보다 상승

단점 : 서버가 저장 => 서버 저장 공간, Stateless X

 

***

### __#3 🚀 JWT (JSON Web Token)__

 

개념
JSON 형태의 데이터를 웹에서 안전하게 전송하기 위한 웹에서 사용하는 토큰

= 토큰을 가진 사용자가 "증명"을 하기 위한 수단

cf. 토큰 : (인증용) 입장 가능한 유저 / 관리자 권한&일반 유저 권한

 
장점
- 보안에 강하다! <= "암호화"가 되어 있다.

- HTTP 특징을 잘 따랐다. = Statelesss하다 <= "서버가 상태를 저장하지 않습니다."

- 서버 부담도 줄이며, cf. 토큰을 발행하는 서버를 따로 만들어줄 수 있음.

---

## [과제] JWT 구조 및 인증 절차 내용 정리
### JWT 구조 
 

__Header (헤더)__

 

- 헤더는 JWT의 타입("typ": "JWT")과 암호화 알고리즘("alg": "HS256") 정보를 담고 있습니다.

 

__Payload (페이로드)__

 

- 페이로드에는 인증된 사용자에 대한 정보가 포함됩니다. 사용자의 이름, 이메일과 같은 데이터를 담을 수 있지만, 보안상 **민감한 정보(비밀번호)**는 포함하지 않습니다.


- 페이로드에는 또한 토큰의 유효 기간, 발행 시간 등의 정보를 담을 수 있습니다.

 

__Signature (서명)__

 
- 서명은 헤더와 페이로드를 암호화한 값입니다. 주어진 시크릿 키로 헤더와 페이로드를 결합하여 해싱한 결과로 생성됩니다.

- 이 서명을 사용하면 토큰이 중간에 변조되지 않았다는 것을 보장할 수 있습니다.

---

### JWT로 인증/인가 하는 절차
 

JWT는 사용자가 로그인할 때 생성되어, 이후 요청마다 포함되어 인증을 진행합니다.

 

__로그인 요청__

1. 사용자가 로그인을 시도합니다. 클라이언트에서 로그인 요청을 보낼 때, username과 password가 함께 POST 요청으로 서버에 전송됩니다: 요청: POST /login

 

__서버에서 로그인 처리__

2. 서버는 받은 사용자 정보를 확인하고, 로그인 성공 시 JWT를 생성합니다.

- 로그인 성공 시점에 서버는 JWT를 발행하고, 이 토큰은 헤더, 페이로드, 서명을 포함합니다.

- 서버는 사용자에게 발급된 JWT를 응답으로 보냅니다.

 

__JWT를 클라이언트에 전달__

3. 클라이언트는 서버에서 받은 JWT를 저장합니다. 이 토큰은 보통 로컬 스토리지 또는 세션 스토리지에 저장됩니다.

 

__인증된 요청__

4. 사용자는 이후 인증이 필요한 요청을 할 때마다 JWT를 HTTP 요청의 헤더에 포함시켜 서버에 보냅니다.

 

__서버에서 JWT 검증__

 

5. 서버는 클라이언트로부터 받은 JWT의 서명을 검증하여 해당 사용자가 인증된 유저인지 확인합니다.

- JWT의 서명을 확인하여 토큰이 중간에 변조되지 않았는지 검증하고, 토큰의 유효성(유효 기간 등)을 체크합니다.

- 서명이 일치하고 유효 기간이 남아있으면 요청을 허용합니다.

- 서명이 틀리거나 유효하지 않은 JWT일 경우, 요청을 거부합니다.

 
---

__절차 흐름__

 

1. 로그인 요청: 클라이언트 → 서버로 POST /login 요청 (ID, 비밀번호 포함).

2. 서버에서 검증 후 JWT 발급: 로그인 성공 시 JWT 발행.

3. JWT 전달 및 저장: 클라이언트는 발급받은 JWT를 로컬 스토리지나 세션 스토리지에 저장.

4. 인증된 요청: 클라이언트가 이후 인증 요청 시 Authorization: Bearer <JWT> 형태로 헤더에 포함해 서버에 요청.

5. JWT 검증: 서버는 서명과 토큰 유효성을 검증한 뒤, 유효하면 요청을 처리.

---

### .env (environment: 환경 변수 '설정 값' )

__개념__

: 개발을 하다가 포트넘버, 데이터베이스 계정, 암호키, ... 등등   cf. 깃허브에 올라가면 안되는 값

// 외부에 유출되면 안되는 중요한 환경 변수 따로 관리 하기위한 파일

파일 확장자가 : .env

.env 파일은 환경 변수 파일 -> 프로젝트 최상위 패키지에 존재

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbEPHl8%2FbtsJMgROPXp%2FjbZFS7JhcOZaJxcCEBWfP0%2Fimg.png)
__.env 사용하는 코드__
 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbTr0B1%2FbtsJMDMLuKn%2FNhsCLBE8djz38fwIiSQGw0%2Fimg.png)

__최상위 패키지에 .env 파일 생성__
 

```
// .env 파일

PRIVATE_KEY = '' # JWT 암호키 
PORT = '' # express 포트넘버
```

.env 파일에서 환경 변수는 보통 스네이크 케이스(Snake Case)로 설정하는 것이 관례입니다.

스네이크 케이스는 모든 글자를 대문자로 쓰고, 단어 사이를 **밑줄(_)**로 구분합니다.

---


### Secure
 

__HTTP__

- HTTP는 웹에서 데이터를 전송하는 프로토콜로, 클라이언트(브라우저)와 서버 간의 요청 및 응답을 처리합니다.
- HTTP는 기본적으로 데이터를 암호화하지 않습니다. 따라서 중간에 공격자가 데이터를 가로채거나 수정할 수 있습니다. 이로 인해 사용자 개인정보나 중요한 정보가 노출될 수 있습니다.

• 빠른 속도: 암호화가 없기 때문에 데이터 전송 속도가 빠릅니다.

• 신뢰성 부족: 데이터가 안전하게 전송되지 않기 때문에 보안에 취약합니다.



__HTTPS__

- HTTPS는 HTTP의 보안 버전으로, SSL(Secure Sockets Layer) 또는 TLS(Transport Layer Security) 프로토콜을 사용하여 데이터를 암호화합니다. 이를 통해 클라이언트와 서버 간의 안전한 통신을 보장합니다.
- HTTPS는 데이터를 암호화하여 전송하므로, 중간에 데이터가 가로채지거나 수정되는 것을 방지합니다. 이를 통해 사용자의 개인정보 및 중요한 데이터의 안전성이 확보됩니다.


• 데이터 암호화: 전송되는 데이터가 암호화되므로 보안성이 높습니다.

• 신뢰성: HTTPS를 사용하는 사이트는 일반적으로 신뢰할 수 있으며, 사용자에게 안전한 환경을 제공합니다.

• SEO(검색 엔진 최적화): Google과 같은 검색 엔진은 HTTPS를 사용하는 웹사이트의 순위를 높이는 경향이 있습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbBMvmy%2FbtsJNiBp0SY%2FNKJHGNAmRwAdPkOKRdIhoK%2Fimg.png)
 

cf. httpOnly(= 프론트엔드가 아니라 API 호출만 허락) : XSS 공격 (프론트엔트 공격 : 웹 브라우저 js 접근 => 공격) 

HttpOnly true =>

 

__httpOnly__ 플래그는 웹 애플리케이션에서 쿠키의 보안을 강화하기 위해 사용되는 중요한 옵션입니다. 이 플래그를 설정하면, 해당 쿠키는 JavaScript에서 접근할 수 없게 되어 XSS(교차 사이트 스크립팅) 공격으로부터 보호할 수 있습니다.

 

 
__jwt 유효기간 설정__

## 금일 실습한 쿠키 설정 및 JWT 유효기간 설정 등 코드 결과물을 깃헙에 업로드 한 뒤 링크를 공유 하시오.

 

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLdiRT%2FbtsJMErpj3H%2FCmBUkIRLNftC0RPvDpzYsK%2Fimg.png)
__jwt 유효기간 설정__
 


![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FceNqkj%2FbtsJMi9YEKx%2FP36U4Ddnxa1RUWCpf8mjSK%2Fimg.png)

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbVElwm%2FbtsJLzZaejw%2FGxb4b5Sywfqmku1ACvKimk%2Fimg.png)
__유효기간 설정 후 exp가 생겼습니다.__