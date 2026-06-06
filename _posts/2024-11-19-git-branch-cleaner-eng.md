---
layout: single
title: A Shell Script to Bulk-Delete git Branches
date: 2024-11-19 08:50:30.000000000 +09:00
type: post
header:
    teaser: "https://avatars.githubusercontent.com/u/18133?s=200&v=4"
    image: "https://avatars.githubusercontent.com/u/18133?s=200&v=4"
categories:
- IT
tags: [git]
---

## Bulk-deleting local branches

```sh
# List of branches to exclude (separated by spaces)
EXCLUDE_BRANCHES=("main" "develop" "release")

# Add the current branch to the exclude list
CURRENT_BRANCH=$(git branch --show-current)
EXCLUDE_BRANCHES+=("$CURRENT_BRANCH")

# Skip branches matching the exclude patterns and delete the rest
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

## Bulk-deleting remote branches

```sh
# List of branches to exclude
EXCLUDE_BRANCHES=("main" "develop" "release")

# Remote repository name
ORIGIN_NAME="origin"

# Delete remote branches
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
