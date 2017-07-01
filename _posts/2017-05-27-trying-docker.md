---
layout: post
title: Dock初体验
date: 2017-05-27
categories: blog
tags: [系统分析与设计]
description: Docker初体验
---

## 环境检查
Ubnutu操作系统必须是64bit,并且内核版本必须大于或者等于3.1.0
```bash
uname -r
```
![ubuntu 版本](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/dock1.png)

##  安装

更新Ubuntu软件库
```bash
sudo apt-get update
```

安装Docker
```bash
sudo apt-get install docker.io
```

安装后，docker已启动


## 检查安装
1. 检查Docker进程是否已经启动
1. 检查Docker的版本

![检查Docker进程是否已经启动](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker2.png)

不以管理员运行，会提示无法连接到Docker Daemon
![不以管理员运行](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker3.png)

![以管理员运行](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker4.png)

## 使用

1. **交互式容器**
当退出Docker环境,整个生命周期结束.
2. **守护式容器**
当退出Docker环境,整个生命周期没有结束.

**docker run** 命令用于运行容器。

docker run 是最常用也是最复杂的命令，通过 **docker help run** 我们可以看到该命令有很多选项。其中最常用的选项包括：

* -i -t 这两个选项经常一起使用，可以缩写为 -it，用于创建交互式容器
* -d    让容器运行在后台，用于创建守护式容器
* -name 为容器制定一个名称

容器运行成功后，会返回一个64字符的**容器ID**，作为容器的唯一标识。类似于镜像ID，容器ID也可以采用简写形式。

### 创建容器
如果本地镜像列表中没有需要创建的容器所需的镜像，则自动从Docker的镜像仓库中去PULL镜像，然后利用PULL下来的镜像创建容器，这个过程可能比较慢。

![创建容器](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker5.png)

### 使用容器

![使用容器](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker6.png)

### 查看容器的状态

![查看容器的状态](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker7.png)

### 查看镜像

![查看镜像](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/docker8.png)
