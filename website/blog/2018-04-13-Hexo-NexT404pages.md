---
title: Hexo-NexT主题的404页面
author: woobamboo
authorURL:
authorFBID:
---



+ 设置hexo 下next主题的404页面

> 腾讯公益404页面，寻找丢失儿童，让大家一起关注此项公益事业！

<!--truncate-->

# 设置hexo 下next主题的404页面

按照[Next官网的做法](http://theme-next.iissnan.com/theme-settings.html), 设置`404.html`，则每次`hexo generate`都会将这个html 重新生成。并不可行。

查找github的解决办法，有个**`skip_render`参数**，可以让hexo忽略某个文件，不进行生成，而直接拷贝(对我来说并不可行)。

经过多番尝试，有了下面的解决办法：



## 腾讯404页面介绍：

[腾讯404页面](http://www.qq.com/404/)中给了接入公益404的办法：

```
<script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" charset="utf-8" homePageUrl="http://yoursite.com/yourPage.html" homePageName="回到我的主页"></script>
```



## 解决办法：创建`404.md`而不是`404.html`

于是，我直接将`source`下新建了个`404.md`,

内容: (设置comments是因为即使页面会被腾讯404覆盖，但生成的html代码依然有comments的内容需要加载。)

### 创建`404.md`

> `404.md`的内容

```html
---
comments: false
---

<script type="text/javascript" src="//qzonestyle.gtimg.cn/qzone/hybrid/app/404/search_children.js" homepagename='返回主页' homepageurl="/" charset="utf-8"></script>
```

### 修改`theme/_config.yml`文件

然后将`theme`中的menu的404进行设置(**最后一行**)：

```
menu:
   home: / || home
   about: /about/ || user
   tags: /tags/ || tags
   categories: /categories/ || th
   archives: /archives/ || archive
   #schedule: /schedule/ || calendar
   #sitemap: /sitemap.xml || sitemap
   commonweal: 404.html || heartbeat
```

大功告成！

### 重新生成html文件，并发布

然后到一级目录执行：

```shell
hexo generate
hexo deploy # 推送到github.

#tips: 我在.bashrc进行了如下设置以简便命令：
alias hexo1='hexo generate'
alias hexo2='hexo deploy'
```



效果参见[https://kaigedong.github.io/404.html](https://kaigedong.github.io/404.html)：

![404效果](assets/2017-09/figure_12.jpg)


最后祝愿走失的孩子找到回家的路❤！