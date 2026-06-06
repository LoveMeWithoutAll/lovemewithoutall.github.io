---
layout: single
title: When requestAnimationFrame's callback runs - why only the second rAF can guarantee the frame was actually painted to the screen
date: 2025-07-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
    image: "https://developer.chrome.com/static/docs/chromium/renderingng-architecture/image/diagram-the-rendering-pi-093c8ed755a54.jpeg?hl=ko"
categories:
- IT
tags: [javascript]
---

I kept getting confused about when requestAnimationFrame's callback function actually runs, so I decided to write it all down.

## Environment
- iOS webview

## The Problem

In the webview of 11st's iOS app, when leaving (navigating the URL away from) the full-screen shorts player (officially called 11Play), the `<video>` would briefly flicker as a black box. To fix this, I used a **wait-for-two-rAFs** pattern like the following:

```ts
showImage();                    // Insert a thumbnail DOM element on top of the video

return new Promise((resolve) => { // Allow leaving the player once the Promise resolves
  requestAnimationFrame(() =>     // rAF #1
    requestAnimationFrame(resolve)  // rAF #2
  );
})

```

You might think, "Couldn't we just wait once?" But once you look at the **exact order of the rendering pipeline**, it becomes clear why you have to wait for two frames.

---

### 1. What the browser does within one frame (typically 60 Hz)

```text
VSyncₙ                                                
① Run the rAF queue       <- rAF #1                    
② Style calculation                                          
③ Layout                                              
④ Paint                                               
⑤ Commit (copy from main → compositor thread)              
⑥ Composite / Draw (submit GPU commands)                     
( Schedule GPU swap )   <- only once this is done are the 'on-screen pixels' finalized    
-----------------
VSyncₙ₊₁     ← the panel swaps in the frame
① Run the rAF queue       <- rAF #2
... repeat
```

* **rAF callback**: right after the preceding VSync, **just before the rendering pipeline starts** for the new frame.
* **Style -> (...) -> Composite**: runs continuously right after rAF.
* **Display on screen**: at the next VSync, the data to be sent to the display panel is swapped in from the buffer completed on the GPU.

---

### 2. At rAF #1 the "pixels are not yet on the GPU"

| Point in time          | What has finished?    | How far have the DOM changes intended by showImage() been reflected? |
| -------------------- | --------------- | ---------------------------- |
| **Entering rAF #1**    | Nothing rendered yet    | **Only the "DOM flag" is set**            |
| **Style → Paint**    | Layout and pixel buffer created   | The drawing exists in CPU memory              |
| **Commit**           | Layer tree copied to the compositor | Hasn't reached the GPU yet                 |
| **Composite / Draw** | GPU commands submitted       | Only now is the GPU buffer updated              |
| **VSync₁**           | Buffer swap           | **Visible in the user/tab snapshot**                |

So if you allow navigation with `resolve()` inside **rAF #1**,
when iOS's WKWebView takes a tab snapshot to navigate the URL, it captures the snapshot **before the Commit stage of the rendering pipeline**, and the black rectangle reappears.

---

### 3. rAF #2 is called "after the layer commit is finished"

* For rAF #2 to run, the **Composite → GPU swap scheduling** must already be done, with the browser merely waiting for the next VSync.
* In other words, by the time rAF #2 runs, the pixels produced by `showImage()` are guaranteed to **exist in the GPU buffer**.
* Even if URL navigation, snapshotting, or a Process Swap begins at this point, what gets captured is **the image displayed by the showImage function, not the black video layer**.

---

### 4. So when exactly does rAF run?

```text
VSync₀
└─ rAF #1 ─ showImage(); resolve();  ← allowing URL navigation (leaving the player) here is dangerous
   that rendering pipeline process (Style→ ... →Composite)
VSync₁  ← the moment the thumbnail pixels actually become visible
└─ rAF #2 ─ resolve();              ← allowing URL navigation here = safe
   that process (Style→ ... →Composite)
VSync₂                              ← at this point navigation to the other URL page begins
```

---

### 5. The rendering pipeline order, recapped

| #                                             | What actually happens                                                                                                       
| --------------------------------------------- | ----------------------------------------------------------------------------------------------------------------
| **0. VSync₀**<br>Previous frame finished displaying                 | The display enters the Vertical Blank period. The OS/browser is notified (VSYNC) that it's "time to draw the next frame"                                             |                     
| **1. `showImage()` runs**                       | Only records the DOM change. **Style and layout calculations have not happened yet**                                                                              |                     
| **2. rAF callback runs** (first rAF)                      | rAF runs **right after VSync₀, in the stage *just before* the next render**<br> -> if you call `resolve()` here, the rendering pipeline (3-6) is *not yet finished* at this point 
| **3. Style → Layout → Paint** | Within one frame, the browser calculates and rasterizes the DOM change                                                                                   |                     
| **4. Commit**                       | The layer tree is copied from the main thread → the compositor thread                                                                                     |                     
| **5. Composite / Draw**          | The compositor thread composites the layers and submits commands to the GPU                                                                               |                     
| **6. Schedule buffer swap**                         | The GPU schedules a "buffer swap at the next VSYNC"                                                                                        |                     
| **7. VSync₁**                       | The frame built this round is sent to the panel **at this very moment** and becomes visible to the user <br> -> only now does the result of `showImage()` (the image) appear                                    |                     
| **8. rAF callback runs** (second rAF)        | Right after VSync₁. Now it's guaranteed that the previous frame has been **fully reflected on screen**                                                                       |                     
| **9. Style → Layout → … → VSync₂**   | The next frame's rendering cycle repeats                                                                                                   |                     


---

## Two-line summary

requestAnimationFrame's callback does not run one VSync late. It runs immediately within the current rendering cycle.

**References:**
- [Why is requestAnimationFrame better than setInterval or setTimeout (stack overflow)](https://stackoverflow.com/questions/38709923/why-is-requestanimationframe-better-than-setinterval-or-settimeout)
- [RenderingNG Architecture](https://developer.chrome.com/docs/chromium/renderingng-architecture?hl=ko)

EOD

20250704
