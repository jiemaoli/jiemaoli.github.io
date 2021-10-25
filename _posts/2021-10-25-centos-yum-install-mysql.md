---
title: CentOS Yum安装MySQL
description: CentOS 7使用Yum方式安装MySQL 5.7.34
categories:
 - MySQL
tags:
 - CentOS
 - MySQL
updated: 
---

> CentOS 7.0发布以来，yum源中开始使用Mariadb来代替MySQL的安装。即使你输入的是yum install -y mysql, 显示的也是Mariadb的安装内容。如果想使用yum安装MySQL，就需要去下载官方指定的yum源。本文记录使用yum方式安装 MySQL 5.7.34 社区版。

<!-- more -->
## 环境
* Linux `CentOS 7.9 64位`
* MySQL `MySQL 5.7.34`
* User `root`

## 安装前
安装前先检查是否已安装当前版本的MySQL，如果已安装可以跳过安装或删除重新安装
### 检查是否已安装的MySQL，可以使用如下方式
1. 使用`whereis mysql`查看
```sh
[root@VM_0_15_centos ~]# whereis mysql
mysql: /usr/bin/mysql /usr/lib64/mysql /usr/share/mysql /usr/share/man/man1/mysql.1.gz
```
2. 使用 `rpm -qa | grep mysql`
```sh
[root@VM_0_15_centos ~]# rpm -qa | grep mysql
mysql-community-client-5.7.34-1.el7.x86_64
mysql-community-common-5.7.34-1.el7.x86_64
mysql-community-server-5.7.34-1.el7.x86_64
mysql57-community-release-el7-10.noarch
mysql-community-libs-compat-5.7.34-1.el7.x86_64
mysql-community-libs-5.7.34-1.el7.x86_64
```
3. 使用 `systemctl status mysqld.service`
```sh
[root@VM_0_15_centos ~]# systemctl status mysqld.service
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since Wed 2021-06-23 09:30:45 CST; 4 months 2 days ago
     Docs: man:mysqld(8)
           http://dev.mysql.com/doc/refman/en/using-systemd.html
 Main PID: 8508 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─8508 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid
```
执行以上命令中的任意一个，如果有返回mysql相关的信息，刚证明已安装mysql。
### 删除mysql
#### 停止服务
```sh
[root@VM_0_15_centos ~]# systemctl stop mysqld.service
```
#### 卸载组件
* 查看安装了哪些组件
```sh
[root@VM_0_15_centos ~]# yum list installed | grep mysql
Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
mysql-community-client.x86_64           5.7.34-1.el7                   @mysql57-community
mysql-community-common.x86_64           5.7.34-1.el7                   @mysql57-community
mysql-community-libs.x86_64             5.7.34-1.el7                   @mysql57-community
mysql-community-libs-compat.x86_64      5.7.34-1.el7                   @mysql57-community
mysql-community-server.x86_64           5.7.34-1.el7                   @mysql57-community
mysql57-community-release.noarch        el7-10                         @/mysql57-community-release-el7-10.noarch
```
* 卸载组件
```sh
[root@VM_0_15_centos ~]# yum remove mysql-community-client.x86_64
[root@VM_0_15_centos ~]# yum remove mysql-community-common.x86_64
[root@VM_0_15_centos ~]# yum remove mysql-community-libs.x86_64
[root@VM_0_15_centos ~]# yum remove mysql-community-libs-compat.x86_64
[root@VM_0_15_centos ~]# yum remove mysql-community-server.x86_64
[root@VM_0_15_centos ~]# yum mysql57-community-release.noarch
```
* 查看是否卸载完成
```sh
[root@VM_0_15_centos ~]# rpm -qa | grep mysql
```
#### 查找mysql相关的目录并删除
* 查看mysql相关目录
```sh
[root@VM_0_15_centos ~]# find / -name mysql
/etc/selinux/targeted/active/modules/100/mysql
/usr/share/mysql
/var/lib/mysql
/var/lib/mysql/mysql
```
* 删除
```sh
[root@VM_0_15_centos ~]# rm -rf /etc/selinux/targeted/active/modules/100/mysql/ /usr/share/mysql/ /var/lib/mysql/
```
## 下载安装mysql yum源
* 获取下载链接
[yum下载地址](https://dev.mysql.com/downloads/repo/yum/)找到Red Hat Enterprise Linux 7 / Oracle Linux 7 (Architecture Independent), RPM Package，单击后面的Download，在新的页面中单击最下面的No thanks, just start my download，获取到最新的yum源，最新的源会包含历史版本的安装文件。
* 执行下载命令
```sh
[root@VM_0_15_centos ~]# cd /usr/local/
[root@VM_0_15_centos local]# wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm
```
* 安装资源库
```sh
[root@VM_0_15_centos local]# yum localinstall mysql80-community-release-el7-3.noarch.rpm
```
* 资源库安装是否成功
```sh
[root@VM_0_15_centos local]# yum repolist enabled | grep "mysql.*-community.*"
mysql-connectors-community/x86_64    MySQL Connectors Community              221
mysql-tools-community/x86_64         MySQL Tools Community                   135
mysql80-community/x86_64             MySQL 8.0 Community Server              301
```
## 安装 mysql 5.7
* 选择要安装 的版本
```sh
# 查看 yum 仓库 mysql 的列表
[root@VM_0_15_centos local]# yum repolist all | grep mysql
# 只查看启用的
[root@VM_0_15_centos local]# yum repolist enabled | grep mysql
mysql-connectors-community/x86_64    MySQL Connectors Community              221
mysql-tools-community/x86_64         MySQL Tools Community                   135
mysql80-community/x86_64             MySQL 8.0 Community Server              301
```
* 关闭80，开启57
```sh
# 安装 YUM 管理工具包，此包提供了 yum-config-manager 命令工具
[root@VM_0_15_centos local]# yum install yum-utils
# 禁用 8.0
[root@VM_0_15_centos local]# yum-config-manager --disable mysql80-community
# 启用 5.7
[root@VM_0_15_centos local]# yum-config-manager --enable mysql57-community
```
* 再次确认启用的mysql版本
```sh
[root@VM_0_15_centos local]# yum repolist enabled | grep mysql
mysql-connectors-community/x86_64    MySQL Connectors Community              221
mysql-tools-community/x86_64         MySQL Tools Community                   135
mysql57-community/x86_64             MySQL 5.7 Community Server              544
```
* 安装 mysql 5.7
```sh
[root@VM_0_15_centos local]# yum install -y mysql-community-server
```
* 启动服务并设置为开机启动
[root@VM_0_15_centos local]# systemctl start mysqld.service
[root@VM_0_15_centos local]# systemctl enable mysqld.service

