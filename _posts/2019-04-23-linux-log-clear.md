---
layout: single
title: 리눅스 서버 용량 확보 방법(log 간편 정리)
date: 2019-04-23 10:33:30.000000000 +09:00
type: post
header:
    teaser: "https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/225px-Tux.svg.png"
    image: "https://upload.wikimedia.org/wikipedia/commons/thumb/3/35/Tux.svg/225px-Tux.svg.png"
categories:
- IT
tags: [Linux]
---

관리가 제대로 되지 않고 있는 리눅스 서버가 하나 있다. 이 서버의 log 파일을 지우기 위해 사용하는 명령어를 사용 순서대로 정리해보았다.

--------------

# 1. 디스크 사용량 확인

```bash
df
```

--------------

# 2. 하위 디렉토리별 용량 확인

```bash
du -h --max-depth=1 # 디렉토리 용량만 확인
du -sh *            # 디렉토리 및 파일 용량 확인 
```

--------------

# 3. 파일 확인

만든지 30일을 초과한 log 파일을 검색하여 정렬

```bash
find ./ -name '*.log.*' -mtime +29 | sort
```

--------------

# 4. 파일 삭제

만든지 30일을 초과한 log 파일을 삭제

```bash
find ./ -name '*.log.*' -mtime +29 -delete
```

--------------

# 5. 파일 압축 후 삭제

만든지 2일을 초과한 log 파일을 압축 후, 원본 파일은 삭제

```bash
find ./ -name '*.log.*' -mtime +1 -exec sh -c "tar cjvf {}.bz2 {}; rm -f {};" \;
```
