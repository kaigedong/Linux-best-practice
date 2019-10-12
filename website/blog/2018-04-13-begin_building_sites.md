---
title: 开始建站Hexo+NexT
author: woobamboo
authorURL:
authorFBID:
---



# 从零开始建站笔记 Hexo+NexT

<!--truncate-->

## 1. 初始化blog文件夹

```
 mkdir HexoBlog
 cd HexoBlog
 hexo init
 npm install # 安装依赖包
 hexo g
 hexo s
 
 # 发一篇新文章：
 hexo new 'test'
 hexo s -g
```

## 2.1 GitHub信息配置

```
# 创建github repo：kaigedong.github.io,并复制Repository的地址（ssh格式） 
# 打开_config.yml文件并修改：
deploy:
  type: git
  repository: git@github.com:kaigedong/kaigedong.github.io.git
  branch: master

# 生成ssh文件：
ssh-keygen -t rsa -C "dongkaige@gmail.com"
ssh-agent -s
ssh-add ~/.ssh/id_rsa

# 添加到GitHub：
复制~/.ssh/id_rsa.pub 内容 -- “Settings” -- "SSH and GPG keys" -- 填写

# 验证：
ssh -T git@github.com
>>>Hi kaigedong! You've successfully authenticated, but GitHub does not provide shell access.
```

## 2.2 发布到GitHub

```
开始部署：
hexo clean
hexo g
hexo d
>>>ERROR Deployer not found: git
npm install hexo-deployer-git --save
hexo d
```

## 2.3 安装NexT主题

```
git clone https://github.com/theme-next/hexo-theme-next themes/next
更改主目录下_config.yml:
theme: next
```

## 2.4 hexo 支持更简单的命令格式了，比如：

```
hexo g ==  hexo generate
hexo d ==  hexo deploy
hexo s ==  hexo server
hexo n ==  hexo new
```

## 2.5 Tips

1.如何设置「阅读全文」？
> 在文章中使用 <!--truncate--> 手动进行截断，Hexo 提供的方式 **推荐**

2.换电脑了怎么办？

> ```
> 在github创建 HexoBlog
> 然后将本地仓库与之关联并推送到github仓库：
> git init
> git add .
> git commit -m "初始化添加Hexo所有文件"
> git remote add origin git@github.com:kaigedong/HexoBlog.git # 根据GitHub提示
> # 添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。
> git push -u origin master # 把本地内容推送到远程库
> ```

3.发布文章流程

> ```
> hexo s # 本地显示
> hexo new "test"
> hexo g
> hexo d
>
> git add .
> git commit -m "修改Hexo站点说明"
> git push origin master
> ```
> 





## 参考

+ [hexo+github教程之一：搭建hexo博客](https://blog.csdn.net/u012443858/article/details/60811236)


+ [使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)
+ [Hexo的版本控制与持续集成](https://formulahendry.github.io/2016/12/04/hexo-ci/)
+ [廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/0013752340242354807e192f02a44359908df8a5643103a000)


