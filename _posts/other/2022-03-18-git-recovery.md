---
title: git 误删远程分支恢复方法
tags: [git]
category: other
image:
  path: /assets/img/headers/git_recovery.webp
---

在开发过程中，有时候会不小心删掉某个远程分支，下面是恢复删除分支的解决办法。

## 步骤
在 `gitlab` 的 `activity` 菜单找到该分支的最后一次提交的 `commitId`,复制 `commitId`，然后执行以下命令：

```bash
git checkout -b xxx commitId
git push -u origin xxx
```