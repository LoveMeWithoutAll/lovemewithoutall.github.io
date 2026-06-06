---
layout: single
title: "Fixing vite-tsconfig-paths Path Resolution Issues in a Yarn Monorepo (feat. AI)"
date: 2026-01-29 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://github.com/evilmartians/lefthook/raw/master/logo_sign.svg"
    image: "https://github.com/evilmartians/lefthook/raw/master/logo_sign.svg"
categories:
- IT
tags: [typescript]
---

### Background and the Problem

In a Yarn Monorepo environment, I set up the `vite-tsconfig-paths` library to sync TypeScript's Path Aliases over to Vite's Aliases.
I structured the project configuration based on my understanding of the division of roles between TypeScript (LSP), Vite (Bundler), and Yarn (Package Manager), as well as the PnP environment. However, when running the local server, an error occurred: when Yarn loaded another package in the monorepo, it tried to find the monorepo package via the **PnP virtual path** instead of the path alias defined in `tsconfig`. Setting up new projects is something I do all the time, and I hadn't tried anything new in this project's setup, so I was thoroughly bewildered.

### The Approach

1. **AI-based debugging (failure):** For the first two days, I handed the problem off to AI (the various agents in oh-my-opencode). The AI dug deep into the interactions and configuration details of TypeScript, Yarn, and Vite, but couldn't find a solution.
2. **Human developer debugging (success):** I set aside the AI's help and re-examined the official `vite-tsconfig-paths` documentation. To see how the library modifies Vite's configuration, I enabled the dedicated debugging CLI option and analyzed the logs.

### Root Cause Analysis

The cause of the problem lay in **the scope of config files that `vite-tsconfig-paths` recognizes**.

* **Library behavior:** By default, `vite-tsconfig-paths` only reads the `paths` setting specified in the root `tsconfig.json` file and injects it into Vite.
* **Project setup:** This Monorepo project managed its configuration in a fine-grained way, split into `tsconfig.base.json` and `tsconfig.app.json`. This is Vite's default project setup, but it was something I usually considered pointless and deleted...
* **Result:** The library failed to recognize the Path Aliases specified in the split config files (`tsconfig.app.json`, `tsconfig.base.json`, etc.), so Vite couldn't resolve the paths, and this led to the Yarn PnP resolution error.

### The Fix

When configuring the `vite-tsconfig-paths` plugin, I solved the problem by either using the `projects` option to explicitly specify the paths of the `tsconfig` files to reference, or by restructuring things so that the entry `tsconfig.json` contains the correct path settings.

### Reflection: The Roles of AI and an Experienced Developer

This debugging experience reaffirmed for me both the limits of AI and the role of the developer when it comes to complex environment configuration problems.

#### AI's strengths
- AI is strong with common configuration errors and code syntax.
- It's also fast at searching other open-source project repositories to find best practices.
- It excels at learning and exploring deep knowledge of TypeScript, Yarn, and Vite. I learned a lot myself this time around just watching the AI work.

#### AI's weaknesses
- It showed limits in grasping the "rare context" that arises when a library's implicit default behavior combines with a project's unusual file structure (Split Configs).
- It lacks debugging know-how (!).

**In the end, the basic engineering skills of reading the documentation thoroughly and using debugging tools to verify low-level behavior were the key to solving the problem.**

---

## One-line Summary

Read the official documentation carefully.

EOD

20260202
