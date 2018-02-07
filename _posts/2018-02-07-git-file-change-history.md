---
layout: single
title: git으로 파일 변경 이력 확인하기
date: 2018-02-07 17:07:30.000000000 +09:00
type: post
header:
  teaser: "https://pbs.twimg.com/profile_images/827067710081228800/7LvoSGRf_400x400.jpg"
  image: "https://pbs.twimg.com/profile_images/827067710081228800/7LvoSGRf_400x400.jpg"
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
그래서 [여기](https://git-scm.com/download/gui/windows)에 나온 툴을 검토한 결과, [TortoiseGit]을 쓰기로 했다. 
[TortoiseCVS] 시절부터 써온 믿음직한 거북이라 선택했다. 구닥다리 이미지가 있지만 깔끔하고 잘 돌아간다.

결론은, 
### 꼬부기 너로 정했다!

[git]: https://git-scm.com/
[SourceTree]: https://ko.atlassian.com/software/sourcetree
[git cola]: https://git-cola.github.io/
[TortoiseGit]: https://tortoisegit.org/
[TortoiseCVS]: http://www.tortoisecvs.org/