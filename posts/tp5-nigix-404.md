---
title: 'ThinkPHP5+宝塔+nginx环境下访问路由出现404问题'
date: 2020-05-01 22:20:28
tags: [thinkphp]
published: true
hideInList: false
feature: 
isTop: false
---
在网站伪静态下添加


```
location / {
    if (!-e $request_filename){
	      rewrite ^(.*)$ /index.php?s=$1 last; break;
	}
}
```
