---
layout: single
title: The visibleIf Property in SurveyJS
date: 2018-05-01 14:00:30.000000000 +09:00
type: post
header:
    teaser: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
    image: "https://surveyjs.io/Content/Images/logo_1200x630.jpg"
categories:
- IT
tags: [SurveyJS]
---

Here's something I discovered while building a survey service with [SurveyJS].

When you create JSON with the [Survey Builder](https://surveyjs.io/Survey/Builder/), each question has a property called **visibleIf**. If it's "true", the question is shown on screen; if it's "false", it isn't.

### It's not a boolean true/false.
### It's a string "true"/"false".

So you need to write your JSON like this:

```json
{
    // omitted
    "type": "checkbox",
    "name": "Q1",
    "title": "test quesition",
    "isRequired": true,
    "visibleIf": "false", // since it's "false", this question is not shown
    "choices": [
        "item1",
        "item2",
        "item3"
    ],
    // omitted
}
```

[SurveyJS]: https://surveyjs.io/
