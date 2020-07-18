---
title: 'git 回退已经提交的commit（转）'
date: 2020-07-18 13:56:07
tags: [git]
published: true
hideInList: false
feature: 
isTop: false
---

在使用git时，push到远端后发现commit了多余的文件，或者希望能够回退到以前的版本。

先在本地回退到相应的版本：
```bash
git reset --hard <版本号>
// 注意使用 --hard 参数会抛弃当前工作区的修改
// 使用 --soft 参数的话会回退到之前的版本，但是保留当前工作区的修改，可以重新提交
```

如果此时使用命令：

```
git push origin <分支名>
```

会提示本地的版本落后于远端的版本；

为了覆盖掉远端的版本信息，使远端的仓库也回退到相应的版本，需要加上参数--force

```
git push origin <分支名> --force
```


[原文](https://blog.csdn.net/xs20691718/article/details/51901161)