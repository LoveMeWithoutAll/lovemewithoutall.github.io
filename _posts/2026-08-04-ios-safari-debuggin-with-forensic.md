---
layout: single
title: 포렌식 기법을 활용한 iOS simulator의 safari history 로그 확인
date: 2026-08-04 00:49:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/6/6a/Mobiles.JPG?utm_source=en.wikipedia.org&utm_campaign=imageinfo&utm_content=original"
    image: "https://upload.wikimedia.org/wikipedia/commons/6/6a/Mobiles.JPG?utm_source=en.wikipedia.org&utm_campaign=imageinfo&utm_content=original"
categories:
- IT
tags: [iOS]
---


# 포렌식 기법을 활용한 iOS simulator의 safari history 로그 확인

## 목표
MacOS에서 iOS simulator를 띄운 후, safari 브라우저에서 새 탭으로 열린 페이지가 어디로 이동했는지 확인하고 싶다.
일반적으로 이런 경우에는 safari devtools를 사용하여 디버깅 하지만, 링크가 브라우저 새 탭을 띄울 경우에는 페이지 이동을 safari devtools로 확인할 수 없다. 이걸 확인하려면 미리 해당 사파리 탭의 devtools를 띄워놔야 하지만, 아직 만들어지지도 않은 브라우저 새 탭의 devtools를 실행할 수는 없기 때문이다.

## 도구
safari devtools를 사용할 수는 없지만, xcode 개발자 도구를 사용할 수는 있다. 대신 커맨드라인에서 `xcrun simctl`을 사용하면 된다.


### xcrun

xcrun은 xcode 개발자 도구를 command line에서 쉽게 사용할 수 있도록 하는 도구이다. 

