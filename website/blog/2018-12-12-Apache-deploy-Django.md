---
title: Apache 部署 Django
author: woobamboo
authorURL:
authorFBID:
---

Apache + uwsgi部署Django

<!--truncate-->



Apache 部署Django的尝试

+ Apache版本：见前面博文

+ Python环境：

  ```
  Django==2.1.3
  django-datatables-view==1.17.0
  mysql-connector-python==8.0.5
  protobuf==3.6.1
  pytz==2018.7
  six==1.11.0
  uWSGI==2.0.17.1
  ```



Apache 需要加载`modules/mod_wsgi.so`模块，python也要安装`uwsgi`。

在https://github.com/unbit/uwsgi/blob/master/apache2/mod_uwsgi.c 下载该文件，使用Apache提供的`apxs`自己编译：

```
wget https://github.com/unbit/uwsgi/blob/master/apache2/mod_uwsgi.c
apxs -i -a -c mod_uwsgi.c # 这样就会在modules文件夹增加mod_wsgi.so， 并且会自动修改http.conf,我们只要重启Apache就行了

```





## 参考

http://www.iitshare.com/apache-uwsgi-django-multi-sites.html