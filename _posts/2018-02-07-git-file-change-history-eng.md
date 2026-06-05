---
layout: single
title: Checking a File's Change History with git
date: 2018-02-07 17:07:30.000000000 +09:00
type: post
header:
  teaser: "https://tortoisegit.org/images/logo.svgz"
  image: "https://tortoisegit.org/images/logo.svgz"
categories:
- IT
tags: [git, SourceTree, TortoiseGit, git log]
---

Checking a file's change history in [git] is simple. Put the path of the file you want to check in file_path.

```bash
git log -p -- file_path
```

If you want to see the change history word by word, just add the **--word-diff** option.

```bash
git log -p --word-diff -- file_path
```

This is enough on its own, but sometimes I want the convenience of checking it through a GUI. However, [SourceTree], the tool I mainly use on Windows, doesn't seem to have this feature.
On Linux, [SourceTree] won't run anyway, so you can use [git cola]. [git cola] supports checking the change history per file.
So, after reviewing the tools listed [here](https://git-scm.com/download/gui/windows),

**I decided to use [TortoiseGit].**

I chose it because it's a trusty turtle I've been using since the [TortoiseCVS] days. It looks a bit old-fashioned, but it's clean and works well. Here's how to check. This is based on the Korean version.

1. In Windows Explorer, right-click the file whose log you want to check
1. Hover over *TortoiseGit*
1. Click *Show log*

In conclusion,
### Squirtle, I choose you!

---

## Appendix

[SourceTree] also has a feature similar to **git log -p -- file_path**. In the Korean version, it's the **Log Selected** feature that appears when you right-click a file name. But unless you're checking the log of a recently modified file, it's a bit cumbersome.

If you really can't be bothered to install all sorts of things, you can just use the feature built into your IDE. If you're a [Visual Studio Code] user, you can install and use the [Git history](https://github.com/DonJayamanne/gitHistoryVSCode) or [GitLens](https://github.com/eamodio/vscode-gitlens) extension.

[git]: https://git-scm.com/
[SourceTree]: https://ko.atlassian.com/software/sourcetree
[git cola]: https://git-cola.github.io/
[TortoiseGit]: https://tortoisegit.org/
[TortoiseCVS]: http://www.tortoisecvs.org/
[Visual Studio Code]: https://code.visualstudio.com/
