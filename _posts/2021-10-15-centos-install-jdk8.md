---
title: CentOS安装JDK8
description: CentOS 7.9安装JDK 1.8
categories:
 - java
tags:
 - CentOS
 - JDK 1.8
---

> Java开发的第一步是搭建开发环境，首先需要安装JDK。文章记录在CentOS下安装JDK 1.8的过程。

<!-- more -->
## 环境
* Linux `CentOS 7.9 64位`
* JDK `jdk1.8.0_291`
* User `root`

## 安装前
安装前需要先检测是否已安装当前版本的JDK，或其它JDK，如Linux系统通常都会默认安装`OpenJDK`
### 查看已安装的JDK版本
```sh
[root@sz-newtest1 ~]# java -version
openjdk version "1.8.0_292"
OpenJDK Runtime Environment (build 1.8.0_292-b10)
OpenJDK 64-Bit Server VM (build 25.292-b10, mixed mode)
```
当前系统中安装了`OpenJDK 1.8.0_292`，不是我们要安装的版本，先删除。
### 获取JDK安装路径
1. 如果配置了JDK环境变量，可以使用 echo $JAVA_HOME 定位安装路径
```sh
echo $JAVA_HOME
```
2. 如果没有配置环境变量，使用 which java 来查找
```sh
[root@sz-newtest1 ~]# which java
/usr/bin/java
[root@sz-newtest1 ~]# ls -ltr /usr/bin/java
lrwxrwxrwx. 1 root root 22 6月  28 12:13 /usr/bin/java -> /etc/alternatives/java
[root@sz-newtest1 ~]# ls -ltr /etc/alternatives/java
lrwxrwxrwx. 1 root root 73 6月  28 12:13 /etc/alternatives/java -> /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64/jre/bin/java
```
> `/usr/lib/jvm/` 即是安装路径，`which java`直接使用是定位不到安装路径的，需要配合其它命令使用。
### 删除旧版本JDK
```sh
[root@sz-newtest1 ~]# cd /usr/lib/jvm
[root@sz-newtest1 jvm]# ls
java-1.7.0-openjdk-1.7.0.261-2.6.22.2.el7_8.x86_64  jre-1.7.0-openjdk-1.7.0.261-2.6.22.2.el7_8.x86_64
java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64     jre-1.8.0
jre                                                 jre-1.8.0-openjdk
jre-1.7.0            `                               jre-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64
jre-1.7.0-openjdk                                   jre-openjdk
[root@sz-newtest1 jvm]# rm -rf java-1.8.0-openjdk-1.8.0.292.b10-1.el7_9.x86_64/
```
## 下载
* 获取下载链接
从[Oracle官网](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)获取JDK8 `Linux x64 Compressed Archive` 版本的下载链接。如果提示登录，可以使用账号`2696671285@qq.com` `Oracle123` 网络账号不保证可用性
* 执行下载命令
```sh
[root@sz-newtest1 ~]# cd /usr/local
[root@sz-newtest1 local]# mk dir java
[root@sz-newtest1 local]# cd java/
[root@sz-newtest1 java]# wget https://download.oracle.com/otn/java/jdk/8u291-b10/d7fc238d0cbf4b0dac67be84580cfb4b/jdk-8u291-li23989297_7ff18f9e0311551781e2bf448174efd4
```
> 下载地址为带时间戳的地址，可能过期，需要去页面点击下载按钮重新生成
* 查看安装包大小
```sh
[root@sz-newtest1 java]# ls -lh
[root@sz-newtest1 java]# jdk-8u291-linux-x64.tar.gz\?AuthParam\=1623989297_7ff18f9e0311551781e2bf448174efd4
```
* 重命名
```sh
[root@sz-newtest1 java]# mv jdk-8u291-linux-x64.tar.gz\?AuthParam\=1623989297_7ff18f9e0311551781e2bf448174efd4 jdk-8u291-linux-x64.tar.gz
```
## 安装
* 解压
```sh
[root@sz-newtest1 java]# tar -xvf jdk-8u291-linux-x64.tar.gz
```
* 配置环境变量
```sh
[root@sz-newtest1 java]# vi /etc/profile
```
按键盘`i`键 进入编辑模式，在文件末尾追加配置信息
```
#set java environment
JAVA_HOME=/root/bin/java/jdk1.8.0_291
JRE_HOME=/root/bin/java/jdk1.8.0_291
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```
按`Esc`退出编辑模式，按`:qw`，退出并保存修改。
* 环境变量生效
```sh
[root@sz-newtest1 java]# source /etc/profile
```
## 验证
```sh
[root@sz-newtest1 java]# java -version
java version "1.8.0_291"
Java(TM) SE Runtime Environment (build 1.8.0_291-b10)
Java HotSpot(TM) 64-Bit Server VM (build 25.291-b10, mixed mode)
```
