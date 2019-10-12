---
title:  Linux crontab定时任务
author: woobamboo
authorURL:
authorFBID:
---

Linux crontab是用来定期执行程序的命令。

<!--truncate-->

## 简介

crond命令每分钟会定期检查是否有要执行的工作，如果有要执行的工作便会自动执行该工作。

而linux任务调度的工作主要分为以下两类：

- 1、系统执行的工作：系统周期性所要执行的工作，如备份系统数据、清理缓存
- 2、个人执行的工作：某个用户定期要做的工作，例如每隔10分钟检查邮件服务器是否有新信，这些工作可由每个用户自行设置

crontab 是用来让使用者在固定时间或固定间隔执行程序之用，换句话说，也就是类似使用者的时程表。

## 语法：

```shell
crontab [ -u user ] file
```

或

```shell
crontab [ -u user ] { -l | -r | -e }
```

- -u user 是指设定指定 user 的时程表，这个前提是你必须要有其权限(比如说是 root)才能够指定他人的时程表。如果不使用 -u user 的话，就是表示设定自己的时程表。
- -e : 执行文字编辑器来设定时程表，内定的文字编辑器是 VI，如果你想用别的文字编辑器，则请先设定 VISUAL 环境变数来指定使用那个文字编辑器(比如说 setenv VISUAL joe)
- -r : 删除目前的时程表
- -l : 列出目前的时程表

### 时程表的格式如下：

```
f1 f2 f3 f4 f5 program
```

 其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。

- 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其余类推
- 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推
- 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推
- 当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推

使用者也可以将所有的设定先存放在文件中，用 crontab file 的方式来设定时程表。

## 常用命令格式：

```shell
  Schedule cron jobs to run on a time interval for the current user.
  Job definition format: "(min) (hour) (day_of_month) (month) (day_of_week) command_to_execute".

  Edit the crontab file for the current user:

      crontab -e

  Edit the crontab file for a specific user:

      sudo crontab -e -u user

  View a list of existing cron jobs for current user:

      crontab -l

  Remove all cron jobs for the current user:

      crontab -r

  Sample job which runs at 10:00 every day (* means any value):

      0 10 * * * command_to_execute

  Sample job which runs every minute on the 3rd of April:

      * * 3 Apr * command_to_execute

  Sample job which runs a certain script at 02:30 every Friday:

      30 2 * * Fri /absolute/path/to/script.sh
```

## 例子

```
0 * * * * /bin/ls # 每月每天每小时的第 0 分钟执行一次 /bin/ls

0 6-12/3 * 12 * /usr/bin/backup # 在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 /usr/bin/backup

0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata # 0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata

20 0-23/2 * * * echo "haha" # 20 0-23/2 * * * echo "haha"

0 */2 * * * /sbin/service httpd restart # 每两个小时重启一次apache 

50 7 * * * /sbin/service sshd start # 每天7：50开启ssh服务 

50 22 * * * /sbin/service sshd stop # 每天22：50关闭ssh服务 

0 0 1,15 * * fsck /home  # 每月1号和15号检查/home 磁盘 

1 * * * * /home/bruce/backup # 每小时的第一分执行 /home/bruce/backup这个文件 

00 03 * * 1-5 find /home "*.xxx" -mtime +4 -exec rm {} \;  # 每周一至周五3点钟，在目录/home中，查找文件名为*.xxx的文件，并删除4天前的文件。
30 6 */10 * * ls  # 每月的1、11、21、31日是的6：30执行一次ls命令
```

