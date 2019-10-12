---
title: Acer中Arch安装Secure boot EFI 设置
author: woobamboo
authorURL:
authorFBID:
---

记录一下Arch安装的心得。

<!--truncate-->

安装参考：[以官方Wiki的方式安装ArchLinux](https://www.viseator.com/2017/05/17/arch_install/)

问题1：Win与Arch能否安装到不同的盘，然后进行引导？大概是不能(没找到解决办法)。

问题2：最后一步，Reboot 后， 电脑(Acer VN7 592G) 在打开UEFI 模式下显示找不到启动引导程序。

在之前安装Ubuntu时，打开UEFI模式，无论`secure boot`设置成开还是关，都无法成功安装Windows/Ubuntu双系统。解决办法是将`UEFI`引导设置成`BIOS Legeacy`引导的方式。但当时只是看到教程是这么做的，也没想过为什么。这么多年过去了，UEFI早已成为市场主流，难道还要再用回传统的BIOS引导吗？

在安装Arch时，需要挂载引导盘上的EFI分区，这其中包含了很多开机加载的`.efi`文件，通过`diff`命令查看了一下`EFI`分区中`Windows`下的`efi`和`/EFI/Boot/bootx64.efi`文件夹下的`efi`发现完全一样，这表明安装arch时并没有替换这个文件 ~~(grub安装双系统引导要不要替换这个？)~~。而这个`bootx64.efi`在开机时，无论放的谁家的`efi`,最后肯定会被引导。

于是我将安装`arch`时的`grub`下的一个`efi` 复制并替换`EFI/Boot/bootx64.efi`， 重启还是不行。

最终解决办法：`UEFI` +` Secure boot 开启`时，恢复BIOS系统设定，然后重新手动选择Arch的引导程序，这时重启就能进入`Arch`的引导程序了。~~再进行添加windows引导程序（系统坏了一次，我把windows盘直接格式化了，就没再测试这一步）~~。

Arch真好用~

## UEFI系统引导原理

参考[科普贴：BIOS和UEFI的启动项](https://zhuanlan.zhihu.com/p/31365115)



## Shadowsocks 只能代理部分软件(有些软件不支持设置代理)，怎么才能全局代理？

参考：[如何用 Privoxy 辅助翻墙？](https://program-think.blogspot.com/2014/12/gfw-privoxy.html)

文章写于2014.12 【2002年就已经是 3.0.0 版本。这么多年，才升到 3.0.22，真够慢的。】

现在才到 3.0.26

----

+ HTTP代理转发：

  ```
  　　　｜＝＝＝＝＞｜　　　　　　　｜＝＝＝＝＞｜　　　　　　｜＝＝＝＝＞｜
  浏览器｜　　　　　｜Ｐｒｉｖｏｘｙ｜　　　　　｜ＨＴＴＰ代理｜　　　　　｜目标网站
  　　　｜＜＝＝＝＝｜　　　　　　　｜＜＝＝＝＝｜　　　　　　｜＜＝＝＝＝｜
  ```

+ 把该语法添加到“主配置文件中”
	```
		forward target_pattern http_proxy:port
	```

如：

	```
		forward  /  127.0.0.1:8080
	```


> 表示：把所有HTTP请求都转发给本机

如： 
	```
	forward-socks5 .onion localhost:9050 .
	```
	
> 表示顶级域名为.onion 的HTTP请求都转发给本机(localhost也可以用来表示本机，相当与127.0.0.1 的TOR SOCKS端口)

+ Privoxy 将 socks转发HTTP代理：

  ```
  sudo pacman -S privoxy # arch linux
  mkdir -p ~/.config/privoxy # 默认打开privoxy的位置，其实也可以编辑 /etc/privoxy/config 这个文件，但是要在运行时指定这个位置；否则prifoxy自动去找~/.config/privoxy这个文件
  
  # 向~/.config/prifoxy/config 写入：
  listen-address 127.0.0.1:8118 # 开启监听端口
  forward-socks5 / 127.0.0.1:1080 . # socks5代理端口,注意最后的点。
  
  #启动代理
  privoxy ~/.config/privoxy/config
  #开机启动服务：
  sudo systemctl enable privoxy.service
  ```



+ 参考: [Privoxy将Socks代理转化HTTP代理](https://www.cnblogs.com/zhuxiaoxi/p/9010753.html)