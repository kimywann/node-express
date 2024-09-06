# Node.js + Express 기본(8)

### 핸들러(handler)란?
HTTP request가 날아오면 자동으로 호출되는 메소드


__노드 : 콜백함수로, app.HTTPMETHOD(path, ... handler)__

```
// 개별 유튜버 DELETE
app.delete('/youtubers/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    let youtuber = db.get(id)
    if (youtuber == undefined) {
        res.json({
            message : `요청하신 ${id}번은 없는 유튜버입니다.`
        })
    } else {
        const channelTitle = youtuber.channelTitle
        db.delete(id)

        res.json({
            message : `${channelTitle}님, 아쉽지만 우리 인연은 여기까지 입니다.`
        })
    }
})
```

⬇️ (리팩토링)

```
// 개별 유튜버 DELETE
app.delete('/youtubers/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    let youtuber = db.get(id)
    if (youtuber) {
        const channelTitle = youtuber.channelTitle
        db.delete(id)

        res.json({
        message : `${channelTitle}님, 아쉽지만 우리 인연은 여기까지 입니다.`
        })
    } else {
            res.json({
                message : `요청하신 ${id}번은 없는 유튜버입니다.`
            })
    }
})
```

부정문이 보통 else 쪽으로 와야 전체적으로 이해하기 쉽고 코드의 가독성도 높아집니다.
위 코드는 그 결과를 보여줍니다.

*****

Json array, find(), 예외처리
 

__과일 전체 조회__

```
// 과일 전체 조회
app.get('/fruits', (req, res) => {
    res.json(fruits) // json array
})
```
![json array](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FypJCz%2FbtsJtyyiksC%2FjP1yCUAGbWgPpW8ynA8Uj1%2Fimg.png)



__과일 개별 조회__

```
// 과일 개별 조회
app.get('/fruits/:id', (req, res) => {
    let id = req.params.id
    let fruit = fruits[id]

    res.json(fruit) 
})
```

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbzDy7y%2FbtsJtGbNPFx%2FbtrqcGpJ6ykJKgamx9zhOK%2Fimg.png)

localhost:1234/fruits/2
:id값을 2로 호출하였는데 위 사진을 보면 id 값이 3이 반환되는 문제를 발견했습니다.


이러한 문제는,

![json array](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FypJCz%2FbtsJtyyiksC%2FjP1yCUAGbWgPpW8ynA8Uj1%2Fimg.png)

배열로 감싸진 JSON 데이터를 다룰 때 __배열의 인덱스__ 와 __id__ 값을 혼동해서 발생하는 것으로 보입니다.

이 상황은 let id = req.params.id 부분에서 발생합니다.


req.params.id는 URL 파라미터로 받은 값을 __문자열__ 로 전달하며, 이 값을 그대로 배열의 인덱스로 사용하게 됩니다. 배열 인덱스는 0부터 시작하지만, 과일 id는 1부터 시작할 가능성이 큽니다. 그래서 id 값과 배열의 인덱스가 불일치하게 됩니다.



### 해결방안 
 

1.

```
// 과일 개별 조회
app.get('/fruits/:id', (req, res) => {
    let id = req.params.id
    let fruit = fruits[id-1]

    res.json(fruit) 
})
```

2.

```
// 과일 개별 조회
app.get('/fruits/:id', (req, res) => {
    let id = req.params.id
    // let fruit = fruits[id-1]

    let findFruit = ""

    fruits.forEach(function(fruit) {
        if (fruit.id == id) {
            findFruit = fruit
        }
    })

    res.json(findFruit) 
})
```

3.

```
app.get('/fruits/:id', (req, res) => {
    let id = req.params.id;
    let findFruit = fruits.find(fruit => fruit.id == id);
    res.json(findFruit);
});
```


### 예외처리


__HTTP 상태 코드__

- 찾는 페이지 (리소스) 없음 (url에 맞는 api 없음) : __404__ 

 

status를 404 값을 줘서 예외처리를 할 수 있습니다. => __클라이언트와 소통을 정확하게 하기 위함__

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzaZCw%2FbtsJsjWyFHV%2FiSnZnr4bE5TdlefMrroES0%2Fimg.png)

