---
title: 'git stash（转）'
date: 2020-07-18 13:55:40
tags: [git]
published: true
hideInList: false
feature: 
isTop: false
---
我们有时会遇到这样的情况，正在dev分支开发新功能，做到一半时有人过来反馈一个bug，让马上解决，但是新功能做到了一半你又不想提交，这时就可以使用git stash命令先把当前进度保存起来，然后切换到另一个分支去修改bug，修改完提交后，再切回dev分支，使用git stash pop来恢复之前的进度继续开发新功能。下面来看一下git stash命令的常见用法

### git stash
保存当前工作进度，会把暂存区和工作区的改动保存起来。执行完这个命令后，在运行git status命令，就会发现当前是一个干净的工作区，没有任何改动。使用git stash save 'message...'可以添加一些注释

### git stash list
显示保存进度的列表。也就意味着，git stash命令可以多次执行。

### git stash pop [–index] [stash_id]
- `git stash pop` 恢复最新的进度到工作区。git默认会把工作区和暂存区的改动都恢复到工作区。
- `git stash pop --index`
恢复最新的进度到工作区和暂存区。（尝试将原来暂存区的改动还恢复到暂存区）
- ` stash pop stash@{1}`恢复指定的进度到工作区。stash_id是通过git stash list命令得到的
通过git stash pop命令恢复进度后，会删除当前进度。
git stash apply [–index] [stash_id]
除了不删除恢复的进度之外，其余和git stash pop 命令一样。

### git stash drop [stash_id]
删除一个存储的进度。如果不指定stash_id，则默认删除最新的存储进度。

### git stash clear
删除所有存储的进度。

> [原文](https://blog.csdn.net/daguanjia11/article/details/73810577)
>
