---
layout: single
title: git으로 파일 변경 이력 확인하기
date: 2018-02-07 17:07:30.000000000 +09:00
type: post
header:
  teaser: "https://tortoisegit.org/images/logo.svgz"
  image: "https://tortoisegit.org/images/logo.svgz"
categories:
- IT
tags: [git, SourceTree, TortoiseGit, git log]
---

[git]에서 파일의 변경 이력을 확인하는 방법은 간단하다. file_path에는 확인하고픈 파일의 경로를 넣어준다.

```bash
git log -p -- file_path
```

만약 단어별로 변경 이력을 보고 싶다면 **--word-diff** 옵션을 추가하면 된다.

```bash
git log -p --word-diff -- file_path
```

이걸로도 충분하지만 가끔은 gui로 편하게 확인하고플 때가 있다. 하지만 내가 windows 환경에서 주로 쓰는 툴인 [SourceTree]에는 이런 기능이 없는 것 같다.
Linux 환경에서는 어짜피 [SourceTree]가 안돌아가니 [git cola]를 쓰면 된다. [git cola]에서는 파일별 변경 이력 확인을 지원한다.
그래서 [여기](https://git-scm.com/download/gui/windows)에 나온 툴을 검토한 결과, 

**[TortoiseGit]을 쓰기로 했다.**

[TortoiseCVS] 시절부터 써온 믿음직한 거북이라 선택했다. 구닥다리 이미지가 있지만 깔끔하고 잘 돌아간다. 확인 방법은 다음과 같다. 한글판 기준이다.

1. 윈도우 탐색기에서 로그 확인을 원하는 파일에 우클릭
1. *TortoiseGit*에 마우스 올리기
1. *로그보기* 클릭

결론은, 
### 꼬부기 너로 정했다!

---

## 보론

[SourceTree]에도 **git log -p -- file_path** 와 비슷한 기능이 있다. 한글판 기준으로 파일명에 우클릭 하면 나오는 **선택된 로그** 기능이다. 하지만 최근에 수정한 파일의 로그를 확인하는게 아니라면 조금 번거롭다.

이것저것 설치하기 영 귀찮으면 그냥 IDE에 내장된 기능을 써도 좋다. [Visual Studio Code] 사용자라면 [Git history](https://github.com/DonJayamanne/gitHistoryVSCode)나 [GitLens](https://github.com/eamodio/vscode-gitlens) extension을 설치해서 써도 좋다.

[git]: https://git-scm.com/
[SourceTree]: https://ko.atlassian.com/software/sourcetree
[git cola]: https://git-cola.github.io/
[TortoiseGit]: https://tortoisegit.org/
[TortoiseCVS]: http://www.tortoisecvs.org/
[Visual Studio Code]: https://code.visualstudio.com/