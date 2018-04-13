---
layout: single
title: axios 멀티 리퀘스트 보내기
date: 2018-04-13 16:07:30.000000000 +09:00
header:
  teaser: "/assets/images/axios.png"
  image: "/assets/images/axios.png"
type: post
categories:
- IT
tags: [axios]
---

[axios]에는 HTTP request 여러 개를 한번에 묶어 던지는 기능이 있다. 이 기능을 사용하여 기껏 개발해놨는데, 기능 변경으로 인해 코드를 지워야 한다. 잊기 전에 박제해두자.

```javascript
import axios from 'axios'

// get 요청 보내는 함수1
const login = (uid, password) => {
    return axios.get('HTTP endpoint you want to use like ./login.aspx', {
        params: {
            'uid': uid,
            'password': password
        }
    })
}

// get 요청 보내는 함수2
const isFinished = uid => {
    return axios.get('HTTP endpoint you want to use like ./finish.aspx', {
        params: {
            'uid': uid
        }
    })
}

// 결과 검증하는 함수
const isPossible = (len, cnt) => {
    // ...
}

// 결과값으로 뭔가 작업 하는 함수
const doSomething = ({name, birth}) => {
    // ...
}

// multiple requests 보내기
axios.all([login(uid, password), isFinished(uid)]) // axios.all로 여러 개의 request를 보내고
    .then(axios.spread((loginResp, isFinishedResp) => { // response를 spread로 받는다
        if (!isPossible(loginResp.data.length, isFinishedResp.data.CNT)) return false
        doSomething({
            name: loginResp.data.name,
            birth: loginResp.data.birth
        })
    })).catch((error) => {
        console.error(error)
    })
```      

[axios]: https://github.com/axios/axios
