---
title: Django+Apache wsgi 部署
author: woobamboo
authorURL:
authorFBID:
---



+ 找对了文档非常简单...

<!--truncate-->

> 官方文档非常简单，而且能确保正确。网上的博客质量参差不齐，或者因为软件发展太快，导致可能会有各种错误。

## 基础设置

> 首先设置`http.conf` 

```
WSGIScriptAlias / /path/to/mysite.com/mysite/wsgi.py # 这里 / 我设置成了 /dbweb
WSGIPythonHome /path/to/venv
WSGIPythonPath /path/to/mysite.com

<Directory /path/to/mysite.com/mysite>
<Files wsgi.py>
Require all granted
</Files>
</Directory>
```

## 使用`mod_wsgi` 

Django官方使用的是 `official mod_wsgi`, 我们直接去看看如何安装上这个模块。进入：[installation and configuration documentation](https://modwsgi.readthedocs.io/en/develop/installation.html) ,按照说明：

```
wget https://github.com/GrahamDumpleton/mod_wsgi/archive/4.6.5.zip # 更新很快，自己去看看新版本。
./configure --with-apxs=/usr/local/apache/bin/apxs --with-python=/usr/local/bin/python
make
make install
```

然后在`httpd.conf` 添加一行

```
LoadModule wsgi_module modules/mod_wsgi.so
```

并重启Apache服务。



## 参考

+ [How to use Django with Apache and `mod_wsgi`](https://docs.djangoproject.com/zh-hans/2.1/howto/deployment/wsgi/modwsgi/#how-to-use-django-with-apache-and-mod-wsgi)