---
title: 'MAMP Pro更改文件上传大小限制'
date: 2020-04-20 20:10:42
tags: []
published: true
hideInList: false
feature: 
isTop: false
---
> 近期由于项目需求，需要上传一些几百MB的文件，但是大文件始终报500错误，连TP5的提示都没给，遂Google一通终得一优解，特此记录。

主要思路：
- 修改php.ini文件（需为当前项目使用的php版本）

==（MAMP Pro菜单栏选择File->Edit Template->PHP->选择对应版本即可）==

```
file_uploads = On           ; 是否允许上传文件 On/Off 默认是On
upload_max_filesize = 32M   ; 上传文件的最大限制（改为需求大小即可）
post_max_size = 32M         ; 通过Post提交的最多数据（改为需求大小即可）
```
- 考虑网络传输快慢,可以修改一些参数

```
max_execution_time = 30000  ; 脚本最长的执行时间 单位为秒
max_input_time = 600        ; 接收提交的数据的时间限制 单位为秒
memory_limit = 1024M        ; 最大的内存消耗
```