```
// 과일 개별 조회
app.get('/fruits/:id', (req, res) => {
    let id = req.params.id;
    let findFruit = fruits.find(fruit => fruit.id == id);
     // fruits 배열 안에 있는 객체 중, id 값이 params.id링 같은 객체
    
    if(findFruit) {
        res.json(findFruit);
    } else { // 예외를 처리한다 = http status code 성공 X 실패 !
        res.status(404).send(
            "전달주신 id로 저장된 과일이 없습니다."
        )
    }
});
```

*****
#### " == vs === "
 

__차이점__

" == " 와 달리 " ===" 은 자료형까지 같은지 확인합니다.

*****

## YouTuber demo 예외 고도화 : map은 undefined가 아닙니다.
 
```
// 전체 조회 GET
app.get('/youtubers', (req, res) => {
    
    let youtubers = {}
    if (db) {
        db.forEach(function (value, key) {
            youtubers[key] = value
        });
        res.json(youtubers)
        
    } else {
        res.status(404).json({
            message : "조회할 수 있는 유튜버가 없습니다."
        })
    }
})
```

예외 고도화를 하기 위해서 리팩토링을 해주었는데,
postman에서 전체 유튜버를 삭제한 후 전체 유튜버 조회를 했더니



__404 오류 메세지가 호출되는 것이 아닌 200 OK 상태 코드가 반환되고 빈  json 객체 {}가 나오는 것을 보았습니다.__

 


__상황 분석__

- __Map(0) {}__ 가 출력되는 것은 db에서 모든 유튜버가 삭제되어, 빈 Map 또는 빈 객체 상태임을 나타냅니다.
- 하지만 빈 객체를 반환하는 것은 데이터가 없어도 여전히 정상적인 요청 처리로 간주되어 200 OK 상태 코드가 반환된 것입니다.



__해결방안__

```
// 전체 조회 GET
app.get('/youtubers', (req, res) => {
    
    let youtubers = {}
    if (db.size !== 0) {
        db.forEach(function (value, key) {
            youtubers[key] = value
        });
        res.json(youtubers)

    } else {
        res.status(404).json({
            message : "조회할 수 있는 유튜버가 없습니다."
        })
    }
})
```

db.size가 0이면 else로 호출 되게끔 수정하였습니다.

정상적으로 출력되는 것을 볼 수 있었습니다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F9lijB%2FbtsJt44BkBh%2F1274hxIBz1UQKaRD9sCj41%2Fimg.png)


### YouTuber demo 예외 고도화 : post

```
// 유튜버 등록 POST
app.use(express.json()) //http 외 모듈인 '미들웨어' : json 설정 
app.post('/youtubers', (req, res) => {
    
    const channelTitle = req.body.channelTitle

    if (channelTitle) {
        // 등록..? Map(db)에 저장(PUT) 
        db.set(id++, req.body)
        res.status(201).json({
        message : `${db.get(id-1).channelTitle}님, 유튜버 생활을 응원합니다!`
    })
    } else {
        res.status(400).json({
            message : "요청 값을 제대로 보내주세요."
        })
    }
})
```

*****

#### HTTP 상태 코드


__2** : 성공__

- 조회/수정/삭제 성공 : __200__
- 등록 성공 : __201__

__4** : 클라이언트 문제__

- 요청한 연산(처리)를 할 때 필요한 데이터(req)가 덜 왔을 때 : __400__
- 찾는 페이지(리소스) 없음 (url에 맞는 api 없음) : __404__

__5** : 서버 문제__

- 서버가 크리티컬한 오류를 맞았을 때 : __500__


*****

#### GET, POST 차이점

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FDhspi%2FbtsJsQAj1T8%2FKEPjFiEJAoLL0xzK6UbFBK%2Fimg.png)

*****

### 미니 프로젝트



__회원 API 설계__



__로그인 : POST /login__

- req : body (id, pwd)
- res : `${name}님 환영합니다` // => 메인 페이지 



