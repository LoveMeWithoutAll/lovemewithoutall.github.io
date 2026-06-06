---
layout: single
title: Setting up lefthook in a monorepo
date: 2026-01-27 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://github.com/evilmartians/lefthook/raw/master/logo_sign.svg"
    image: "https://github.com/evilmartians/lefthook/raw/master/logo_sign.svg"
categories:
- IT
tags: [CI]
---

When setting up Lefthook in an environment where the project lives in a monorepo or a subfolder, you may run into situations where it can't find the config file or where yarn commands fail.

## 1. The Problem

Given a project structure like this:

```
/my-repo (Git Root, .git)
  └── /frontend (Project Root, package.json, lefthook.yml)
```

The errors that occurred:

1. Config file not recognized: When running Lefthook from the frontend/ folder, it tries to find the config file at the Git root and fails. (No config files found)
2. Yarn project not recognized: If you move the config file to the Git root, the yarn command runs at the root and can't find package.json. (Usage Error: No project found)

## 2. Root Cause Analysis

- How Lefthook works: Since Lefthook is a Git hook tool, it always looks for the config file and runs commands relative to the Git Root where the .git folder is located.
- Yarn Berry's strictness: Yarn Berry blocks command execution if there is no package.json or related config at the location where it runs. Lefthook's root option alone is not enough to control Yarn Berry's environment.

## 3. The Solution: Explicit Path Change (cd)

The most reliable solution is to move lefthook.yml to the Git root and configure it to enter the project folder directly when running commands.

### Final configuration (/my-repo/lefthook.yml)

```YAML
pre-commit:
  commands:
    frontend-check:
      run: cd frontend && yarn check # Move into the frontend folder -> run the yarn script from that location
```

## 4. Why is this approach good?

1. Solid environment isolation: With cd frontend, Yarn Berry can accurately recognize the .yarnrc.yml and dependencies in that folder.
2. Flexible scalability: Even if you add other folders like backend/ later, you can manage hooks for all projects from a single lefthook.yml at the root.
3. Lefthook v2 compatibility: It fundamentally prevents the issue where the root option behaves unexpectedly in some environments.

## One-line summary

When using Lefthook together with Yarn Berry, the key is to clearly define the "current working directory (CWD) where the command runs".

## Extra

Tip: After setting this up, you can test the behavior without an actual commit by running the `yarn lefthook run pre-commit` command!

EOD

20260127
