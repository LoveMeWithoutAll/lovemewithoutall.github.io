---
layout: single
title: Auto rotate images using Firebase Functions
date: 2018-09-03 17:45:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [javascript, firebase]
---

# Rotate images by EXIF orientation infomation

Using [Cloud Functions for Firebase], Images uploaded in [Firebase Cloud Storage] will be automatically rotated by EXIF orientation meta data. The below function script is triggered on uploading image. And that's all. It works!

# Function script

```javascript
const functions = require('firebase-functions')
const mkdirp = require('mkdirp-promise')
const { Storage } = require('@google-cloud/storage')
const spawn = require('child-process-promise').spawn
const path = require('path')
const os = require('os')
const fs = require('fs')
const storage = new Storage()

exports.rotateUsingExif = functions.storage.object().onFinalize(async (object) => {
  const filePath = object.name
  const bucketName = object.bucket
  const metadata = object.metadata

  const tempLocalFile = path.join(os.tmpdir(), filePath)
  const tempLocalDir = path.dirname(tempLocalFile)
  const bucket = storage.bucket(bucketName)

  if (!object.contentType.startsWith('image/')) {
    console.log('This is not an image.')
    return null
  }

  if (metadata.autoOrient) {
    console.log('This is already rotated')
    return null
  }
  
  return mkdirp(tempLocalDir).then(() => {
    // Download file from bucket.
    return bucket.file(filePath).download({destination: tempLocalFile})
  }).then(() => {
    console.log('The file has been downloaded to', tempLocalFile)
    // Convert the image using ImageMagick.
    return spawn('convert', [tempLocalFile, '-auto-orient', tempLocalFile])
  }).then(() => {
    console.log('rotated image created at', tempLocalFile)
    metadata.autoOrient = true
    return bucket.upload(tempLocalFile, {
      destination: filePath,
      metadata: {metadata: metadata}
    })
  }).then(() => {
    console.log('image uploaded to Storage at', filePath)
    // Once the image has been converted delete the local files to free up disk space.
    fs.unlinkSync(tempLocalFile)
    return console.log('Deleted local file', filePath)
  })
})
```

# Example

* Working app is [here](https://book-blog-with-largo.web.app/).
* Working code is [here](https://github.com/LoveMeWithoutAll/book-blog/blob/master/functions/index.js).

# etc

* 2019.06.10: [Firebase Cloud Storage] on Node 10 is beta. I recommend to use Node 8. [Here is a problem that I encountered](https://github.com/firebase/firebase-functions/issues/447).

[Cloud Functions for Firebase]: https://firebase.google.com/docs/functions/?authuser=0
[Firebase Cloud Storage]: https://firebase.google.com/docs/storage/?authuser=0
