---
title: 'docker创建mysql数据库并导入sql文件'
date: 2020-04-23 22:08:49
tags: [docker]
published: true
hideInList: false
feature: 
isTop: false
---
## 1.安装mysql

### 1.1 拉取镜像（以5.7.26为例）

```
docker pull mysql:5.7.26
```
### 1.2 运行容器


```
docker run -itd --name mysql-test -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 mysql
```

参数说明：

- -p 3307:3306 ：映射容器服务的 3307 端口到宿主机的 3306 端口，外部主机可以直接通过 宿主机ip:3307 访问到 MySQL 的服务。
- MYSQL_ROOT_PASSWORD=123456：设置 MySQL 服务 root 用户的密码。


## 2.docker操作mysql创建数据库

### 2.1 确定linux中的docker容器（mysql）正常运行
### 2.2 执行命令：“docker exec -it 容器名称 bash” 进入容器bash-即进入到mysql容器中

### 2.3 登录mysql：mysql -uroot -p      使用root账号登录mysql
### 2.4 页面提示输入密码——数据root 账号所属密码，登录到mysql中
### 2.5 create database 数据库名称  命令创建新数据库；
> 注：所有的mysql命名末尾一定要加“;”
> mysql基本命令：
> 
> -h数据库主机
> -u用户
> -p密码
> -P端口号（大写P）
> 
> 例如：mysql -h127.0.0.1 -uroot -p123456 -P3306
> PS:-p密码部分，可以直接指定密码，如果不指定，会提示输入密码。

## 3.将准备好的sql文件导入到docker中的mysql中

### 3.1 备份sql文件存储目录


```
/data
```

### 3.2 查看当前mysql容器名称

```
docker ps
```

### 3.3 将sql导入mysql


```
docker exec -i mysql5.7 mysql -uroot -p123456 mydb < /data/mydb.sql
```
> 注：
>
> mydb 为：数据库名
>
> mysql5.7 为：容器名


