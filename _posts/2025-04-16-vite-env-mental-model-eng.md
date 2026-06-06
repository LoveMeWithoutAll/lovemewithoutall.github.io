---
layout: single
title: A Mental Model for Vite Environment Variable Precedence
date: 2025-04-16 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://vitejs.dev/logo-with-shadow.png"
    image: "https://vitejs.dev/logo-with-shadow.png"
categories:
- IT
tags: [frontend, vite]
---

Vite loads several .env files in order to set up environment variables. You can understand the load order and precedence as follows.

```
Pre-existing environment variables
    │
    ├─ (.env.[mode].local)  → Specific mode & local only (e.g. .env.production.local)
    │
    ├─ (.env.[mode])        → Specific mode only (e.g. .env.production)
    │
    ├─ (.env.local)         → Used in all situations, but local only (ignored by Git)
    │
    └─ (.env)               → Used in all situations
```

## 1. Pre-existing Environment Variables
-	Highest priority: Environment variables already set by the OS or on the command line (e.g. a variable specified like VITE_SOME_KEY=123 vite build) have higher precedence than any .env file.
-	Meaning: Because these variables are already set before Vite runs, the .env files loaded afterward do not overwrite their values.

## 2. Mode-Specific Environment Variable Files

Vite supports dedicated environment variable files for specific modes (e.g. production, development, etc.).

### .env.[mode].local
-	When it applies: Used only in a specific mode, and limited to the local development environment.
-	Precedence: Has higher precedence than the .env.[mode] file.

###	.env.[mode]
-	When it applies: Used for the specified mode.
-	Precedence: Takes precedence over the values defined in the general .env and .env.local.

## 3. General (Common) Environment Variable Files

###	.env.local
-	When it applies: Used in all situations, but limited to the local development environment and ignored by Git.
-	Precedence: Takes precedence over the .env file, but has lower precedence than the mode-specific files.

###	.env
-	When it applies: Provides environment variable settings that apply by default in all situations.
-	Precedence: The lowest precedence.

------

## Example: Order of Environment Variable Application in "production" Mode

### 1.	Pre-existing Environment Variables:
-	Example: VITE_SOME_KEY=123 (a variable already set on the command line or in the operating system)

### 2. Mode-Specific Local File:

-	File: .env.production.local
→ Applied in the local development environment, exclusively for "production" mode

### 3. Mode-Specific File:

-	File: .env.production
→ Applied exclusively for "production" mode

### 4. General Local File:

-	File: .env.local
→ Used in the local environment, and not included in Git

### 5. General File:

-	File: .env
→ Applied by default in all situations

------

## Key Takeaways
-	Highest priority: Environment variables that already exist at runtime have the highest precedence.
-	Mode-specific files take priority: When the same variable name exists, the values defined in .env.[mode].local and .env.[mode] are applied with priority over the values defined in .env and .env.local.
-	Provide defaults: .env and .env.local provide default values when there are no mode-specific files, or when only some variables are specified.

By understanding Vite's environment variable load order through this mental model, you can easily manage the desired precedence in your project's environment configuration and predict how a specific environment variable's value is determined.

## Caution

`.env.local` has lower precedence than `mode`.

------


EOD

20250416
