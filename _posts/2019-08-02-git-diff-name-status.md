---
layout: single
title: git에서 commit간 변경된 파일 목록 조회
date: 2019-08-02 18:07:30.000000000 +09:00
type: post
header:
    teaser: "assets/images/Git-Logo-2Color.png"
    image: "assets/images/Git-Logo-2Color.png"
categories:
- IT
tags: [git]
---

커밋을 비교하여 변경된 파일의 목록을 뽑아보자

```bash
git diff --name-status <old_commit> <new_commit>
```

파일명 앞에 붙는 구분자의 의미는 다음과 같다([출처](https://git-scm.com/docs/git-diff#Documentation/git-diff.txt---name-status))

```
A: addition of a file

C: copy of a file into a new one

D: deletion of a file

M: modification of the contents or mode of a file

R: renaming of a file

T: change in the type of the file

U: file is unmerged (you must complete the merge before it can be committed)

X: "unknown" change type (most probably a bug, please report it)
```

특정 구분자, 예를 들면 삭제된 파일만 뽑아내려면 다음 옵션을 추가하면 된다.([출처](https://git-scm.com/docs/git-diff#Documentation/git-diff.txt---diff-filterACDMRTUXB82308203))

```bash
--diff-filter=[(A|C|D|M|R|T|U|X|B)…​[*]]
```

