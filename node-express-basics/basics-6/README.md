# Node.js + express 기본

## 자바스크립트

데이터 타입이 동적으로 변환되는 특징이 있습니다.
이로 인해 문자열로 받은 숫자가 자동으로 int 값으로 해석되는 경우가 발생합니다.
이를 방지하거나 명시적으로 처리하기 위해 parseInt() 함수를 사용할 수 있습니다.

```
let number = parseInt(req.params.n)
console.log(number)

res.json({
num : number
})
```
 

 

## params, query
 

### Route Parameters (req.params)

- URL 경로(Route) 안에서 : 뒤에 오는 변수로 데이터를 전달.

- ex : https://example.com/users/123/posts/456

- 사용 ex : 특정 리소스에 대한 데이터 접근.

 

 

### Query Parameters (req.query)

- URL에서 ? 뒤에 위치하며, key=value 형태로 데이터를 전달.

- ex : https://example.com/search?query=javascript&sort=recent

- 사용 ex : 필터링, 정렬, 페이지네이션 등.

*****

 
## 자바스크립트 객체, 배열 비구조화
 

### 객체 비구조화

```
app.get('/watch', function (req, res) {

    // const q = req.query // q = {v:___, t: _____}
    // console.log(q.v)
    // console.log(q.t)

    //JS객체(JSON)의 비구조화
    const {v, t} = req.query
    console.log(v)
    console.log(t)

    res.json({
        video : v,
        timeline : t
    })
})
```

 

### 배열 비구조화

```
const array = [1, 2, 3, 4, 5]

const num1 = array[0]
const num4 = array[3]

console.log(num1) // 1
console.log(num4) // 4
```

⬇️

```
const array = [1, 2, 3, 4, 5]
const [, num2, num3, , num5] = array

console.log(num2) // 2
console.log(num3) // 3
console.log(num5) // 5
```

*****

## 자바스크립트의 네이밍 케이스 
 

### [kebab-case], cf. [snake_case] // 둘 중 어떤 것도 좋으니 "통일" 

폴더 ex. demo-api
파일명 ex. object_api_demo.js



=> 특징 :

        - 두 개 이상의 단어를 합쳐서 사용할 땐, 첫 번째 단어와 두 번째 단어 사이에 ' - ' 
        - 알파벳 소문자

 

### [camelCase]

변수, 함수 ex. channelTitle, videoNum 

 

=> 특징 : 두 개 이상 단어가 합쳐서 사용할 땐, 두 번째 단어의 첫 글자를 "대문자"로

 

### [PascalCase]

클래스 

*****

## 자바스크립트 Map (String) 

자바스크립트의 Map 객체는 키-값 쌍으로 데이터를 저장할 수 있는 구조를 제공합니다.
Map은 객체와 유사하지만, 일반 객체와 달리 모든 종류의 값(객체, 원시값 등)을 키로 사용할 수 있습니다.
또한, 삽입된 순서대로 요소를 순회할 수 있다는 특징이 있습니다.
```
let db = new Map()
db.set(1, "Notebook") // 키로 벨류를 찾을 수 있는 한 쌍을 저장
db.set(2, "Cup") 
db.set(3, "Chair") 

console.log(db)// Map(3) { 1 => 'Notebook', 2 => 'Cup', 3 => 'Chair' }
console.log(db.get(3)) // Chair
```

## 신입이 반드시 알아야 하는 자료구조  
Map / List

```
app.get('/:id', function (req, res){
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    if (db.get(id) == undefined) {
        res.json({
            message: "없는 상품입니다."
        }) 
    } else {
        res.json({
            id : id,
            productName : db.get(id)
        })
    }
})
```


*****

## express 실습

```
sudo npm i express-generator -g  
// Express 애플리케이션 생성을 위한 템플릿을 제공하는 express-generator를 전역으로 설치합니다. 

npm list express
// 현재 프로젝트의 node_modules 디렉토리에서 설치된 express 모듈을 확인하는 명령어입니다.

npm install express
// express 모듈을 프로젝트에 설치합니다. 이 명령어는 package.json에 명시된 express 버전을 설치합니다.

npm install
// 프로젝트의 package.json 파일에 정의된 모든 종속성을 설치합니다.
// 이 명령어는 node_modules 디렉토리에 필요한 모든 패키지를 설치합니다.

npm start
// package.json에 정의된 start 스크립트를 실행하여 애플리케이션을 시작합니다.
```

