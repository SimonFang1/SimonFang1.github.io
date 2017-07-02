---
layout: post
title: 利用 MySQL Workbench 进行逆向工程——导出ER图
date: 2017-05-19
categories: blog
tags: [系统分析与设计, 数据库]
description: 逆向工程——导出ER图
---

## 安装 MySQL Workbench 

```bash
sudo apt-get install mysql-workbench
```

## 启动  MySQL Workbench 

```bash
sudo mysql-workbench
```

## 连接本地数据库

![连接本地数据库](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/workbench1.png)


## 逆向工程
![逆向工程](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/workbench2.png)

选择要逆向工程的数据库，使用默认配置下一步即可

![选择数据库](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/workbench3.png)

![下一步](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/workbench4.png)

## 导出结果
![导出结果](https://raw.githubusercontent.com/SimonFang1/SimonFang1.github.io/master/img/blog/workbench5.png)

ER图及MySQL创建源码见
[Github仓库文档](https://github.com/TicketingProject/doc/blob/master/teamwork_files/t_GDPLS_11_database_model.pdf)
