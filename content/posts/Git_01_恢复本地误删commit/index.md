---
posts: G-01
title: Git 小记-001：恢复本地误删 commits
date: 2023-05-18
draft: false
description: 本地 git commits 误删之后的恢复方法
tags:
- Git

hiddenFromHomePage: true
---

<!--more-->

## 起因
---

当我需要 push 到远程时，报错：
```
fatal: You are not currently on a branch.
To push the history leading to the current (detached HEAD)
state now, use

    git push master HEAD:<name-of-remote-branch>
```

## 经过 
---

接着我尝试了：
```
git branch temp
git checkout main
git merge temp
git branch -d temp
```

此时提示：
```
Warning: you are leaving 5 commits behind, not connected to
any of your branches:

  ec6b40a feat(posts): update posts-001
  7ce0b79 chore(code-highlight): add code-highlight
  d26725d chore(commitlint): add commit standard
  4bbbed0 chore(gitignore): modify gitignore
  868b222 refactor(themes): update hugo-blog-awesome
```

此时，出现问题：以上五条commit关联的文件从目录消失

## 解决
---

1. 查找丢失的文件关联的 commits'SHA
```
git reflog
```

2. 基于最后一条丢失的 commit 建立新分支 recovery
```
git branch recovery {commit's SHA}
```

3. merge 当前分支和 recovery
```
git merge recovery
```

4. 删除分支 recovery
```
git brach -d recovery
```
