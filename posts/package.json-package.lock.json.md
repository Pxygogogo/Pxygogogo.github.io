---
title: 'package.json和package-lock.json的区别'
date: 2020-05-11 22:36:59
tags: [node]
published: true
hideInList: false
feature: 
isTop: false
---
package.json:

主要用来定义项目中需要依赖的包

package-lock.json：


根据官方文档，package-lock.json是生成的系统当前安装的库的具体来源和版本号，锁定版本。在 `npm install`时候生成一份文件，用以记录当前状态下实际安装的各个npm package的具体来源和版本号。

当你执行npm install的时候， node会先从package.json文件中读取所有dependencies信息，然后根据dependencies中的信息与node_modules中的模块进行对比，没有的直接下载，node是从package.json文件读取模块名称，从package-lock.json文件中获取版本号，然后进行下载或者更新。


当package.json与package-lock.json都不存在，执行"npm install"时，node会重新生成package-lock.json文件，然后把node_modules中的模块信息全部记入package-lock.json文件，但不会生成package.json文件，此时，你可以通过"npm init --yes"来生成package.json文件

参考：https://blog.csdn.net/u013992330/java/article/details/81110018
