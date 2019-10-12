---
title: 无root安装Apache
author: woobamboo
authorURL:
authorFBID:
---

简单的操作


<!--truncate-->



# 无root安装Apache

无root安装Apache非常简单

1. 下载(看参考)

2. 根据官网说明安装(参考)

   ```shell
   # 需要下载APR 和 APR-Util,并放入特定位置，并需要安装pcre
   wget -c https://www-eu.apache.org/dist//httpd/httpd-2.4.37.tar.bz2
   wget https://www-us.apache.org/dist//apr/apr-1.6.5.tar.gz
   wget ftp://ftp.csx.cam.ac.uk/pub/software/programming/pcre/pcre-8.42.zip # 注意！不是pcre2!
   wget https://www-us.apache.org/dist//apr/apr-util-1.6.1.tar.gz
   
   # 将 apr, apr-util 解压去掉版本号，移动到 apache的 srclib 下
   
   ./configure --prefix=/picb/rnomics2/RBP_database/dbweb/Softwares/httpd_2.4.37 --with-included-apr --with-pcre=/home/dongkaige/rnomics2/RBP_database/dbweb/Softwares/pcre_8.42
   make
   make install
   ```

3. 启动、关闭与重启

   ```shell
   ./apachectl start
   ./apachectl stop
   ./apachectl restart
   ```


## 参考

+ [Apache24下载位置](http://httpd.apache.org/download.cgi#apache24)
+ [http://httpd.apache.org/docs/current/](http://httpd.apache.org/docs/current/)
+ Apache [配置与优化--之乎专栏](https://www.jianshu.com/p/c36dd3946e74)