---
title: 服务器无root安装MySQL
author: woobamboo
authorURL:
authorFBID:
---

+ 无root安装MySQL

<!--truncate-->

# MySQL 安装

> 5.7版本和8.0版本都可以通过这个方法

1. 下载并解压MySQL 5.7

2. MySQL解压后，进入文件夹并新建配置文件`my.cnf`。`my.cnf`的内容：

   ```
   [client]
   local-infile=1
   port=3336
   socket=/path/to/your/mysql.sock
   
   [mysqld]
   local-infile=1
   default-storage-engine=MYISAM
   bind-address = 0.0.0.0
   port=3336
   basedir=/path/to/your/mysql
   datadir=/path/to/your/mysql/data
   pid-file=/path/to/your/mysql/mysql.pid
   socket=/path/to/your/mysql/mysql.sock
   log_error=/path/to/your/mysql/error.log
   server-id=100
   ```

   > `mysql.sock` 位置很重要
   >
   > `bind-address`不设置，或者设置成`0.0.0.0`表示任意IP都可以连接到MySQL
   >
   > 需要设置自动加载本地数据库的参数(**MySQL 8不能这么做，不确定如何做**)：
   >
   > ```
   > [mysqld]
   > local-infile=1
   > 
   > [client]
   > local-infile=1
   > ```
   >
   > 需要设置UCSC支持的引擎：
   >
   > ```
   > [mysqld]
   > default-storage-engine=MYISAM
   > ```

3. 初始化

   ```
   bin/mysqld --defaults-file=/path/of/my.cnf \
   --initialize \ # 自动生成带随机密码的root用户,比如我的是root@localhost: gc&p6Rqh?w3t
   --user=dongkaige \
   --basedir=/path/to/your/mysql_5 \
   --datadir=/path/to/your/mysql_5/data
   ```

   > 查看`error.log`，可以找到初始root密码

4. 更改密码及创建用户

   ```shell
   bin/mysql -u root -p -S /path/to/your/mysql/mysql.sock
   Enter password: 
   
   mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
   #修改密码 
   mysql> flush privileges;
   mysql> show databases;
   +--------------------+
   | Database           |
   +--------------------+
   | information_schema |
   | mysql              |
   | performance_schema |
   | sys                |
   +--------------------+
   4 rows in set (0.00 sec)
   
   # 设置该数据库能够远程登录：
   mysql> use mysql;
   mysql> update user set host='%' where user='root';
   mysql> select host, user from user;
   +-----------+------------------+
   | host      | user             |
   +-----------+------------------+
   | %         | root             |
   | localhost | mysql.infoschema |
   | localhost | mysql.session    |
   | localhost | mysql.sys        |
   +-----------+------------------+
   4 rows in set (0.00 sec)
   
   mysql> fulsh privileges;
   ```

5. 关闭及运行

   + 关闭：`ps aux | grep mysql`；然后根据pid，关掉： `kill pid`

   + 关闭方式2

     ```shell
     bin/mysqladmin shutdown -u root -p
     ```

   + 开启

     ```shell
     bin/mysqld_safe \
     --defaults-file=/path/to/your/mysql/my.cnf \
     --user=dongkaige &
     ```


## 参考

+ [Linux普通用户安装mysql-5.7.21](https://blog.csdn.net/tianpy5/article/details/79842888)
+ [Manual installation of the UCSC Genome Browser on a Unix server](https://genome.ucsc.edu/goldenpath/help/mirrorManual.html)