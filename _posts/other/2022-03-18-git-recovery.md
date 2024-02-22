---
title: git 误删远程分支恢复方法
tags: [git]
category: other
image:
  path: /assets/img/headers/git_recovery.webp
---

在开发过程中，有时候会不小心删掉某个远程分支，下面是恢复删除分支的解决办法。

```bash
在gitlab的activity菜单找到该分支的最后一次提交的commitId,复制commitId
git checkout -b xxx commitId
git push -u origin xxx
```