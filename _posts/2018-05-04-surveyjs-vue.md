---
layout: single
title: Vue.js에서 SurveyJS로 설문조사 서비스 만들기
date: 2018-05-04 14:00:30.000000000 +09:00
type: post
header:
    teaser: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
    image: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
categories:
- IT
tags: [SurveyJS, Vue.js]
---

설문조사 서비스를 만들었다. [Vue.js]와 [SurveyJS]를 사용하여 SPA로 만들었다. 
소스코드는 [여기](https://github.com/LoveMeWithoutAll/vue-survey-template)서 볼 수 있다.

## 1. Vue 구성
우선 [vue-cli]로 프로젝트를 시작한다. 사용한 `vue-cli`의 버전은 2.3.0이다.
```bash
# vue cli로 프로젝트 시작
vue create vue-survey-template 
```

간단히 `vue-router`, `babel`, `eslint`만 적용했다.

## 2. SurveyJS
[SurveyJS]를 설치한다.
```bash
npm install --save survey-vue
```

## 3. 설문문항 만들기
[Survey Builder]로 설문문항을 만든다. Survey Designer에서 뚝딱뚝딱 만든 후, JSON Editor의 값을 복사해 저장하면 된다. 나는 이렇게 만들었다.

```JSON
// src/assets/survey.json
{
    "title": "make survey with Vue.js and SurveyJS!",
    "pages": [
        {
        "name": "page1",
        "elements": [
                {
                "type": "radiogroup",
                "name": "question1",
                "choices": [
                            "item1",
                            "item2",
                            "item3"
                            ]
                }
            ]
        }
    ]
}
```

## 3. SurveyJS 넣기
HelloWorld 컴포넌트를 아래와 같이 수정한다.
```html
// src/components/HelloWorld.vue
<template>
  <div class="hello">
    <div id='surveyElement'>
      <survey :survey="surveyModel"/> <!-- surveyJS 컴포넌트에 surveyModel을 넣는다 -->
    </div>
  </div>
</template>

<script>
import * as SurveyVue from 'survey-vue' // surveyJS를 import한다
import surveyJSON from '@/assets/survey.json' // 설문조사 JSON 문항을 불러온다

let Survey = SurveyVue.Survey // surveyJS에서 Survey 컴포넌트만 따로 빼낸다

export default {
  name: 'HelloWorld',
  components: {
    Survey // SurveyJS 컴포넌트를 사용한다
  },
  computed: {
    // data에 surveyModel을 넣어도 좋지만 
    // 나는 vuex의 값에 따라 설문조사 문항을 변경하는 경우가 많아서
    // computed에 surveyModel을 정의했다
    surveyModel () { 
      let survyeModel = new SurveyVue.Model(surveyJSON) // 설문조사 JSON 문항을 model로 넣는다
      survyeModel.onComplete.add(function (result) { // Complete 버튼을 누르면 실행할 콜백 함수를 넣는다
        alert(`result: ${JSON.stringify(result.data)}`)
      })
      return survyeModel
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
```

## 4. 마무리
Complete 버튼을 누르면 아래 화면처럼 뜬다.

![surveyJS-onComplete](/assets/images/surveyjs-vue.png)

끝!

[SurveyJS]: https://surveyjs.io/
[Survey Builder]: (https://surveyjs.io/Survey/Builder/)
[Vue.js]: https://vuejs.org/
[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md