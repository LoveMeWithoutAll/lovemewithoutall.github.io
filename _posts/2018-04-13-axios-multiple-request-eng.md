---
layout: single
title: Sending Multiple Requests with axios
date: 2018-04-13 16:07:30.000000000 +09:00
header:
  teaser: "/assets/images/axios.png"
  image: "/assets/images/axios.png"
type: post
categories:
- IT
tags: [axios]
---

[axios] has a feature that lets you bundle several HTTP requests and fire them off at once. I went to the trouble of building something with this feature, but now I have to delete the code because of a feature change. Let me preserve it here before I forget.

```javascript
import axios from 'axios'

// function 1 that sends a get request
const login = (uid, password) => {
    return axios.get('HTTP endpoint you want to use like ./login.aspx', {
        params: {
            'uid': uid,
            'password': password
        }
    })
}

// function 2 that sends a get request
const isFinished = uid => {
    return axios.get('HTTP endpoint you want to use like ./finish.aspx', {
        params: {
            'uid': uid
        }
    })
}

// function that validates the results
const isPossible = (len, cnt) => {
    // ...
}

// function that does something with the result values
const doSomething = ({name, birth}) => {
    // ...
}

// send multiple requests
axios.all([login(uid, password), isFinished(uid)]) // send multiple requests with axios.all
    .then(axios.spread((loginResp, isFinishedResp) => { // receive the responses with spread
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
