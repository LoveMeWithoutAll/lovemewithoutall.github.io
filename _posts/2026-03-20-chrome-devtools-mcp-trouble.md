---
layout: single
title: Chrome DevTools MCP: Network.enable timed out 해결기
date: 2026-03-20 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://developer.chrome.com/static/blog/chrome-devtools-mcp/image/hero.png?hl=ko"
    image: "https://developer.chrome.com/static/blog/chrome-devtools-mcp/image/hero.png?hl=ko"
categories:
- IT
tags: [AI]
---

## [Troubleshooting] Chrome DevTools MCP: "Network.enable timed out" 해결기

최근 AI Agent에게 브라우저 제어권을 부여하는 **Chrome DevTools MCP(Model Context Protocol)** 를 사용하던 중, 뜻밖의 지옥에 빠졌다. 결론부터 말하자면, **'브라우저 프로필 오염'** 이 원인이었고, 크롬 브라우저 완전 재설치로 해결했다.

### 1. 환경 및 설정

  * **OS**: macOS
  * **MCP Server**: `chrome-devtools-mcp`
  * **Config**: 공식 문서에서 제시하는 가장 표준적인 설정을 사용


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

### 2. 현상: 모든 것이 정상인데 'Timeout'

현상은 더럽고 지저분하고 까다로웠다. 분명 겉으로 보기엔 모든 연결 고리가 완벽했기 때문.

  * **Healthcheck**: 정상 (OK)
  * **Remote Debugging**: `--remote-debugging-port=9222` 옵션과 함께 크롬이 정상 실행 중.
  * **Connectivity**: MCP와 Chrome 간의 포트 접속 확인 완료.

하지만 AI Agent(Claude, Codex, Gemini 모두 동일)가 실제 브라우저 액션을 수행하려고 하면 반드시 아래 오류와 함께 모든 작업이 타임아웃다. AI Agent에게 이 작업을 맡기니 문제 해결이 더욱 어려웠다. AI는 그 저주 받을... 무한한... 인내심으로 timeout이 날 때까지 기다리는 데 익숙하기 때문이다.

> **ProtocolError: Network.enable timed out**

공식 문서의 [Troubleshooting](https://www.google.com/search?q=https://github.com/ChromeDevTools/chrome-devtools-mcp/blob/main/docs/troubleshooting.md%23connection-timeouts-with---autoconnect) 페이지에도 유사한 사례가 언급되어 있으나, 해당 가이드는 `--autoConnect` 설정에 국한된 내용이었다. 따라서 이 케이스(설정하지 않음)에는 직접적인 해결책이 되지 못했다. 

### 3. 실패의 기록

문제를 해결하기 위해 AI Agent들과 머리를 맞대고 다양한 시도를 했습니다.

1. MCP 설정 값(timeout, port 등) 미세 조정 -> 실패
2. 여러 대의 Agent 동시 접속 여부 확인 및 세션 정리 -> 실패
3. Chrome 브라우저 삭제 후 재설치 -> 실패
4. 베타 버전으로 Chrome 브라우저 실행 -> 실패
5. 그 외 AI가 제멋대로 실행한 모든 mcp 설정 파일 수정 -> 전부 실패

여기서 중요한 점은

- 모든 AI agent가 모두 실패하며
- 단일 실패 지점이라 의심되는 **Chrome 애플리케이션(`.app`)을 삭제해도 해결되지 않았다**는 사실이다

사람 미치게 하는 상황이다.

### 4. 해결책: "Nuke it from Orbit"

원인은 도무지 알 수가 없었다.

- 여러 Agent가 동시에 remote 접속을 시도했거나, 
- 너무 많은 프로필을 띄워놓아 CDP(Chrome DevTools Protocol) 내부 상태가 꼬였을 가능성

이 있었다. 

결국 단일 실패점인 'Chrome'의 모든 흔적을 지우기로 결심했다.

macOS에서 Chrome 앱 뿐만 아니라, Chrome의 설정과 프로필 데이터가 저장되는 경로를 완전히 날려버린 후 재설치했다.

```bash
# 주의: 핵을 쏘면 모두 시원하게 날아간다
$ rm -rf ~/Library/Application\ Support/Google/Chrome
```

이후 Chrome을 다시 설치하고 실행하자, 그토록 속을 썩이던 `Network.enable timed out` 오류는 사라지고 MCP가 마법처럼 정상 동작하기 시작했다..!

### 5. 마치며: 개발자의 통제욕과 MCP

이번 트러블 슈팅을 통해 느낀 점은, AI 기반의 도구들이 주는 편리함 이면에는 여전히 블랙박스 같은 '마법'의 영역이 존재한다는 것.

모든 연결 상태가 '정상'임에도 내부 프로토콜 레벨에서 발생하는 타임아웃은 개발자의 통제 욕구를 자극하여 광증을 유발한다. 결국 전통적인 **Clean Wipe(rm -rf)** 가 정답이 되는 상황을 보며, 기술의 층위가 아무리 높아져도 로컬 환경의 클린 상태를 유지하는 것이 얼마나 중요한지 다시금 깨닫는다...

혹시 비슷한 문제를 겪고 계신다면, 설정 파일을 만지기 전에 우선 `Application Support`부터 의심해 보시기 바랍니다.

EOD

20260320
