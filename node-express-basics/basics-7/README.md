# Node.js + Express 기본 (7)
*****


## 전체 조회 GET 메소드 설계 진행 중,
 
[ERR_HTTP_HEADERS_SENT]: 
Cannot set headers after they are sent to the client 오류

// REST API 설계 중 오류 현상

```
app.get('/youtubers', (req, res) => {
    
    db.forEach(function (youtuber) {
        res.json(db.set())
    })

let jsonObject = {}
db.forEach(function(value, key){ 
    jsonObject[key] = value
});

res.json(jsonObject) // 처음엔 이 부분이 위 코드 블럭에서 빠져나와있어 오류가 뜸.
``` 

⬇️

```
app.get('/youtubers', (req, res) => {
    
    db.forEach(function (youtuber) {
        res.json(db.set())
    })

let jsonObject = {}
db.forEach(function(value, key){ 
    jsonObject[key] = value
});

res.json(jsonObject) // 코드블럭에 넣어 실행하였는데 오류가 발생
})
``` 

__Cannot set headers after they are sent to the client  오류__
서버가 클라이언트에 둘 이상의 응답을 보내려고 할 때 발생하는 오류 입니다. 

즉 하나의 응답을 보낸 후 또 다른 응답을 동시에 보내려고 할 때 생기는 오류 입니다.

 

__문제 해결__
__res.json 호출을 한 번 하도록 수정하였습니다.__

⬇️

```
// REST API 설계
app.get('/youtubers', (req, res) => {
    
    let youtubers = {}
    db.forEach(function (value, key) {
        youtubers[key] = value
    });
    
    res.json(youtubers)
})
```


![유튜버 등록 후, 전체 조회](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F8B7Lc%2FbtsJrGwIx9s%2FuCtJLVKXUkCYu0MJVByIHK%2Fimg.png)


__그 결과,__
등록된 유튜버 정보까지 전체 조회가 되는 것을 확인할 수 있었습니다.

*****

## forEach 메서드


__[배열 arr의 forEach 사용]__

```
const arr = [1, 2, 3, 4, 5]

arr.forEach(function(a, b, c) {
    console.log(`a: ${a}, b: ${b}, c: ${c}`) // a: 요소 값, b: 인덱스, c: 배열 전체
})



------- 출력 결과 -------
a: 1, b: 0, c: 1,2,3,4,5
a: 2, b: 1, c: 1,2,3,4,5
a: 3, b: 2, c: 1,2,3,4,5
a: 4, b: 3, c: 1,2,3,4,5
a: 5, b: 4, c: 1,2,3,4,5
```



__[Map 객체에서 forEach 사용]__

```
let map = new Map()

map.set(7, "seven")
map.set(9, "nine")
map.set(8, "eight")

map.forEach(function(a, b, c) { // 정렬은 X
    console.log(`a: ${a}, b: ${b}, c: ${c}`) // a: 값, b: 키, c: Map 객체
})



----------- 출력 결과 -----------
a: seven, b: 7, c: [object Map]
a: nine, b: 9, c: [object Map]
a: eight, b: 8, c: [object Map]
```
*****

__[map 함수(메서드) vs forEach 차이]__

```
const arr = [1, 2, 3 ,4 ,5]

const foreachArr = arr.forEach(function(a, b, c) {
    return a * 2
})

const foreachMap = arr.map(function(a, b, c) { 
    return a * 2
})

console.log(`arr : ${foreachArr}, map : ${foreachMap}`)


----------- 출력 결과 ------------
arr : undefined, map : 2,4,6,8,10
```


__- forEach__ 
반환값이 없고 단순히 배열을 순회하며 작업을 수행할 때 사용 (반환값은 undefined).

__- map__ 
배열을 순회하면서 반환값들로 새로운 배열을 생성 (반환값은 변형된 배열).


__따라서 배열을 변형하려고 할 때, map을 사용해야 한다는 것을 알 수 있었습니다.__

*****

