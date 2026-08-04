---
layout: single
title: Tracing Safari Navigation History in the iOS Simulator with Forensic Techniques
date: 2026-08-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/6/6a/Mobiles.JPG?utm_source=en.wikipedia.org&utm_campaign=imageinfo&utm_content=original"
    image: "https://upload.wikimedia.org/wikipedia/commons/6/6a/Mobiles.JPG?utm_source=en.wikipedia.org&utm_campaign=imageinfo&utm_content=original"
categories:
- IT
tags: [iOS]
---

# Tracing Safari Navigation History in the iOS Simulator with Forensic Techniques

## Goal

I want to boot an iOS simulator on macOS and find out where a page opened in a new Safari tab navigated to.

Normally you would debug this with Safari devtools, but when a link opens a new browser tab, devtools can't show you the navigation. To capture it you would need devtools already attached to that Safari tab — and you can't attach devtools to a tab that doesn't exist yet.

## Tools

Safari devtools is out, but the Xcode developer tools are not. We'll use `xcrun simctl` from the command line instead.

### xcrun

`xcrun` is the tool that lets you invoke Xcode developer tools from the command line.

#### References

- [xcrun_commands](https://github.com/lana-20/xcrun_commands)
- [xcrun man page](https://www.unix.com/man_page/osx/1/xcrun/)

### simctl

`simctl` is the command line utility Xcode provides for controlling simulators.

To confirm it works, let's stream the logs of the running simulator.

```
xcrun simctl spawn booted log stream --predicate 'process == "MobileSafari"' --style compact
```

Now let's list the simulators that are currently running.

```
xcrun simctl list devices booted
```

The output looks roughly like this. What matters is the UDID of the instance marked `Booted` — in the example below, `077928C7-026B-4D15-AC8C-C992B910D0C4`. Everything about that simulator instance is looked up by this UDID.

```
ys-m4pro@youngseonui-MacBookPro Safari % xcrun simctl list devices booted
== Devices ==
-- iOS 26.2 --
-- iOS 26.4 --
-- iOS 26.5 --
    iPhone 17 (077928C7-026B-4D15-AC8C-C992B910D0C4) (Booted)
```

#### References

- [simctl: Control iOS Simulators from Command Line](https://medium.com/xcblog/simctl-control-ios-simulators-from-command-line-78b9006a20dc)
- [iOS Simulator from the Command Line](https://suelan.github.io/2020/02/05/iOS-Simulator-from-the-Command-Line/)

### Safari History.db

As of iOS 26.5, iOS Safari stores history and similar data in SQLite files. There's no official documentation for this, but the forensics pioneers have already dug most of it up. Reading Plaso's parser is a good place to start.

[Source code for plaso.parsers.sqlite_plugins.safari](https://plaso-fork.readthedocs.io/en/latest/_modules/plaso/parsers/sqlite_plugins/safari.html)

So where is Safari's `History.db`? This is where the simulator instance UDID from above comes in. Head to this directory:

```
~/Library/Developer/CoreSimulator/Devices/[UDID from xcrun simctl]/data/Library/Safari
```

There's all kinds of data in here.

```
ys-m4pro@youngseonui-MacBookPro Safari % ls -lh
total 11240
-rw-r--r--  1 ys-m4pro  staff   139K  Aug  3 16:32 AutoFillQuirks.plist
-rw-r--r--  1 ys-m4pro  staff   4.0K  Jun  8 11:34 Bookmarks.db
-rw-r--r--  1 ys-m4pro  staff    32K  Aug  3 16:33 Bookmarks.db-shm
-rw-r--r--  1 ys-m4pro  staff   515K  Jun  8 11:36 Bookmarks.db-wal
-rw-r--r--  1 ys-m4pro  staff   4.0K  Jun  8 11:33 BrowserState.db
-rw-r--r--  1 ys-m4pro  staff    32K  Aug  3 16:34 BrowserState.db-shm
-rw-r--r--  1 ys-m4pro  staff    60K  Jun  8 11:33 BrowserState.db-wal
-rw-r--r--@ 1 ys-m4pro  staff    40K  Jun  8 11:34 CloudTabs.db
-rw-r--r--  1 ys-m4pro  staff    32K  Aug  3 17:24 CloudTabs.db-shm
-rw-r--r--  1 ys-m4pro  staff     0B  Jun  8 11:34 CloudTabs.db-wal
-rw-r--r--  1 ys-m4pro  staff     0B  Jun  8 11:34 com.apple.Bookmarks.lock
-rw-r--r--@ 1 ys-m4pro  staff   4.0K  Jun  8 11:34 History.db
-rw-r--r--@ 1 ys-m4pro  staff    32K  Aug  4 09:00 History.db-shm
-rw-r--r--@ 1 ys-m4pro  staff   3.6M  Aug  4 09:24 History.db-wal
-rw-r--r--  1 ys-m4pro  staff   208K  Aug  3 20:39 SafariTabs.db
-rw-r--r--  1 ys-m4pro  staff    32K  Aug  3 16:32 SafariTabs.db-shm
-rw-r--r--  1 ys-m4pro  staff   258K  Aug  4 09:24 SafariTabs.db-wal
```

The file we want is `History.db`. If you want to keep a snapshot of the database, save `History.db`, `History.db-wal`, and `History.db-shm` together. The three are one set.

### sqlite3

Reading the data is straightforward — just open `History.db` with sqlite3. Prefer the `-readonly` option; reading can modify the database.

```
ys-m4pro@youngseonui-MacBookPro Safari % sqlite3 -readonly History.db
SQLite version 3.51.0 2025-06-12 13:14:41
Enter ".help" for usage hints.
```

Let's list the tables inside `History.db`.

```
sqlite> .tables
history_client_versions  history_items            history_tombstones
history_event_listeners  history_items_to_tags    history_visits
history_events           history_tags             metadata
```

You can also inspect the schema of a specific table.

```
sqlite> .schema history_visits
CREATE TABLE history_visits (id INTEGER PRIMARY KEY AUTOINCREMENT,history_item INTEGER NOT NULL REFERENCES history_items(id) ON DELETE CASCADE,visit_time REAL NOT NULL,title TEXT NULL,load_successful BOOLEAN NOT NULL DEFAULT 1,http_non_get BOOLEAN NOT NULL DEFAULT 0,synthesized BOOLEAN NOT NULL DEFAULT 0,redirect_source INTEGER NULL UNIQUE REFERENCES history_visits(id) ON DELETE CASCADE,redirect_destination INTEGER NULL UNIQUE REFERENCES history_visits(id) ON DELETE CASCADE,origin INTEGER NOT NULL DEFAULT 0,generation INTEGER NOT NULL DEFAULT 0,attributes INTEGER NOT NULL DEFAULT 0,score INTEGER NOT NULL DEFAULT 0);
CREATE INDEX history_visits__last_visit ON history_visits (history_item, visit_time DESC, synthesized ASC);
CREATE INDEX history_visits__origin ON history_visits (origin, generation);
```

## Action

Referring to [Source code for plaso.parsers.sqlite_plugins.safari](https://plaso-fork.readthedocs.io/en/latest/_modules/plaso/parsers/sqlite_plugins/safari.html), let's pull just the 10 most recent navigations. `visit_time` is in seconds since the Apple reference date (2001-01-01 GMT), so add `+978307200` to convert it to Unix epoch.

```
sqlite> .mode box
sqlite> select v.id,
   ...>        datetime(v.visit_time+978307200,'unixepoch','localtime') t,
   ...>        i.url
   ...> from history_visits v
   ...> join history_items i on i.id = v.history_item
   ...> order by v.visit_time desc, v.id desc
   ...> limit 10;
┌────┬─────────────────────┬──────────────────────────────────────────────────────────────┐
│ id │          t          │                             url                              │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 85 │ 2026-08-04 11:08:20 │ https://m.epic.ai.kr/?utm_source=naver&utm_medium=brandsearc │
│    │                     │ h&utm_campaign=MO&utm_id=search                              │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 84 │ 2026-08-04 11:08:20 │ https://m.epic.ai.kr/?utm_source=naver&utm_medium=brandsearc │
│    │                     │ h&utm_campaign=MO&utm_id=search                              │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 83 │ 2026-08-04 09:24:16 │ https://m.epic.ai.kr/copilot/36b7f134-1581-4c83-90d1-df71482 │
│    │                     │ 0f1b8                                                        │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 82 │ 2026-08-04 09:24:12 │ https://m.epic.ai.kr/copilot                                 │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 81 │ 2026-08-03 17:20:01 │ https://m.epic.ai.kr/copilot/36b7f134-1581-4c83-90d1-df71482 │
│    │                     │ 0f1b8                                                        │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 80 │ 2026-08-03 17:20:00 │ https://m.epic.ai.kr/copilot/conversations-list              │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 79 │ 2026-08-03 17:19:58 │ https://m.epic.ai.kr/copilot                                 │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 78 │ 2026-08-03 16:55:37 │ https://m.epic.ai.kr/?utm_source=naver&utm_medium=brandsearc │
│    │                     │ h&utm_campaign=MO&utm_id=search                              │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 77 │ 2026-08-03 16:55:37 │ https://m.epic.ai.kr/?utm_source=naver&utm_medium=brandsearc │
│    │                     │ h&utm_campaign=MO&utm_id=search                              │
├────┼─────────────────────┼──────────────────────────────────────────────────────────────┤
│ 76 │ 2026-08-03 16:51:55 │ https://ad-creative.gfa.naver.com/widget/preview.html?creati │
│    │                     │ veId=6a703f4a8965ead565d1266f&fullwidth                      │
└────┴─────────────────────┴──────────────────────────────────────────────────────────────┘
```

The top row is the most recent. Read upward from the bottom and you get the browser's navigation history leading up to the current page. In the result above, id 76 → 77 is the case where a link opened a new browser tab and navigated.

## Caveats

### 1. Whether a new tab was opened

Unfortunately, `History.db` keeps no record of whether a new tab was involved. Browser tabs are recorded in `SafariTabs.db`, but there's no key that ties it back to `History.db`.

### 2. Sometimes you get 2 rows, sometimes 1

Here's why.

When a SPA initializes, react-router calls `replaceState`. At that moment:

- `History.db` → adds one row (ids 77–78 and 84–85 in the example above)
- browser history API stack → adds nothing

So the entries on the browser history API stack and the rows in `History.db` cannot be mapped one to one.

A document navigation or an in-app SPA navigation adds exactly one row to `History.db` and one entry to the browser history API stack.

## Summary

When browser devtools can't get you there, forensic techniques can.

20260804

EOD
