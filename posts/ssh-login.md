---
title: 'ssh 免密码快速登录（MacOs）'
date: 2020-03-05 19:47:14
tags: [ssh]
published: true
hideInList: false
feature: 
isTop: false
---
## 1.创建秘钥文件

- 打开终端，执行 ssh-keygen 命令创建密钥对：

> ``` bash
> ssh-keygen -t rsa -C 'your email@domain.com'
> -t 指定密钥类型，默认即 rsa ，可以省略
> -C 设置注释文字，比如你的邮箱，可以省略
> ```

- 生成过程中会提示输入密码两次，如果不想在使用公钥的时候输入密码，可以回车跳过；
  密钥默认保存位置在 `~/.ssh` 目录下，打开后会看到私钥文件 `id_rsa` 和公钥文件 `id_rsa.pub`；

## 2.复制公钥至服务器

- 通过宝塔面板直接将`~/.ssh/id_rsa.pub`配置到服务器的`~/.ssh/authorized_keys`文件中

- 使用 scp 命令将本地的公钥文件 `id_rsa.pub` 复制到需要连接的Linux服务器：

  ```bash
  scp ~/.ssh/id_rsa.pub <用户名>@<ip地址>:/home/id_rsa.pub
  ```

- 如果修改了ssh默认连接端口的话，需要加上端口信息：

  ```bash
  scp -P <端口号> ~/.ssh/id_rsa.pub <用户名>@<ip地址>:/home/id_rsa.pub
  ```

- 把公钥追加到服务器ssh认证文件中:

  ```bash
  cat /home/id_rsa.pub >> ~/.ssh/authorized_keys
  ```

- 这时候在本地终端中使用用户名和ip登录就不需要密码了：

  ```xml
  ssh <用户名>@<ip>
  ```

- 如果修改了ssh默认连接端口的话，需要加上端口信息：

  ```xml
  ssh -p <端口号> <用户名>@<ip地址>
  ```

## 3.配置快捷登录

- 在本地 `~/.ssh/config` 配置文件中添加ssh服务器信息（可多个），格式：

> ```ruby
> Host            alias            #自定义别名
> HostName        hostname         #替换为你的ssh服务器ip或domain
> Port            port             #ssh服务器端口，默认为22
> User            user             #ssh服务器用户名
> IdentityFile    ~/.ssh/id_rsa    #第一个步骤生成的公钥文件对应的私钥文件
> ```

- 配置成功后，即可直接通过`ssh alias`登录