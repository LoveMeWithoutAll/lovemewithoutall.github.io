---
layout: single
title: git branch 일괄 삭제 shell script
date: 2024-11-19 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://avatars.githubusercontent.com/u/18133?s=200&v=4"
    image: "https://avatars.githubusercontent.com/u/18133?s=200&v=4"
categories:
- IT
tags: [git]
---

## 로컬 브랜치 일괄 삭제

```sh
# 제외할 브랜치 목록 (공백으로 구분)
EXCLUDE_BRANCHES=("main" "develop" "release")

# 현재 브랜치 포함 제외 리스트
CURRENT_BRANCH=$(git branch --show-current)
EXCLUDE_BRANCHES+=("$CURRENT_BRANCH")

# 제외할 브랜치 패턴에 맞는 브랜치를 제거하고 삭제
for BRANCH in $(git branch | sed 's/* //'); do
  if [[ ! " ${EXCLUDE_BRANCHES[@]} " =~ " ${BRANCH} " ]]; then
    echo "Deleting branch: $BRANCH"
    git branch -D $BRANCH
  else
    echo "Skipping branch: $BRANCH"
  fi
done
```

-----

## 원격 브랜치 일괄 삭제

```sh
# 제외할 브랜치 목록
EXCLUDE_BRANCHES=("main" "develop" "release")

# 원격 저장소 이름
ORIGIN_NAME="origin"

# 원격 브랜치 삭제
for BRANCH in $(git branch -r | grep -v "${ORIGIN_NAME}/HEAD" | sed "s/${ORIGIN_NAME}\///"); do
  EXCLUDE=false
  for EXCLUDE_BRANCH in "${EXCLUDE_BRANCHES[@]}"; do
    if [[ "$EXCLUDE_BRANCH" == "$BRANCH" ]]; then
      EXCLUDE=true
      break
    fi
  done

  if [[ "$EXCLUDE" == false ]]; then
    echo "Deleting remote branch: $BRANCH"
    git push $ORIGIN_NAME --delete "$BRANCH"
  else
    echo "Skipping remote branch: $BRANCH"
  fi
done
```

20241119