#### 참고
[xcrun_commands](https://github.com/lana-20/xcrun_commands)
[xcrun man page](https://www.unix.com/man_page/osx/1/xcrun/)


### simctl

simctl은 xcode가 시뮬레이터 제어할 수 있도록 해주는 command line 유틸리티이다.

잘 돌아가는지 확인하기 위해 실행 중인 시뮬레이터의 로그를 스트림으로 볼 수 있는 명령어를 실행해보자.

```
xcrun simctl spawn booted log stream --predicate 'process == "MobileSafari"' --style compact
```

이번에는 현재 실행 중인 시뮬레이터의 목록을 확인하자.

```
xcrun simctl list devices booted
```

대략 이런 식으로 출력될 것이다. 여기서 중요한 것은 Booted 표시된 시뮬레이터 인스턴스의 UDID이다. 아래 실행 예시에서는 077928C7-026B-4D15-AC8C-C992B910D0C4 인 값이다. 이 UDID를 기준으로 시뮬레이터 인스턴스의 정보를 조회할 수 있다.

```
ys-m4pro@youngseonui-MacBookPro Safari % xcrun simctl list devices booted
== Devices ==
-- iOS 26.2 --
-- iOS 26.4 --
-- iOS 26.5 --
    iPhone 17 (077928C7-026B-4D15-AC8C-C992B910D0C4) (Booted)
```

#### 참고
[simctl: Control iOS Simulators from Command Line](https://medium.com/xcblog/simctl-control-ios-simulators-from-command-line-78b9006a20dc)
[iOS Simulator from the Command Line](https://suelan.github.io/2020/02/05/iOS-Simulator-from-the-Command-Line/)


### Safari History.db
iOS 26.5 기준으로 iOS safari는 history 등의 정보를 sqlite3 파일로 저장한다. 여기에 대해 공식 문서는 없지만 포렌식 하시는 선각자들이 이미 많은 걸 파헤쳐두셨다. Plaso의 스크립트를 읽어보면 도움이 될 것이다.

[Source code for plaso.parsers.sqlite_plugins.safari](https://plaso-fork.readthedocs.io/en/latest/_modules/plaso/parsers/sqlite_plugins/safari.html)

그러면 Safari의 History.db는 어디에 있는가? 여기서 위의 시뮬레이터 인스턴스 UDID가 필요하다. 아래 디렉토리로 가보자.

```
~/Library/Developer/CoreSimulator/Devices/[xcrun simctl로 조회한 UDID]/data/Library/Safari
```

이 디렉토리에는 온갖 정보가 있다.

```
ys-m4pro@youngseonui-MacBookPro Safari % ls -lh
total 11240
-rw-r--r--  1 ys-m4pro  staff   139K  8월  3 16:32 AutoFillQuirks.plist
-rw-r--r--  1 ys-m4pro  staff   4.0K  6월  8 11:34 Bookmarks.db
-rw-r--r--  1 ys-m4pro  staff    32K  8월  3 16:33 Bookmarks.db-shm
-rw-r--r--  1 ys-m4pro  staff   515K  6월  8 11:36 Bookmarks.db-wal
-rw-r--r--  1 ys-m4pro  staff   4.0K  6월  8 11:33 BrowserState.db
-rw-r--r--  1 ys-m4pro  staff    32K  8월  3 16:34 BrowserState.db-shm
-rw-r--r--  1 ys-m4pro  staff    60K  6월  8 11:33 BrowserState.db-wal
-rw-r--r--@ 1 ys-m4pro  staff    40K  6월  8 11:34 CloudTabs.db
-rw-r--r--  1 ys-m4pro  staff    32K  8월  3 17:24 CloudTabs.db-shm
-rw-r--r--  1 ys-m4pro  staff     0B  6월  8 11:34 CloudTabs.db-wal
-rw-r--r--  1 ys-m4pro  staff     0B  6월  8 11:34 com.apple.Bookmarks.lock
-rw-r--r--@ 1 ys-m4pro  staff   4.0K  6월  8 11:34 History.db
-rw-r--r--@ 1 ys-m4pro  staff    32K  8월  4 09:00 History.db-shm
-rw-r--r--@ 1 ys-m4pro  staff   3.6M  8월  4 09:24 History.db-wal
-rw-r--r--  1 ys-m4pro  staff   208K  8월  3 20:39 SafariTabs.db
-rw-r--r--  1 ys-m4pro  staff    32K  8월  3 16:32 SafariTabs.db-shm
-rw-r--r--  1 ys-m4pro  staff   258K  8월  4 09:24 SafariTabs.db-wal
```

우리가 원하는 파일은 `History.db` 이다. 만약 db 스냅샷을 따로 보관하고 싶으면 `History.db` & `History.db-wal` & `History.db-shm`을 모두 보관하자. 이 3개는 한 셋트다.


### sqlite3
데이터 조회하는 방법은 간단하다. sqlite3로 History.db를 읽으면 된다. 가급적 -readonly  옵션으로 조회하자. read가 db에 영향을 줄 수 있다.

```
ys-m4pro@youngseonui-MacBookPro Safari % sqlite3 -readonly History.db
SQLite version 3.51.0 2025-06-12 13:14:41
Enter ".help" for usage hints.
```

History.db 내부의 테이블을 조회하자

```
sqlite> .tables
history_client_versions  history_items            history_tombstones
history_event_listeners  history_items_to_tags    history_visits
history_events           history_tags             metadata
```

테이블을 지정해 스키마를 조회할 수도 있다.

```
sqlite> .schema history_visits
CREATE TABLE history_visits (id INTEGER PRIMARY KEY AUTOINCREMENT,history_item INTEGER NOT NULL REFERENCES history_items(id) ON DELETE CASCADE,visit_time REAL NOT NULL,title TEXT NULL,load_successful BOOLEAN NOT NULL DEFAULT 1,http_non_get BOOLEAN NOT NULL DEFAULT 0,synthesized BOOLEAN NOT NULL DEFAULT 0,redirect_source INTEGER NULL UNIQUE REFERENCES history_visits(id) ON DELETE CASCADE,redirect_destination INTEGER NULL UNIQUE REFERENCES history_visits(id) ON DELETE CASCADE,origin INTEGER NOT NULL DEFAULT 0,generation INTEGER NOT NULL DEFAULT 0,attributes INTEGER NOT NULL DEFAULT 0,score INTEGER NOT NULL DEFAULT 0);
CREATE INDEX history_visits__last_visit ON history_visits (history_item, visit_time DESC, synthesized ASC);
CREATE INDEX history_visits__origin ON history_visits (origin, generation);
```

## action


[Source code for plaso.parsers.sqlite_plugins.safari](https://plaso-fork.readthedocs.io/en/latest/_modules/plaso/parsers/sqlite_plugins/safari.html) 를 참고하여 최근 10개의 페이지 이동만 조회하자. `visit_time`은 Apple 기준시(2001-01-01 GMT) 기준 초라서, Unix epoch로 바꾸려면 +978307200 를 해야한다.

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

맨 윗줄이 가장 최근이다. 역순으로 safari의 현재 페이지에 이르기까지의 브라우저의 페이지 이동 이력을 조회할 수 있다. 위 sql 실행 결과에서는 id 76 ~ 77이 브라우저 새 탭 열어 이동하는 케이스이다. 

## 주의

### 1. 브라우저 새 탭 여부

아쉽지만 브라우저 새 탭 여부에 대한 기록은 History.db에는 남지 않는다. 브라우저 탭은 SafariTabs.db에 기록되지만, History.db와 연결할 수 있는 키가 없다.

### 2. 어느 때는 row가 2개 남고, 어느 때는 row가 1개 남는다. 

그 이유는 다음과 같다.
SPA를 초기화 할 때, react-router는 `replaceState`를 실행한다. 이 때

- History.db -> row를 하나 쌓음(위 예시에서는 id 77~78, 84~85)
- safari history api 스택 -> stack을 쌓지 않음

그래서 browser history api에 쌓이는 스택과 History.db에 쌓이는 row는 1:1로 대입할 수 없다.

문서 이동과 SPA 내부 이동은 History.db와 safari history api 모두 1개의 row와 stack만 쌓는다.


## 요약

browser devtools로 디버깅이 불가능 할 때는 포렌식 기법이 도움이 된다.

20260804

EOD
