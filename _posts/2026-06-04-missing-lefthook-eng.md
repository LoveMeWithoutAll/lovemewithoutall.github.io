---
layout: single
title: How lefthook works — why you should never run install in a linked worktree
date: 2026-06-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://lefthook.dev/assets/lefthook.png"
    image: "https://lefthook.dev/assets/lefthook.png"
categories:
- IT
tags: [git, lefthook]
---

## Environment

- lefthook v2.1.6
- git v2.54.0

## Background

- git executes files with reserved names, such as `pre-commit`, located under `.git/hooks` at specific points in its workflow. This is a built-in feature of git.
- Initially, the directory only contains example files suffixed with `.sample`, so no hooks run. Once you create an executable file with the exact reserved name, that git hook will run from then on.
- `lefthook install` generates a shell script that says "find the lefthook binary and run it," and places it at `.git/hooks/pre-*`. At this point, the path to the lefthook binary is hardcoded into the script.
- Every time a git hook fires, the lefthook binary reads the jobs described in the `lefthook.yml` DSL and executes them. This is why changes to jobs in the yml take effect immediately, without re-running `lefthook install`.

## How lefthook runs, step by step

1. A git command is executed
2. git runs the hook script in `.git/hooks`
3. The git hook script runs the lefthook binary
4. The lefthook binary executes the jobs registered in `lefthook.yml`

## Why this matters

Creating a new `git worktree` does not create a new `.git/hooks`. A git repository has exactly one `.git/hooks`, shared across all worktrees.

As long as you run `lefthook install` only in the primary worktree, everything is fine. 

**But if you run `lefthook install` in a newly created linked worktree, the lefthook binary path registered in `.git/hooks` gets hardcoded to the binary inside that linked worktree.**

While the linked worktree is alive, nothing goes wrong. But when you delete the linked worktree, the lefthook binary is deleted along with it.

After that, git hooks still fire without raising any error. All you get is a message: "Can't find lefthook in PATH". The lefthook binary was removed together with the linked worktree. If you put blind faith in lefthook, you may end up feeling betrayed.

20260604
