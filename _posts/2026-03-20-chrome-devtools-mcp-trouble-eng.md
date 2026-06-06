---
layout: single
title: "Chrome DevTools MCP: Fixing Network.enable timed out"
date: 2026-03-20 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/blog/chrome-devtools-mcp/image/hero.png?hl=ko"
    image: "https://developer.chrome.com/static/blog/chrome-devtools-mcp/image/hero.png?hl=ko"
categories:
- IT
tags: [AI]
---

## [Troubleshooting] Chrome DevTools MCP: Fixing "Network.enable timed out"

While recently using the **Chrome DevTools MCP (Model Context Protocol)**, which gives an AI Agent control over the browser, I fell into an unexpected hell. To get straight to the point: the culprit was **'browser profile corruption,'** and I fixed it by completely reinstalling the Chrome browser.

### 1. Environment and Configuration

  * **OS**: macOS
  * **MCP Server**: `chrome-devtools-mcp`
  * **Config**: Using the most standard setup suggested in the official docs


```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["-y", "chrome-devtools-mcp@latest"]
    }
  }
}
```

### 2. The Symptom: Everything Looks Fine, Yet 'Timeout'

The symptom was nasty, messy, and tricky. On the surface, every link in the chain looked perfect.

  * **Healthcheck**: Normal (OK)
  * **Remote Debugging**: Chrome was running fine with the `--remote-debugging-port=9222` option.
  * **Connectivity**: Confirmed the port connection between the MCP and Chrome.

But whenever the AI Agent (Claude, Codex, and Gemini all behaved the same) actually tried to perform a browser action, every task would inevitably time out with the error below. Handing this task off to an AI Agent made the problem even harder to solve, because AI is, with its accursed... infinite... patience, perfectly comfortable waiting around until the timeout fires.

> **ProtocolError: Network.enable timed out**

The official [Troubleshooting](https://www.google.com/search?q=https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md%23connection-timeouts-with---autoconnect) page mentions a similar case, but that guide was limited to the `--autoConnect` setting. So it wasn't a direct solution for this case (which doesn't use it).

### 3. A Record of Failures

To solve the problem, I put our heads together with the AI Agents and tried various approaches.

1. Fine-tuned the MCP config values (timeout, port, etc.) -> Failed
2. Checked whether multiple Agents were connecting simultaneously and cleaned up sessions -> Failed
3. Uninstalled and reinstalled the Chrome browser -> Failed
4. Launched the Chrome browser using the beta version -> Failed
5. Edited every other MCP config file that the AI had touched on its own -> All failed

The key point here is that

- every AI agent failed, and
- **the issue persisted even after deleting the Chrome application (`.app`)**, which I suspected to be the single point of failure.

It was maddening.

### 4. The Solution: "Nuke it from Orbit"

I had no idea what the cause was.

There was a possibility that

- multiple Agents had attempted a remote connection at the same time, or
- I had too many profiles open and the CDP (Chrome DevTools Protocol) internal state got tangled up.

In the end, I decided to wipe out every trace of 'Chrome,' the single point of failure.

On macOS, I not only removed the Chrome app but also completely blew away the paths where Chrome's settings and profile data are stored, then reinstalled it.

```bash
# Warning: launch the nuke and everything gets wiped clean
$ rm -rf ~/Library/Application\ Support/Google/Chrome
```

After reinstalling and launching Chrome again, the `Network.enable timed out` error that had been driving me crazy vanished, and the MCP magically started working normally..!

### 5. Wrapping Up: A Developer's Urge for Control and MCP

What I felt through this troubleshooting is that, behind the convenience that AI-based tools provide, there still exists a black-box-like realm of 'magic.'

A timeout occurring at the internal protocol level even when every connection state reads as 'normal' stokes a developer's urge for control and drives them mad. Watching a situation where the traditional **Clean Wipe (rm -rf)** ends up being the answer, I'm reminded once again of how important it is to keep your local environment in a clean state, no matter how high the layers of technology stack up...

If you happen to be facing a similar problem, before you start poking at config files, try suspecting `Application Support` first.

EOD

20260320
