---
title: Mac安装MySQL & Ubuntu下安装MySQL记录
author: woobamboo
authorURL:
authorFBID:
---

Mac 下安装MySQL，碰到了很多坑，这里记录一下。
<!--truncate-->

碰到很多问题，网上的教程没有完整的解决。

```
Navicat错误：2003 - Can't connect to MySQL server on '127.0.0.1' (61 "Connection refused")

ERROR 1045 (28000): Access denied for user 'mysql'@'localhost' (using password: NO)

$mysqld start
mysqld: Can't change dir to '/usr/local/mysql-8.0.11-macos10.13-x86_64/data/' (OS errno 13 - Permission denied)
2018-05-08T05:38:56.432352Z 0 [System] [MY-010116] [Server] /usr/local/mysql-8.0.11-macos10.13-x86_64/bin/mysqld (mysqld 8.0.11) starting as process 60460
2018-05-08T05:38:56.450820Z 0 [Warning] [MY-010091] [Server] Can't create test file /usr/local/mysql-8.0.11-macos10.13-x86_64/data/research-imac72.lower-test
2018-05-08T05:38:56.450857Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /usr/local/mysql-8.0.11-macos10.13-x86_64/data/ is case insensitive
2018-05-08T05:38:56.451089Z 0 [ERROR] [MY-010172] [Server] failed to set datadir to /usr/local/mysql-8.0.11-macos10.13-x86_64/data/
2018-05-08T05:38:56.451102Z 0 [ERROR] [MY-010119] [Server] Aborting
2018-05-08T05:38:56.453321Z 0 [System] [MY-010910] [Server] /usr/local/mysql-8.0.11-macos10.13-x86_64/bin/mysqld: Shutdown complete (mysqld 8.0.11)  MySQL Community Server - GPL.

$sudo chmod -R 755 /usr/local/mysql-8.0.11-macos10.13-x86_64/data/
$ mysqld start
2018-05-08T05:40:10.060603Z 0 [System] [MY-010116] [Server] /usr/local/mysql-8.0.11-macos10.13-x86_64/bin/mysqld (mysqld 8.0.11) starting as process 60542
2018-05-08T05:40:10.064303Z 0 [Warning] [MY-010091] [Server] Can't create test file /usr/local/mysql-8.0.11-macos10.13-x86_64/data/research-imac72.lower-test
2018-05-08T05:40:10.064319Z 0 [Warning] [MY-010159] [Server] Setting lower_case_table_names=2 because file system for /usr/local/mysql-8.0.11-macos10.13-x86_64/data/ is case insensitive
mysqld: File './binlog.index' not found (OS errno 13 - Permission denied)
2018-05-08T05:40:10.064832Z 0 [ERROR] [MY-010119] [Server] Aborting
2018-05-08T05:40:10.065076Z 0 [System] [MY-010910] [Server] /usr/local/mysql-8.0.11-macos10.13-x86_64/bin/mysqld: Shutdown complete (mysqld 8.0.11)  MySQL Community Server - GPL.

```

```
$brew install mysql
......
🍺  /usr/local/Cellar/openssl/1.0.2n: 1,792 files, 12.3MB
==> Installing mysql
==> Downloading https://homebrew.bintray.com/bottles/mysql-5.7.21.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring mysql-5.7.21.high_sierra.bottle.tar.gz
==> /usr/local/Cellar/mysql/5.7.21/bin/mysqld --initialize-insecure --user=kaigedong --basedir=/usr/local/Cellar/mysql/5.7.21 --datadir=/usr/local/var/mysql --tmpdir=/tmp
==> Caveats
We've installed your MySQL database without a root password. To secure it run:
    mysql_secure_installation

MySQL is configured to only allow connections from localhost by default

To connect run:
    mysql -uroot

To have launchd start mysql now and restart at login:
  brew services start mysql
Or, if you don't want/need a background service you can just run:
  mysql.server start
==> Summary
🍺  /usr/local/Cellar/mysql/5.7.21: 323 files, 233.9MB

$mysql_secure_installation # 按要求更改密码，注意，这里最好选0，因为选了1或者2，后面的密码需要满足这个安全条件。
```



> 之前的错误：使用官网下载的8.0版本，手动安装，导致各种文件夹权限问题，端口问题，my.cnf 不存在问题，PID not found问题，由于版本较新，网上教程很少。这里用brew 安装之前的稳定版本，其配置文件在通过 
>
> brew 安装的 MySQL 的配置文件为：`/usr/local/etc/my.cnf`，将`bind-address = 127.0.0.1`注释掉，然后在使用Navicat，连接成功。
> 过段时间在更新到8 ；）

## Ubuntu部分：

1. 安装

   ```
   sudo apt-get install mysql-server
   sudo apt install mysql-client
   sudo apt install libmysqlclient-dev

   sudo netstat -tap | grep mysql 
   $ netstat -tap | grep mysql # 下面的信息，则显示安装成功。
   tcp        0      0 localhost:mysql         0.0.0.0:*               LISTEN      1082/mysqld

   vi /etc/mysql/mysql.conf.d/mysqld.cnf
   注释掉bind-address = 127.0.0.1； 使MySQL允许远程访问

   MySQL 初始用户名密码在：/etc/mysql/debian.cnf
   用mysql -u debian-sys-maint -p 进行登录；
   然后：set password for 'root'@'localhost' = password('yourpass');
   然后进入mysql服务，执行授权命令：
   grant all on *.* to root@'%' identified by '你的密码' with grant option;

   flush privileges; //刷新配置
   然后退出mysql: exit
   然后重启服务：service mysql restart
   ```


   ```




+ [Ubuntu 16.04下安装MySQL](https://www.linuxidc.com/Linux/2017-06/144805.htm)

+ [Ubuntu安装MySQL密码初始化问题](https://blog.csdn.net/theonegis/article/details/51810063)

+ [报错:1130-host ... is not allowed to connect to this MySql server 开放mysql远程连接 不使用localhost](https://www.cnblogs.com/xyzdw/archive/2011/08/11/2135227.html)

+ [MySQL ERROR 1698 (28000) 错误](http://www.cnblogs.com/leolztang/p/5094930.html)


   ```