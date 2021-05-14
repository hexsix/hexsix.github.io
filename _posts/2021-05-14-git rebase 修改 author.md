---
layout:     default
title:      git rebase 修改 author
date:       2021-05-14 10:34
author:     喵小六
---

> 公司电脑的 git 默认设置了自己的名字拼音和公司邮箱作为 `user.name` 和 `user.email`
>
> 结果写这个博客的时候忘了改设置，把错误的名字邮箱提交到 github 上了

要解决这个问题，用到的命令是 `git rebase`

```bash
$ git rebase [-i | --interactive] <start_commit_id> <end_commit_id> [--root]
```

`-i` 表示 `--interactive`，这样 bash 会显示 `./hexsix.github.io (git)-[master|rebase-i] $` 表示正在交互界面

`start_commit_id` 和 `end_commit_id` 缺省都是 `HEAD`，左开右闭，一般只要写 `start_commit_id` 就好了

```bash
$ git rebase -i HEAD~3
```

这个命令将打开一个编辑器，允许编辑 `(HEAD～3, HEAD]` 总共三次 commit

```txt
  1 pick 2817321 fix: margin between posts
  2 pick 1476291 new post
  3 pick 2a0c9b1 fix: summary display
  4 
  5 # Rebase 83df198..2a0c9b1 onto 83df198 (3 commands)
  6 #
  7 # Commands:
  8 # p, pick <commit> = use commit
  9 # r, reword <commit> = use commit, but edit the commit message
 10 # e, edit <commit> = use commit, but stop for amending
 11 # s, squash <commit> = use commit, but meld into previous commit
 12 # f, fixup <commit> = like "squash", but discard this commit's log message
 13 # x, exec <command> = run command (the rest of the line) using shell
 14 # b, break = stop here (continue rebase later with 'git rebase --continue')
 15 # d, drop <commit> = remove commit
 16 # l, label <label> = label current HEAD with a name
 17 # t, reset <label> = reset HEAD to a label
 18 # m, merge [-C <commit> | -c <commit>] <label> [# <oneline>]
 19 # .       create a merge commit using the original merge commit's
 20 # .       message (or the oneline, if no original merge commit was
 21 # .       specified). Use -c <commit> to reword the commit message.
 22 #
 23 # These lines can be re-ordered; they are executed from top to bottom.
 24 #
 25 # If you remove a line here THAT COMMIT WILL BE LOST.
 26 #
 27 # However, if you remove everything, the rebase will be aborted.
 28 #
```

仔细阅读注释，发现 `edit` 命令可以帮助我们修改提交

> `pick`: 保留此 commit，换句话说就是什么都不做
>
> `reword`：只修改 commit message
>
> `edit`：停下来修改内容后通过 commit --amend 提交
>
> `squash`：合并到前一个 commit
>
> `fixup`：合并到前一个 commit，但不要保留该 commit 的 commit message

编辑文本，将 `pick` 修改为 `edit` 或者简写 `e`

```txt
  1 edit 2817321 fix: margin between posts
  2 edit 1476291 new post
  3 edit 2a0c9b1 fix: summary display
  4 
  5 # Rebase 83df198..2a0c9b1 onto 83df198 (3 commands)
......
```

我们并不修改 commit 内容，只修改 author，使用 `commit --amend` 提交 author 修改，使用 `rebase continue` 继续修改下一个 commit 直到提示 Success 退出 `rebase-i` 模式

```bash
$ git commit --amend --author "hexsix <hexsixology@gmail.com>"
$ git rebase --continue
Successfully rebased and updated refs/heads/master.
```

因为左边是开区间，很显然没办法改第一次提 commit，用 `--root` 可以修改所有 commit

```bash
$ git rebase -i --root
```

如果在 rebase 过程中手滑了，用 `--abort` 回到 rebase 前的状态

```bash
$ git rebase --abort
```

一般来讲，rebase 命令是用来合并 commit 的，比如本地开发的分支上的多条 commit，合并成一个 commit 之后 merge 到主分支上。

> 对了，很显然我错误的 author 信息已经提交到 github 上了，rebase 后的 git 仓库单纯用 `git push` 会被驳回
>
> `git push -f` 真的有爽到 XD
