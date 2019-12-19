---
layout: post
title: Code
date: 2019-12-19 10:00:00
tags: code
author:超哥
---

# 4. CURD 操作
> CURD 是一个数据库技术中的缩写词，一般的项目开发的各种参数的基本功能都是 CURD。
>
> 它代表 创建（Create）、 更新（Update） 、 读取（Read） 和 删除（Delete） 操作。

## 4.1 添加数据

### 4.1.1 添加单条数据

```mysql
# 字段名 使用反引号包裹，插入的值 使用单/双引号包裹

# 1). 为表的 【所有字段】 插入数据
# 必须列出所有 字段的值
mysql> INSERT INTO `表名` VALUES ('值1','值2'...'值N');

# 列出所有 字段 和 值，根据指定的 字段顺序，赋值相应的 字段值
mysql> INSERT INTO `表名` (`字段1`,`字段2`...`字段N`) VALUES ('值1','值2'...'值N');

# 2). 为表的 【指定字段】 插入数据
# 根据指定的 字段顺序，赋值相应的 字段值
mysql> INSERT INTO `表名` (`字段1`,`字段2`) VALUES ('值1','值2');
```

### 4.1.2 添加多条数据

```mysql
# 列出所有的 字段的值，多个值之间 用逗号隔开。
mysql> INSERT INTO `表名` VALUES
('值1','值2'...'值N'),
('值1','值2'...'值N'),
('值1','值2'...'值N');

# 指定字段的 多条插入
mysql> INSERT INTO `表名` (`字段1`,`字段2`) VALUES
('值1','值2'),
('值1','值2'),
('值1','值2');

# 将查询结果插入到表中
mysql> INSERT INTO `表名1` (字段列表1) SELECT 查询字段列表 FROM `表名2` [WHERE 条件表达式];
```

## 4.2 查询数据 (基本版)

```mysql
# 查询某表 全部的数据
mysql> SELECT * FROM `表名`;

# 查询某表中 某些字段的数据
mysql> SELECT `字段1`,`字段2` FROM `表名`;

# 查询指定记录
mysql> SELECT `字段1`,`字段2` FROM `表名` WHERE `字段`='值';
```

## 4.3 更新数据

```mysql
# 更新满足条件数据的字段值
mysql> UPDATE `表名`
SET `字段1`='新值1',`字段2`='新值2'...`字段N`='新值N'
WHERE 条件表达式;

# PS. 更新数据，一定要设置"WHERE"条件，否则更新整表数据！！
```

## 4.4 删除数据

### 4.4.1 删除指定数据
```mysql
# 删除满足条件的数据
mysql> DELETE FROM `表名` WHERE 条件表达式;

# 删除数据，一定要设置"WHERE"条件，否则删除整表数据！！
# 删除数据，一定要设置"WHERE"条件，否则删除整表数据！！
# 删除数据，一定要设置"WHERE"条件，否则删除整表数据！！
```

### 4.4.2 清空表数据

```mysql
TRUNCATE TABLE `表名`;
```

----

# 5. 导入/导出 数据表

> 此操作需要在`没有登录 MySQL`的状态下进行。

## 5.1 导出数据

```shell
# 导出 指定数据库
$ mysqldump -u用户名 -p  库名 > 保存SQL文件.sql
# 密码会在 回车后输入

# 导出 指定数据库里 指定的表
$ mysqldump -u root -p 库名 表名1 表名2 > Backup.sql
```

## 5.2 导入数据
```shell
$ mysql -u用户名 -p 库名 < SQL文件.sql
```

## 5.3 使用工具操作 导入/导出

还可以使用软件或工具，来便捷的实现数据导入/导出操作
- [phpMyAdmin](https://www.phpmyadmin.net/)
- [Navicat](https://www.navicat.com.cn/)
- IEDA 编辑器

----

# 6. 修改密码

登陆 MySQL 修改 root 密码（WAMP 默认是无密码）
其原理就是替换 `mysql`.`user` 中 `root` 用户的密码字段值

## 6.1 可以登陆时 正常修改密码

```mysql
# 登陆 MySQL
$ mysql -u root -p

# 使用 mysql 数据库
mysql> use mysql;

# 新密码必须使用`password()`加密函数 进行加密，并更新到 mysql 数据库的密码字段

# MySQL 5.7 以前版本，密码字段为 Password
mysql> UPDATE `user` SET `Password`=password('new_password') WHERE `user`='root';

# MySQL 5.7 以后版本，Password 字段改为了 authentication_string
mysql> UPDATE `user` SET `authentication_string`=password('new_password') WHERE `user`='root';

# 刷新 MySQL 的系统权限相关表
mysql> flush privileges;

# 退出
mysql> exit;
```

## 6.2 无法登陆时 强制修改

1. 关闭 MySQL 服务
2. 打开 DOS 窗口，进入到 MySQL 下的 **`bin`**目录
3. 输入执行**`mysqld --skip-grant-tables` **
   - 意思为，启动 MySQL 服务的时候，跳过权限表认证，即跳过密码验证
   - 此时 DOS 窗口 已经不能动了，它已经占用 3306 端口了
   - 此窗口**`勿关`**

4. 再开一个 DOS 窗口，输入**`mysql`**回车，如果执行成功，将出现 MySQL 提示符
5. 选择连接权限数据库 **`use mysql`**
   - 可以输入 `select * from user\G` 查看`user`内的数据情况
6. 强制修改密码：
   - 5.7+（注意修改你的新密码）
     - **`UPDATE user SET authentication_string=password("你的新密码") WHERE user="root";`**
   - 5.7-（注意修改你的新密码）
     - `UPDATE user SET password=password("你的新密码") WHERE user="root";`
7. 刷新权限：**`flush privileges;`**
8. 退出所有DOS窗口，并**重启 MySQL 服务**，使用新密码登录

----

# 7. 分配权限

```mysql
# 授权
GRANT 权限            (SELECT,INSERT,UPDATE,DELETE)
ON 哪个库.哪个表       (*.*  表示 全部库 全部表)
TO 用户名@'登陆主机'   (登录主机 `%` 表示 任何 IP 皆可)
IDENTIFIED BY '密码'  (密码)

# 刷新权限
flush privileges;

# 收回权限
REVOKE 权限                (收回什么权限)
ON 哪个库.哪个表            (指定库/表)
FROM '用户名'@'登陆主机';   (谁在哪个IP收回)

# 删除用户
DROP USER '用户名'@'登陆主机';
```

----

# PS. 补充说明

学习时，解决 mysql 内存，占用过高问题
将以下两行 写入 `my.ini` 配置文件，并重启 MySQL 服务

```ini
table_open_cache=256
table_definition_cache=400
```