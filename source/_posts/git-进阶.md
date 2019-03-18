---
title: git 进阶
tags:
  - git
---

## head master 和 branch

### head:当前commit的引用

当前commit指的是当前工作目录对应的commit

### branch：分支

如果我在这时创建一个 commit，那么 HEAD 会带着 master分支 一起移动到最新的 commit

master是默认的分支,git clone时会自动checkout到master

### 分支的创建 切换 删除

```shell
## 创建分支
git branch branch1
## 切换分支
git checkout branch1
## 合并以上两条命令，创建并切换分支
git checkout -b branch1
## 删除分支,没有合并到主分支上的分支(分支上的内容已经被追踪或者已经被commit)删除会失败
git branch -d branch1
## 确认删除,不考虑有没有merge
git branch -D branch1
```

## push的本质

* push是上传当前的分支，并上传当前分支的所有commit
* push时，如果当前分支是本地新创建的一个分支，需要指定远程仓库名和分支名

```shell
git push origin branch1
```

* push` 的时候之后上传当前分支，并不会上传 `HEAD`；远程仓库的 `HEAD` 是永远指向默认分支（即 `master)的。

##  merge合并commits

指定一个commit,合并到当前commit

```shell
## 当前分支是A,需要合并的分支是B
git merge B
## 此时，B被合并到A上面
```

### merge应用场景

* 一个分支开发完毕，需要合并到原来的分支
* pull的内部操作

### 特殊情况

1. 冲突

* 一个分支修改A文件，另一个分支修改B文件，合并时自动完成
* 一个分支修改A文件的第100行，另一个分支修改A文件的第20行，合并时自动完成

* 当修改同一文件的同一位置，无法自动完成，会出现冲突。

  1. 解决冲突

  2. 手动合并

  3. add => commit

     如果放弃解决冲突，可以取消merge,回到merge前的状态

     ```shell
     git merge --abort
     ```

2. head领先于当前commit

   此时，merge时的commit与Head处的commit之前不存在分叉，没有什么需要合并的，所以不会进行合并，是一个空操作。

3. head落后于当前commit

   如果 HEAD 和目标 commit 依然是不存在分叉，但 HEAD 不是领先于目标 commit，而是落后于目标 commit, Git 会直接把 `HEAD`（以及它所指向的 `branch`，如果有的话）移动到目标 `commit`。<b>快速前移<i>fast-forward</i></b>

## 最流行的团队开发的工作流：Feature Branching

这种工作流的核心包含以下两点：

* 任何新的功能（feature）或 bug 修复全都新建一个 branch 来写；
* branch 写完后，合并到 master，然后删掉这个 branch。

命令行流程

```shell
## 创建工作分支
git checkout books
## 分支代码完成后
git push origin books
## 同事review代码，合格后,merge
git checkout master
git pull
git merge books
## push代码
git push
## 删除工作分支
git branch -d books
## 如果有远程分支，也删除
git push origin -d books

```

## 关于add

add把改动的内容放入暂存区

```shell
## 全部暂存
git add .
## 每次修改或新增后需要重新add
```

## 看看我都干了些什么

```shell
## 了解修改时间，修改人，修改动作
git log
## 还需要看到修改细节
git log -p
## 查看修改统计
git log --stat
## 查看当前commit的内容
git show
## 比较暂存区和上一次提交
git diff --staged
## 比较工作目录和暂存区
git diff
## 比较工作目录和上一次提交
git diff HEAD

```

