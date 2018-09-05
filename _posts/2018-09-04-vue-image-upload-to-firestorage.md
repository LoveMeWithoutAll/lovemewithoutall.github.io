---
layout: single
title: Vue image uploader to Firebase Cloud Storage
date: 2018-09-04 12:45:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [javascript, firebase, Vue]
---

# Where is Uploader component?

[vuetify] don't have [uploader component](https://github.com/vuetifyjs/vuetify/issues/238), so I made it. It works on [my blog](https://book-blog-with-largo.firebaseapp.com/). Working code is [here](https://github.com/LoveMeWithoutAll/book-blog/blob/master/src/components/FileUploader.vue). But You don't have any authority without read unfortunately. So I took a picture for you.

# Demo

## Scenario

1. Choosing an image
1. Show uploading progress
1. Show picture you uploaded
1. Delete

![vue-image-upload-demo](/assets/images/vue-image-upload-demo.gif)

# Code

It works on [Firebase Cloud Storage](https://firebase.google.com/docs/storage/).

```html
<template>
  <div>
    <v-btn
      @click.native="selectFile"
      v-if="!uploadEnd && !uploading">
        Upload a cover image
        <v-icon
        right
        aria-hidden="true">
          add_a_photo
        </v-icon>
    </v-btn>
    <input
      id="files"
      type="file"
      name="file"
      ref="uploadInput"
      accept="image/*"
      :multiple="false"
      @change="detectFiles($event)" />
      <v-progress-circular
        v-if="uploading && !uploadEnd"
        :size="100"
        :width="15"
        :rotate="360"
        :value="progressUpload"
        color="primary">
        {{ progressUpload }}%
      </v-progress-circular>
      <img
        v-if="uploadEnd"
        :src="downloadURL"
        width="100%" />
      <div v-if="uploadEnd">
        <v-btn
          class="ma-0"
          dark
          small
          color="error"
          @click="deleteImage()"
          >
          Delete
        </v-btn>
      </div>
  </div>
</template>

<script>
// Thanks Marcelo Forclaz(https://github.com/CristalT) https://gist.github.com/CristalT/2651023cfa2f36cddd119fd979581893
// Thanks Matteo Leoni(https://github.com/signalkuppe) https://github.com/signalkuppe/vuetify-cloudinary-upload/blob/master/src/components/v-cloudinary-upload.vue
import { firestorage } from '@/firebase/firestorage'

export default {
  data () {
    return {
      progressUpload: 0,
      fileName: '',
      uploadTask: '',
      uploading: false,
      uploadEnd: false,
      downloadURL: ''
    }
  },
  methods: {
    selectFile () {
      this.$refs.uploadInput.click()
    },
    detectFiles (e) {
      let fileList = e.target.files || e.dataTransfer.files
      Array.from(Array(fileList.length).keys()).map(x => {
        this.upload(fileList[x])
      })
    },
    upload (file) {
      this.fileName = file.name
      this.uploading = true
      this.uploadTask = firestorage.ref('images/' + file.name).put(file)
    },
    deleteImage () {
      firestorage
        .ref('images/' + this.fileName)
        .delete()
        .then(() => {
          this.uploading = false
          this.uploadEnd = false
          this.downloadURL = ''
        })
        .catch((error) => {
          console.error(`file delete error occured: ${error}`)
        })
    }
  },
  watch: {
    uploadTask: function () {
      this.uploadTask.on('state_changed', sp => {
        this.progressUpload = Math.floor(sp.bytesTransferred / sp.totalBytes * 100)
      },
      null,
      () => {
        this.uploadTask.snapshot.ref.getDownloadURL().then(downloadURL => {
          this.uploadEnd = true
          this.downloadURL = downloadURL
          this.$emit('downloadURL', downloadURL)
        })
      })
    }
  }
}
</script>

<style>
.progress-bar {
  margin: 10px 0;
}
input[type="file"] {
  position: absolute;
  clip: rect(0,0,0,0);
}
</style>
```

[vuetify]: https://github.com/vuetifyjs/vuetify