__회원가입 : POST /join__

- req : body(userId, pwd, name)
- res : `${name}님 환영합니다` // => 로그인 페이지



__회원 개별 "조회" : GET /users/:id__

- req : URL (id)
- res : id, name

__회원 개별 "탈퇴" : DELETE /usere/:id__

- req : URL (id)
- res : `${name}님 다음에 또 뵙겠습니다.` // => 메인 페이지

회원은 계정 1개당 채널 100개까지 가질 수 있다.


__채널__

- 채널 생성
- 채널 수정
- 채널 삭제


__로그인 페이지__

(1) 화면 완성 API X
(2) 로그인 버튼 클릭 시, __id, pwd => 로그인 시켜줄 API__


__회원가입 페이지__

(1) 화면 생성 API X 
(2) 회원가입 버튼 클릭 시, __id, pwd, 이름 => 회원가입 시켜줄 API__


__마이 페이지__

(1) 화면 생성 => 회원 정보 조회 API 
(2) 회원탈퇴 버튼 클릭 시, => __회원탈퇴 시켜줄 API__ 


*****

### 회원 API
 

let {id} = req.params 으로 URL 파라미터에서 id를 불러오면, __id는 문자열로 받아집니다.__ 
숫자로 불러오려면 id = parseInt(id)를 해줘야 합니다.

```
// express 모듈 셋팅
const express = require('express')
const app = express()
app.listen(7777)
app.use(express.json())

let db = new Map()
let id = 1 // 하나의 객체를 유니크하게 구별하기 위함 구별하기 

// 로그인
app.post('/login', (req, res) => {
    const id = req.body.id
})
// 회원가입
app.post('/join', (req, res) => {
    const userId = req.body

    if (req.body == {}) {

        res.status(400).json({
            message : "요청 값을 제대로 보내주세요."
        })

    } else {
        db.set(id++, req.body) // 등록..? Map(db)에 저장(PUT) 

        res.status(201).json({
        message : `${db.get(id-1).name}님, 환영합니다.`
        })
    }
})
// 회원 개별 조회
app.get('/users/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    const user = db.get(id)
    if (user == undefined) {
        res.status(404).json({
            message: "회원 정보가 없습니다."
        }) 
    } else {
        res.status(200).json({
            userId : user.userId,
            name : user.name
        })
    }
})
// 회원 개별 탈퇴
app.delete('/users/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    let user = db.get(id)
    if (user == undefined) {
        res.status(404).json({
            message : "회원 정보가 없습니다."
        })
    } else {
        db.delete(id)

        res.status(200).json({
            message : `${user.name}님, 아쉽지만 우리 인연은 여기까지 입니다.`
        })
    }
})
```


경로가 비슷한 라우트를 **Router**를 사용하여 통합


```
// 회원 개별 조회
app.route('/users/:id')
    get((req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    const user = db.get(id)
    if (user == undefined) {
        res.status(404).json({
            message: "회원 정보가 없습니다."
        }) 
    } else {
        res.status(200).json({
            userId : user.userId,
            name : user.name
        })
    }
})

// 회원 개별 탈퇴
    .delete((req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    let user = db.get(id)
    if (user == undefined) {
        res.status(404).json({
            message : "회원 정보가 없습니다."
        })
    } else {
        db.delete(id)

        res.status(200).json({
            message : `${user.name}님, 아쉽지만 우리 인연은 여기까지 입니다.`
        })
    }
})
```

*****

## 회원 API 설계 및 결과 업로드
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FesLPso%2FbtsJtICgI9K%2FoKpN2UeF87Q3ckTkd3ySM1%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FLqPJU%2FbtsJu9LRAUO%2F4VDcz56rkmOdvpg9grti4K%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbCeBKH%2FbtsJuhRFc3L%2FE6Jykzdq019KEK3bcrKeH1%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FE36P0%2FbtsJvrZNRBI%2FBdPtGawnh5b27NvubnpZW0%2Fimg.png)
![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbXUS6y%2FbtsJuslaePr%2Fee9oxznAY3t9f2G2u5UKbk%2Fimg.png)
