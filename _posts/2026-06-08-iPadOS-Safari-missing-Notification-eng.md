---
layout: single
title: "iPadOS Safari ReferenceError: Can't find variable: Notification"
date: 2026-06-08 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTE5avojQC4sVS83b4AZXWCg6GEz_zXbqExaA&s"
    image: "https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcTE5avojQC4sVS83b4AZXWCg6GEz_zXbqExaA&s"
categories:
- IT
tags: [safari]
---

## Symptom

- On iPad Safari (a regular tab), a ReferenceError: Can't find variable: Notification is thrown inside a useEffect.
- Works fine on desktop (Chrome·Safari·Firefox); only happens on a real iOS/iPadOS Safari tab.
- MDN says Notification is supported — so why??


## Cause

- On iOS/iPadOS, regular Safari tabs and WebViews do not expose the Web Notification API.
- MDN lists it as supported, but to be precise it's only partial support... on 16.4+, and only for home-screen PWAs.

```
Safari on iOS 16.4 (Release date: 2023-03-27)

Partial support: The Notification interface is undefined, unless the page is a web app saved to the home screen. The app's manifest must have a non-default display value.
```

- eslint-plugin-compat can't catch the missing Notification API issue, because of caniuse's limitation (it doesn't check MDN's notes).
- Playwright's webkit e2e test runs the desktop engine, so it doesn't catch this error either.


## Fix

Add a typeof guard:

if (typeof Notification === 'undefined') return;


## Preventing Recurrence

- Lint: forbid direct access to the global Notification via no-restricted-globals.
- Test: the jsdom environment used in unit tests has no Notification. So if you don't stub Notification, you can easily test the branch where it's unavailable.


## TL;DR

1. iOS/iPadOS Safari supports the Notification API only partially (!).
2. Prevent recurrence with a no-restricted-globals lint rule (ban direct access) + a test covering the unavailable-Notification branch.
3. Read MDN carefully.


20260608