### 🔥DELETE를 활용해서! 유튜버를 삭제🔥
 

__개별 유튜버 "삭제" => DELETE /youtubers/:id__ 

__- req__ : params.id
__- res__ : "channelTitle님, 아쉽지만 다음에 또 뵙겠습니다."


```
app.delete('/youtubers/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    const channelTitle = db.get(id).channelTitle
    db.delete(id)

    res.json({
        message : `${channelTitle}님, 아쉽지만 우리 인연은 여기까지 입니다.`
    })
})
```

⬇️

 

예외 처리

```
app.delete('/youtubers/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자
    
    if (db.get(id) == undefined) {
        res.json({
            message : `요청하신 ${id}번은 없는 유튜버입니다.`
        })
    } else {
        const channelTitle = db.get(id).channelTitle
        db.delete(id)

        res.json({
            message : `${channelTitle}님, 아쉽지만 우리 인연은 여기까지 입니다.`
        })
    }
})
```

중복된 db.get(id) 호출을 하나의 변수로 묶어주는 방식으로 코드를 최적화


⬇️

```
// 직접 에러 처리한 코드 
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

*****


### 리 - 팩토링 
소프트웨어의 코드 내부(구조)를 변경하는 것


1) 이해하기 쉽게
2) 성능
3) 안전성
...


나쁜 코드가 있는지 여부 => 리팩토링 => 클린 코드


### 리팩토링은 언제 해야하는지...


1. 코드를 이해하기 어렵다고 느낄 때

2. 코드 중복이 많을 때

3. 에러(문제점)이 n회 발견 되었을 때, 리팩토링을 해야한다.

4. 새로운 기능 추가가 어려울 때,   =>  ex. API URL "설계" 수정

5. 코드 리뷰 할 때

⭐️ __배포, 운영 직전에는 코드 수정이 절대로 일어나선 안됩니다.__⭐️

*****

### 전체 유튜버 "삭제" => DELETE /youtubers
- req : X
- res : "전체 유튜버가 삭제 되었습니다."

```
// 전체 + 전체 조회 : 없다 메세지
app.delete('/youtubers', (req, res) => {
    
    if(db.size >= 1){
        db.clear()
    
        res.json({
            message : "전체 유튜버가 삭제 되었습니다."
        })
    } else {
        res.json({
            message : "삭제할 유튜버가 없습니다."
        })
    }
})
```

⬇️ 리팩토링

```
// 전체 + 전체 조회 : 없다 메세지
app.delete('/youtubers', (req, res) => {
    
    let msg = ""
    if(db.size >= 1){
        db.clear()
        msg = "전체 유튜버가 삭제 되었습니다."
    } else {
        msg = "삭제할 유튜버가 없습니다."
    }
    res.json({
        message : msg
    })
})
```

*****


### 개별 유튜버 "수정" => PUT /youtubers/:id
- req : params.id, body <= channelTitle 
- res : "(이전)channelTitle님, (새로운)channelTitle로 변경 되었습니다."

```
app.put('/youtubers/:id', (req, res) => {
    let {id} = req.params
    id = parseInt(id) // "숫자" => 숫자

    let youtuber = db.get(id)
    let oldTitle = youtuber.channelTitle
    if (youtuber == undefined) {
        res.json({
            message : `요청하신 ${id}번은 없는 유튜버입니다.`
        })
    } else {
        let newTitle = req.body.channelTitle
    
        youtuber.channelTitle = newTitle
        db.set(id, youtuber)

        res.json({
            message : `${oldTitle}님, ${newTitle}로 변경 되었습니다.`
        })
    }
})
```

*****

### HTTP 상태 코드


HTTP(인터넷 상에서 통신할 때 사용하는 규약)안에 작성되어서 들어가는 "상태"


조회/수정/삭제 성공 : __200__

등록 성공 : __201__

찾는 페이지가 없음 (url에 맞는 api 없음) : __404__

서버가 크리티컬한 오류를 맞았을 때 : __500__