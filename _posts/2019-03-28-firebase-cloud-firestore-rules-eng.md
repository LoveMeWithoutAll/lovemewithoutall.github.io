---
layout: single
title: Your Firebase Cloud Firestore Database Has Insecure Rules
date: 2019-04-12 13:19:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [firebase]
---

# The Symptom

This happened about a month ago. I got an email like this from Google Firebase.

![firestore-security-alert](/assets/images/firestore-security-alert.png)

It said that [my blog](https://book-blog-with-largo.firebaseapp.com/) was at risk.

# The Cause

It was because I had set up [Cloud Firestore]'s security rules <b><u>carelessly</u></b>.

The code below shows the security rules I had configured. The security vulnerabilities are as follows.

1. Every document path is accessible. See line 3 of the code below.
1. Read access is unconditionally allowed. See line 4 of the code below.

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read;
      allow write: if request.auth != null;
    }
  }
}
```

# The Fix

1. I made it so that only the Post documents are accessible. See line 3 of the code below.
2. I granted permission so that read is only allowed for documents where show == true. See line 4 of the code below.

```
service cloud.firestore {
  match /databases/{database}/documents {
    match /Post/{document=**} {
      allow read: if resource.data.show == true;
      allow write: if request.auth != null;
    }
  }
}
```

You can check this in the [source code](https://github.com/LoveMeWithoutAll/book-blog/blob/master/firestore.rules) of this blog.

# The End

Now the security alert emails no longer arrive. It was a ridiculous mistake I made as a [Cloud Firestore] beginner. I was embarrassed and almost decided not to post about it, but on the off chance that it might help someone, I'm tentatively sharing it here. I hope you don't make the same mistake I did!

## References
https://www.fullstackfirebase.com/cloud-firestore/security-rules
https://firebase.google.com/docs/firestore/security/rules-query

[Cloud Firestore]: https://firebase.google.com/docs/firestore/?hl=ko