*****


## 자바스크립트 함수 4가지 종류

```
function add1 (x, y) {
    return x + y
}
`


let add2 = function (x, y) {
    return x + y
}

// 화살표 함수 (arrow function)
const add3 = (x, y) => {
    return x + y
}


var add4 = (x, y) => x + y


console.log(add1(2, 3)) // 5
console.log(add2(2, 3)) // 5
console.log(add3(2, 3)) // 5
console.log(add4(2, 3)) // 5
```

*****

## Express 구조 요약 정리

 

Express는 Node.js에서 서버 애플리케이션을 구축하기 위해 널리 사용되는 웹 프레임워크입니다. Express의 구조는 기본적으로 다음과 같은 주요 요소들로 구성됩니다.

 

1. 프로젝트 구조

 

- app.js 또는 server.js:

Express 애플리케이션의 진입점입니다. 서버를 설정하고 라우팅 및 미들웨어를 구성합니다.

 

- routes/:
URL 경로에 대한 라우팅 로직을 정의하는 파일들이 들어있는 디렉토리입니다. 예를 들어,
routes/index.js, routes/users.js 등으로 다양한 경로를 처리할 수 있습니다.

 

- views/:
템플릿 엔진을 사용하는 경우, HTML 파일들이 들어가는 디렉토리입니다. Pug, EJS 등의 템플릿 파일이 포함됩니다.

 

- public/:
정적 파일(CSS, JS, 이미지 등)이 저장되는 디렉토리입니다. Express는 이 디렉토리의 파일들을 클라이언트에 제공할 수 있도록 설정할 수 있습니다.

 

- node_modules/:
프로젝트에 설치된 npm 패키지들이 저장되는 디렉토리입니다.

 

- package.json:
프로젝트 메타데이터와 의존성 패키지를 관리하는 파일입니다. 스크립트 명령어, 프로젝트 이름, 버전 등을 정의합니다.

*****

### EADDRINUSE: address already in use 해결
포트 1234를 사용하려고 하는데, 이미 다른 프로세스가 이 포트를 사용 중이기 때문에 오류가 발생했습니다.

```
lsof -i :1234  
// 1234번 포트를 사용 중인 프로세스를 확인하는 데 사용됩니다. 

kill -9 <pid>   
// 확인한 PID를 사용하여, 1234번 포트를 점유하고 있는 프로세스를 강제로 종료합니다.
```

*****

## postman(get, post) 실습 

1. 기존 유튜버 정보만 반환할 수 있었고, 새로운 유튜버를 등록할 수가 없었습니다.
2. 1, 2, 3 유튜버에 대한 api

 
🔥 POST를 활용해서 유튜버를 추가 !


***


## <API 설계 (URL, method)>
 

### 0) 전체 유튜버 "조회" GET/youtubers

- req : X

- res : map을 전체 조회

  

### 1) 개별 유튜버 "조회" GET /youtubers/:id - id로 map에서 객체를 찾아서, 그 객체의 정보를 뿌려줌

- req : params.id <= map에 저장된 key을 전달

- res : map에서 id로 객체를 조회해서 전달

 

### 2) 유튜버 등록 => POST /youtubers

- req : body <= channelTitle, sub = 0, videoNum = 0 신규 유튜버 정보를 전달, db에 저장까지 해줌.

- res : channelTitle님, 유튜버 생활을 응원합니다. 

*****


## 등록 고도화
 
```
// 데이터 셋팅
let db = new Map()

db.set(1, youtuber1) // 키로 벨류를 찾을 수 있는 한 쌍을 저장
db.set(2, youtuber2) 
db.set(3, youtuber3)
```

⬇️

```
// 데이터 셋팅
let db = new Map()
let id = 1

db.set(id++, youtuber1) // 키로 벨류를 찾을 수 있는 한 쌍을 저장
db.set(id++, youtuber2) 
db.set(id++, youtuber3)
```

 

### var이 존재하는 이유

포스트맨으로 POST 유튜버 등록을 하고났는데 유튜버가 4번까지밖에 등록되지 않는 상황
let의 블록스코프 영향으로 등록이 안됨, 아래 방법으로 해결

 
```
var id = 1 // 위 코드에서 let을 var로 수정
```