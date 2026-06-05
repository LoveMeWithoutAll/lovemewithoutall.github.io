---
layout: single
title: Building a Survey Service with SurveyJS in Vue.js
date: 2018-05-04 14:00:30.000000000 +09:00
type: post
header:
    teaser: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
    image: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
categories:
- IT
tags: [SurveyJS, Vue.js]
---

I built a survey service. I built it as an SPA using [Vue.js] and [SurveyJS].
You can find the source code [here](https://github.com/LoveMeWithoutAll/vue-survey-template).

## 1. Vue Setup
First, start the project with [vue-cli]. The version of `vue-cli` I used is 2.3.0.
```bash
# Start the project with vue cli
vue create vue-survey-template 
```

I just applied `vue-router`, `babel`, and `eslint`.

## 2. SurveyJS
Install [SurveyJS].
```bash
npm install --save survey-vue
```

## 3. Creating Survey Questions
Create the survey questions with [Survey Builder]. After quickly putting them together in the Survey Designer, just copy and save the value from the JSON Editor. Here's what I made.

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

## 3. Adding SurveyJS
Modify the HelloWorld component as follows.
```html
// src/components/HelloWorld.vue
<template>
  <div class="hello">
    <div id='surveyElement'>
      <survey :survey="surveyModel"/> <!-- pass surveyModel into the surveyJS component -->
    </div>
  </div>
</template>

<script>
import * as SurveyVue from 'survey-vue' // import surveyJS
import surveyJSON from '@/assets/survey.json' // load the survey question JSON

let Survey = SurveyVue.Survey // extract just the Survey component from surveyJS

export default {
  name: 'HelloWorld',
  components: {
    Survey // use the SurveyJS component
  },
  computed: {
    // you could put surveyModel in data,
    // but since I often change the survey questions based on vuex values,
    // I defined surveyModel in computed
    surveyModel () { 
      let survyeModel = new SurveyVue.Model(surveyJSON) // pass the survey question JSON in as the model
      survyeModel.onComplete.add(function (result) { // add a callback function to run when the Complete button is pressed
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

## 4. Wrapping Up
When you press the Complete button, the screen below appears.

![surveyJS-onComplete](/assets/images/surveyjs-vue.png)

Done!

[SurveyJS]: https://surveyjs.io/
[Survey Builder]: (https://surveyjs.io/Survey/Builder/)
[Vue.js]: https://vuejs.org/
[vue-cli]: https://github.com/vuejs/vue-cli/blob/dev/docs/README.md
