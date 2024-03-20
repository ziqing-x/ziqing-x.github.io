---
title: git 常用命令速查
tags: [git]
category: other
image:
  path: /assets/img/headers/git_command.webp
---

git 中，默认的开发分支是 master 或者 main，origin 是远程版本库，下面列出一些常用的命令。

## 创建版本库

```bash
git clone <url>      # 克隆远程版本库
git init             # 初始化本地版本库
```

## 修改提交历史

```bash
git status           # 查看状态
git diff             # 查看变更内容
git add .            # 跟踪所有改动过的文件
git add <file>       # 跟踪指定的文件
git stash            # 把当前修改了但未跟踪的文件保存到栈里
git stash pop        # 把保存到栈里的修改了但未跟踪的文件弹出栈里
git mv <old> <new>   # 文件改名
git rm <file>        # 删除文件
git rm --cached <file>
                     # 停止跟踪文件但不删除
git commit -m "commit message"
                     # 提交所有更新过的文件
git commit --amend   # 修改最后一次提交
```

## 查看提交历史

```bash
git log              # 查看提交历史
git log -p <file>    # 查看指定文件的提交历史
git blame <file>     # 以列表方式查看指定文件的提交历史
```

## 撤销

```bash
git reset --hard HEAD
                     # 撤销工作目录中所有未提交文件的修改内容
git checkout HEAD <file>
                     # 撤销指定的未提交文件的修改内容
git revert <commit>  # 撤销指定的提交
```

## 分支与标签

```bash
git branch           # 列出所有本地分支
git branch -a        # 列出所有分支，包括远程分支和本地分支
git branch <new-branch>
                     # 创建一个新分支
git branch -d <branch>
                     # 删除指定分支
git checkout <branch/tag>
                     # 切换到指定分支或者标签
git checkout -b <new-branch>
                     # 基于当前的分支创建一个本地的新分支
git tag              # 列出所有本地标签
git tag <tagname>    # 打标签
git tag -d <tagname> # 删除标签
```

## 合并与衍合(变基)

```bash
git merge <branch>   # 合并指定分支到当前分支
git rebase <branch>  # 衍合指定分支到当前分支
```

## 远程操作

```bash
git remote -v        # 查看远程版本库信息
git remote show <remote>
                     # 查看指定远程版本库信息
git remote add <remote> <url>
                     # 添加远程版本库
git fetch <remote>   # 从远程仓库获取代码
git pull <remote> <branch>
                     # 下载代码并合并到本地
git push <remote> <branch>
                     # 上传代码并合并到远程
git push <remote> :<branch/tag-name>
                     # 删除远程分支或标签
git push --tags      # 上传所有标签
git push -u origin <branch>
                     # 把本地分支推送到远程分支（远程不存在此分支则创建）
```