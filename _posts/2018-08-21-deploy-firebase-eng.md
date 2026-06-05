---
layout: single
title: Deploy Vue SPA on firebase
date: 2018-08-21 09:45:30.000000000 +09:00
type: post
header:
    teaser: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
    image: "https://firebase.google.com/_static/13499c168a/images/firebase/lockup.png?hl=ko"
categories:
- IT
tags: [firebase, Vue.js]
---

Here I've put together the steps for deploying a SPA built with [Vue.js] to [firebase hosting] using the [firebase cli].

# 1. Install the firebase cli

```bash
npm install -g firebase-tools
```

# 2. firebase login

```bash
firebase login
```

# 3. firebase init

```bash
# cd project-directory
firebase init
```

# 4. init setting

```bash
# Follow the firebase cli prompts
```

# 5. Configure firebase.json

The default value of firebase.hosting.public is *public*. Let's change it to the *dist* folder where the output of *npm run build* is written.

```json
{
  "firestore": {
    "rules": "firestore.rules",
    "indexes": "firestore.indexes.json"
  },
  "hosting": {
    "public": "./dist", // change this part
    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],
    "rewrites": [
      {
        "source": "**",
        "destination": "/index.html"
      }
    ]
  },
  "storage": {
    "rules": "storage.rules"
  }
}

```

# 6. npm run build

```bash
npm run build
```

# 7. firebase deploy

Now let's upload the output of the *dist* folder to firebase hosting.
```bash
firebase deploy
```

# 8. Wrapping up

From now on, when deploying, you only need to do steps **6** and **7**.

# Etc.

If you want to deploy only the rules, you can do it as follows.

```bash
firebase deploy --only storage # deploy firebase storage rules
firebase deploy --only firestore # deploy firestore rules
```

# The End

[firebase cli]: https://firebase.google.com/docs/cli/?hl=ko
[firebase hosting]: https://firebase.google.com/docs/hosting/deploying?hl=ko
[Vue.js]: https://vuejs.org/
