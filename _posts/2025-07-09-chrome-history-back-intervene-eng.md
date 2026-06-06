---
layout: single
title: "I Hit Back, So Why Am I Here? Digging into Redirect Behavior in Chrome and Safari"
date: 2025-07-09 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
    image: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
categories:
- IT
tags: [javascript]
---

## I Hit Back, So Why Am I Here? Chrome and Safari Behave Differently?

### The Phenomenon

When doing web development, you run into absurd situations where the exact same code behaves differently across browsers. In particular, the behavior of the "Back" button after a page redirect is a recurring source of confusion for developers.

The phenomenon I ran into goes like this.

*   **Scenario:** The user clicked a link on `Page A` to navigate to `Page B`, and as soon as `Page B` loaded, JavaScript automatically redirected it to `Page C`.


```text
[ Page A ]
     |
     |  User clicks the link to B
     V
[ Page B ] --- (automatically redirects to C on load, with no user interaction) ---> [ Page C ]
     |
     |  Clicks the "Back" button
     V
 Chaos ????
```

At this point, if you press the browser's back button on `Page C`...

**Result:**

*   **Chrome:** Skips `B` and goes back to `A`.
    *   `[ A ] <--- (B is skipped) --- [ C ]`
*   **Safari:** Goes back to `B` according to the visit history.
    *   `[ A ] <--- [ B ] <--- [ C ]`

This phenomenon is not a bug. It is a clearly **intended design difference** that reflects each browser's philosophy. In this post, I'll dig into why this difference happens, the underlying principles behind it, and how we as developers should respond.

[Welcome to the dragon's maw (official documentation, straight from the source)](https://html.spec.whatwg.org/multipage/browsing-the-web.html#:~:text=Welcome%20to%20the%20dragon%27s%20maw)

### The Culprit: Chrome's "Special (Exceptional)" Policy

To get straight to the point, the cause of this phenomenon is Chrome's own user protection policy.

*   **Safari's behavior (`A ← B ← C`)** is the result of following the Web Standard exactly as written.
*   **Chrome's behavior (`A ← C`)** is the result of applying an exceptional policy called **"History Manipulation Intervention"** on top of the standard behavior.

Surprisingly, it's Chrome, not Safari, that behaves in the exceptional way.

Now let's take a closer look at this situation from each browser's perspective.

---

### Safari Is Being Unfairly Blamed. It Just Followed the Standard

Safari's behavior is not a special feature; it is the result of faithfully following the fundamental principles of the web (history and performance).

#### Principle 1: History - Every Footstep Must Be Recorded

The HTML Living Standard from WHATWG, the web standards body, defines how the browser's **session history** (i.e., the back/forward list) should behave. According to this standard, except for special cases like `window.location.replace()`, page navigation via `window.location.href` or a link click must add a new entry to the session history stack.

So if pages were opened in the order `A → B → C`, it is perfectly normal for `[A, B, C]` to be pushed onto the visit history stack in order. Safari simply implemented this standard as is.

#### Principle 2: Performance - Going Back Should Be Fast

To maximize back/forward performance, Safari uses the **BFCache (Back-Forward Cache)** very aggressively. The BFCache is a cache that, when the user leaves a page, "freezes" the entire memory state of that page (its DOM, JavaScript state, etc.) and stores it like a snapshot.

When the user presses back, Safari instantly revives this "frozen" page and shows it immediately, because it has stored Page B in the BFCache. This happens even though the developer didn't want to show Page B.

### Why Does Chrome Intervene in the Standard? - The Principle of "User Protection"

Chrome, on the other hand, decided that protecting the user experience is more important than following the web standard.

#### Background: The "Back-Button Trap" of Malicious Sites

In the past, some ad pages and malicious sites abused the back button to trap users on a specific page. Even when the user pressed back, a redirect script planted on the previous page would run again and keep sending the user to an unwanted page. This was the "Back-button Hijacking" problem. It was a situation like an ant lion's pit trap.

#### Chrome's Solution: History Manipulation Intervention

To solve this problem, Chrome introduced a policy called "History Manipulation Intervention" in 2018, starting with Chrome 69. The core of this policy is simple.

1.  After a page loads, it detects whether a redirect to another page occurred **without an explicit interaction such as a user click (User Activation)**.
2.  It internally assigns a **"skip" flag** to the visit history entry created this way (in our example, `Page B`).
3.  When the user presses the back button, the history entry with this flag is ignored and the user is taken to the previous entry (`Page A`).

In other words, Chrome made the decision that "an automatic redirect page that the user didn't intend is not useful and may even harm the user experience, so I'll exclude it from the back list." For a particular page to remain in the visit history in Chrome, that page must absolutely have user interaction.

In JavaScript, you can check whether user interaction (User Activation) has occurred via `navigator.userActivation.isActive`. This, too, is a deep and difficult topic...

### Comparison at a Glance: Chrome vs. Safari

| Category | Chrome | Safari |
| :--- | :--- | :--- |
| **Core Philosophy** | Prioritizes **user protection** | Prioritizes **web standards compliance** and **performance** |
| **Handling of redirect page (B)** | Treats it as "potentially problematic" history, assigns a skip flag | Treats it as a "valid" visit history entry, stores it in the BFCache |
| **Back button result** | **Skips** the intermediate entry (B) | **Goes back** to the intermediate entry (B) |
| **Related technology/policy** | History Manipulation Intervention | BFCache, HTML Standard |

### So What Should Developers Do?

Now that you understand this difference, it's time to make our code predictable.

#### 1. Make Your Intent Clear: `location.replace()`

If `Page B` is merely an intermediate stopover for authentication processing or data transmission, a page the user should never need to return to, then you should use `location.replace()`.

The `replace()` method **replaces** the current page's visit history with the new page. As a result, `B` does not remain in the visit history stack, so pressing back from `C` takes you to `A` consistently across all browsers.

```javascript
// Before: B remains in the visit history.
window.location.href = '/c-page';

// After: B's visit history is replaced with C. (recommended)
window.location.replace('/c-page');
```

#### 2. Accept the Difference and Test

You need to recognize that it is "normal" for a redirect using `location.href` to behave differently across browsers. You must always test whether the planned navigation flow works correctly in the browser environment used by your service's primary target users.

#### 3. Decide from a User Experience (UX) Perspective

Before choosing a technical approach, you should ask a fundamental question: **"Would the user actually want to return to this intermediate page?"** If there's no need for that at all, then using `location.replace()` is the right answer. If the intermediate page contains meaningful information worth returning to, it's better to provide a clear UI so the user isn't confused.

### Conclusion: It's a Feature, Not a Bug

The difference in back button behavior between Chrome and Safari is not a simple bug or fragmentation. It is the result of each browser's distinct philosophy: "user protection" versus "web standards compliance."

Trying to understand the deep inner workings of these browsers is enough to make you keel over in frustration.

## References
- The Chrome team's story (history manipulation intervention): https://chromium.googlesource.com/chromium/src/+/refs/heads/lkgr/docs/history_manipulation_intervention.md
- html spec: https://html.spec.whatwg.org/multipage/browsing-the-web.html
- bfcache: https://web.dev/articles/bfcache?hl=ko

EOD

20250709
