---
title: git 高阶
tags:
  - git
---

## rebase和merge

使用merge会使commit的历史中出现分叉，这种分叉再汇合的结构显得混乱，难以管理。此时就需要用到<b>rebase</b><i>变基</i>。

rebase: 把你指定的 commit 以及它所在的 commit 串，以指定的目标 commit 为基础，依次重新提交一次。

动态示意图：

* merge

  ![merge示意图](/img/merge)

  ```shell
  git merge branch1
  ```

* rebase

![rebase示意图](/img/rebase)

```shell
git checkout branch1
git rebase master
```

另外，在 rebase 之后，记得切回 master 再 merge 一下，把 master 移到最新的 commit：

```shell
git checkout master
git merge branch1
```

 A---B---C topic
         /
    D---E---F---G master         >==git rebase master ==>  

 A'--B'--C' topic
                 /
    D---E---F---G master

应用场景：

我在主分支commit a时新建了新分支，此时开始分叉，分叉后我又在主分支改了东西commit b，此时我后悔了，我不该在commit a时分叉的，因为commit b的东西我新分支也需要，此时用衍合，就等于我丢弃原分叉，在commit b重新分叉（原分叉的改动内容当然也是带上的，没有丢）

## commit时提交的文件存在错误

```shell
## 修正文件
git add 1.text
## 重新commit，当前commit会替换掉之前的commit
git commit --amend
```

## 取消当前的commit

```shell
git reset --hard HEAD^
## 撤销的提交并没有消失，只是失效了
```

### 取消之前的commit

```shell
git rebase --onto HEAD^^ HEAD^ branch1
## 以倒数第二个 commit 为起点（起点不包含在 rebase 序列里哟），branch1 为终点，
## rebase 到倒数第三个 commit 上。
```

```shell
## 在编辑界面中删除想撤销的 commits
git rebase -i 哪一个commit
## 在 rebase 命令中直接剔除想撤销的 commits
git rebase --onto 哪一个commit
```

## 错误的代码push到中央仓库了

### 错误代码还在自己的分支上

修改或删除错误的commit,然后push

如果本地在上次commit之后有了修改，再push会失败，可以强行push

```shell
git push origin branch1 -f
```

### 错误代码已经合并到master

```shell
## 撤销错误的提交（相当于增加相反的commit，覆盖之前的commit）
git revert HEAD^
## 然后push
```

## reset用途

reset命令的本质：重置HEAD和他所指向的branch的位置

更具参数的不同，有不同的用途：

* --hard：重置位置的同时，清空工作目录的所有改动；
* --soft：重置位置的同时，保留工作目录和暂存区的内容，并把重置 HEAD 的位置所导致的新的文件差异放进暂存区。
* --mixed（默认）：重置位置的同时，保留工作目录的内容，并清空暂存区。

## checkout 本质

checkout不仅可以用来切换分支，其本质是签出指定的commit

```shell
git checkout branch1
git checkout HEAD^
git checkout master~5
```

checkout只带着head走，reset带着head和branch一起走

## stash

临时需要切换分支，当前分支的改动不想提交，但是需要暂存起来，之后切换回来时可以再取出来

没有没跟踪(tack)过的（即从来没有被 add 过的文件不会被 stash 起来，因为 Git 会忽略它们），需要先add,然后stash

```shell
## 存
git stash
## 取
git stash pop
```

## 恢复删除的branch

```shell
## 查看历史
git reflog
## 假设HEAD 的最后一次移动行为是「从 branch1 移动到 master」。而在这之后，branch1 就被删除了。
## 所以它之前的那个 commit 就是 branch1 被删除之前的位置了，也就是第二行的 c08de9a。
git checkout c08de9a
git checkout -b branch1
```

