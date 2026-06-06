---
layout: single
title: On the Matter of Perfectly Working Playwright e2e Tests All Breaking
date: 2026-05-21 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Playwright_Logo.svg/960px-Playwright_Logo.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/7/75/Playwright_Logo.svg/960px-Playwright_Logo.svg.png"
categories:
- IT
tags: [.env, playwright]
---

## Environment

- playwright v1.59.1
- vite v8.0.9

## Symptoms

The Playwright e2e tests started failing in my local environment. Not just the newly added test code, but even tests I hadn't touched were all failing. These were tests that had been working perfectly just 10 minutes earlier.

## Cause

There was a problem with the environment variables that Playwright was referencing.

```ts
// playwright.config.ts
dotenv.config({ path: '.env.test' });
```

I had Playwright reference the environment variables as shown above, and in `.env.test` the backend server URL was configured like this.

```
// existing .env.test
VITE_BACKEND_URL=http://api.test
```

The server at the URL `http://api.test` didn't exist in the first place. So wouldn't it be obvious that every test referencing the above URL environment variable was bound to fail? Why on earth had the tests that should obviously have failed been <b>succeeding</b>?

To understand this situation, we first need to trace what Playwright was doing. Let's look at it step by step.

- When Playwright runs tests, it spawns a local server as a child process to run the tests against. The local server here is exactly the vite local server we commonly talk about during development.
- When Playwright spawns the child process (the local server), it passes the environment variables via `process.env`. `process.env` takes higher priority than any environment variable set in files like `.env`.
- Therefore, just as specified in `dotenv.config({ path: '.env.test' });`, Playwright passes `.env.test` to the local server via `process.env`.
- Because of the `VITE_BACKEND_URL=http://api.test` environment variable, the local server sends API requests to a URL that doesn't even exist.

So doesn't this mean the tests should obviously fail?

### So why on earth did the tests succeed (false negative)?

The real cause is this.

```ts
// playwright.config.ts
webServer: {
  command: 'yarn start',
  url: 'http://localhost:3002',
  reuseExistingServer: !process.env.CI,
},
```

This setting tells Playwright to spawn the local server as a child process. Pay attention to `reuseExistingServer`.

### If it's not a CI environment, just reuse the local server that's already up and running.

And I was not in a CI environment.

Now let's separate the cases where the tests succeed and where they fail. Of course, in both cases all the tests show `pass`.

- Test success: The local server I had launched myself was already up, so Playwright didn't spawn a new local server. This local server had the environment variables properly set. So the wrong environment variable wasn't injected.
- Test failure: `The local server wasn't up.` So Playwright spawned a new local server. The wrong environment variable got injected!

### So then why did the environment variable have a weird value in it?

`.env.test` was a file not tracked in git. So I had put whatever values I wanted into it. Managing environment variable files is always tricky.

## Resolution

Once I fixed the wrong environment variable, all the problems were solved.

## One-Line Summary

A combination of ignorance about the fact that child processes inherit `.env` environment variables when spawned, plus ignorance about `reuseExistingServer`, produced a 'non-deterministic' error where tests worked fine and then broke. `.env` environment variable files can cause non-deterministic errors, so be careful when using them.

EOD

20260521
