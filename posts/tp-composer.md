---
title: 'zsh: no matches found不能指定版本'
date: 2020-04-16 10:55:43
tags: [thinkphp]
published: true
hideInList: false
feature: 
isTop: false
---
## 问题
```
composer create-project topthink/think=5.0.* tp50 --prefer-dist
```
使用composer安装Thinkphp5.0时发生错误


```
# 提示
zsh: no matches found: think=5.0.*
```

## 解决


```
# 编辑
nano ~/.zshrc
# 添加
setopt no_nomatch 
# 生效
source ~/.zshrc
```
再安装即可

