---
layout: single
title: Get image's EXIF data on Vue.js
date: 2018-07-19 17:45:30.000000000 +09:00
type: post
header:
    teaser: "/assets/images/exif-vue.png"
    image: "/assets/images/exif-vue.png"
categories:
- IT
tags: [Vue]
---

I wrote codes get [EXIF](https://en.wikipedia.org/wiki/Exif) data of image using [Vue.js] for [liks79](https://github.com/liks79) who is my dear friend.

I used [vue-picture-input](https://github.com/alessiomaffeis/vue-picture-input) and [exif-js](https://github.com/exif-js). You can confirm [EXIF] data on browser console.

```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://unpkg.com/vue"></script>
  <script src="https://unpkg.com/vue-picture-input"></script>
  <script src="https://cdnjs.cloudflare.com/ajax/libs/exif-js/2.3.0/exif.js" async></script>
  <title>Vue exif getter In the browser!</title>
</head>
<body>
  <div id="app">
    <p>{{ message }}</p>
    <picture-input ref="pictureInput" @change="onChange"></picture-input>
  </div>
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        message: 'Vue exif meta info getter'
      },
      components: {
        'picture-input': PictureInput
      },
      methods: {
          onChange (image) {
              console.log('onChange!')
              if (image) {
                  EXIF.getData(this.$refs.pictureInput.file, function() {
                      console.log('image info', this)
                      console.log('exif data', this.exifdata)
                  })
              } else {
                  console.log(`it's not image`)
              }
          }
      }
    })
  </script>
</body>
</html>
```

Working code on [jsfiddle](https://jsfiddle.net/YoungSeonAhn/zLuw1gx4/) may be helpful.

End!

[Vue.js]: https://vuejs.org/
[EXIF]: https://en.wikipedia.org/wiki/Exif