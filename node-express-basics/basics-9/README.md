# Node.js + Express 기본(9)

```
// 로그인
app.post('/login', (req, res) => {
    const {userId} = req.body // userId, pwd
    db.forEach((a, b, c) => {
        console.log(`a: ${a}, b: ${b}, c: ${c}`) // a: value, b: key, c: Map
    })

    // userId가 db에 저장된 회원인지 확인, pwd도 맞는지 비교
})

----------------출력결과-------------------
a: [object Object], b: 1, c: [object Map]
a: [object Object], b: 2, c: [object Map]
a: [object Object], b: 3, c: [object Map]
```

```
//pwd도 맞는지 비교
            if(user.password === password) {
                console.log("패스워드도 같다!")
            } else {
                console.log("패스워드는 틀렸다!")
            }
```

*****
## 빈 객체 확인하는 방법 3가지


__1. 객체.keys()__
2. for in 
3. lodash : isEmpyt

### 객체.keys()
```
const obj1 = {}
const obj2 = { message : "안 빔"}
const num = 1
const str1 = "one"
const str2 = "" // 문자열도 객체입니다!!

console.log(isEmpty(obj1)) // length === 0, true
console.log(isEmpty(obj2)) // length === 1, false

console.log(Object.keys(num).length === 0) // true
console.log(Object.keys(str1).length === 0) // false
console.log(Object.keys(str2).length === 0) // false

function isEmpty(obj) {
    if (Object.keys(obj).length === 0) {
        return true;
    } else {
        return false;
    }
}
```

.

.

.

```
// userId 값을 못 찾았으면..
    if (isNotEmpty(loginUser)) {
        console.log("같은 거 찾았다!")
        //pwd도 맞는지 비교
        if(user.password === password) {
            console.log("패스워드도 같다!")
        } else {
            console.log("패스워드는 틀렸다!")
        } 
    }
})

function isNotEmpty(obj) {
    if (Object.keys(obj).length) {
        return true
    } else {
        return false
    } 
}
------------------------------------------
// userId 값을 못 찾았으면..
    if (isExist(loginUser)) {
        console.log("같은 거 찾았다!")
        //pwd도 맞는지 비교
        if(user.password === password) {
            console.log("패스워드도 같다!")
        } else {
            console.log("패스워드는 틀렸다!")
        } 
    }
})

function isExist(obj) {
    if (Object.keys(obj).length) {
        return true
    } else {
        return false
    } 
}
```

*****
## 채널 API 설계 (URL, http method/status, res/res) 


### 1) 채널 "생성" : POST /channels
- req : body (channelTitle)__
- res 201: `${channelTitle}님 채널을 응원합니다.` => __채널 생성 버튼 클릭 시,  채널 생성__(등록) API

### 2)채널 "수정" : PUT /channels/:id
- req : URL (id), body (channelTitle)
- res 200 : `${}님, 채널명이 성공적으로 수정되었습니다. 기존 : ${} => 수정 : ${}`


### 3)채널 개별 "삭제" : DELETE /channels/:id
- req : URL (id)
- res 200 : `삭제 되었습니다`


### 4) 채널 전체 "조회" : GET /channels
- req : X
- res 200 : 채널 전체 데이터 list, json array 

### 5) 채널 개별 "조회" : GET /channels/:id
- req : URL (id)
- res 200 : 채널 개별 데이터 

## 채널 API 코드 틀 route
```
const express = require('express')
const app = express()
app.listen(7777)
app.use(express.json()) // http 외 모듈 'json'


let db = new Map()
let id = 1 // 하나의 객체를 유니크하게 구별하기 위함 구별하기 


app
    .route('/channels')
    .get() // 채널 전체 조회
    .post() // 채널 개별 생성


app
    .route('/channels/:id')
    .get() // 채널 개별 조회
    .put() // 채널 개별 수정
    .delete() // 채널 개별 삭제
```

 

⬇️

 

## [과제] 로그인 기능 고도화 결과 코드 업로드
```
const express = require('express')
const app = express()
app.listen(7777)
app.use(express.json()) // http 외 모듈 'json'


let db = new Map()
let id = 1 // 하나의 객체를 유니크하게 구별하기 위함 구별하기 


app
    .route('/channels')
    .get((req, res) => { // 채널 전체 조회
        if(db.size) {
            let channels = []

            db.forEach(function(value, key){
                channels.push(value)
            })
            res.status(200).json(channels)
        } else {
            res.status(404).json({
                message : "조회할 채널이 없습니다."
            })
        }
    })
    .post((req, res) => { // 채널 개별 생성 = db에 저장
        if (req.body.channelTitle) {
            db.set(id++, req.body)

            res.status(201).json({
                message : `${db.get(id-1).channelTitle}채널을 응원합니다!`
            })
        } else {
            res.status(400).json({
                message : "요청 값을 제대로 보내주세요."
            })
        }
    }) 


app
    .route('/channels/:id')
    .get((req, res) => { // 채널 개별 조회
        let {id} = req.params
        id = parseInt(id)
        let channel = db.get(id)

        if (channel) {
            res.status(200).json(channel)
        } else {
            res.status(404).json({
                message : "채널 정보를 찾을 수 없습니다."
            })
        }
    })  
    .put((req, res) => { // 채널 개별 수정
        let {id} = req.params
        id = parseInt(id)
        let channel = db.get(id)
        let oldTitle = channel.channelTitle

        if (channel) {
            let newTitle = req.body.channelTitle

            channel.channelTitle = newTitle
            db.set(id, channel)

            res.json({
                message : `채널명이 정상적으로 수정되었습니다. 기존 ${oldTitle} -> 수정 ${newTitle}`
            })

        } else {
            res.status(404).json({
                message : "채널 정보를 찾을 수 없습니다."
            })
        }
    }) 
    .delete((req, res) => { // 채널 개별 삭제
        let {id} = req.params
        id = parseInt(id)
        let channel = db.get(id)

        if (channel) {
            db.delete(id)

            res.status(200).json({
                message : `${channel.channelTitle}이 정상적으로 삭제되었습니다.`
            })

        } else {
            res.status(404).json({
                message : `${channel.channelTitle}채널 정보를 찾을 수 없습니다.`
            })
        }
    })
```