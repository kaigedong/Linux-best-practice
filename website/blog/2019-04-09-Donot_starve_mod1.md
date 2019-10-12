---
title: 饥荒mod1
author: woobamboo
authorURL:
authorFBID:
---

如何制作饥荒mod教程

<!--truncate-->






快速开始

1. 如何安装并使用mod？

     + 从steam 创意工坊安装并启用 略。

     + 从Klei下载并安装：
       + 首先转到下载页面：https://forums.kleientertainment.com/files/， 下载喜欢的mod

       + 然后双击打开下载的压缩包，并将文件夹拷贝到mod文件夹：

         ```
         Steam/Windows: C:\Program Files (x86)\Steam\SteamApps\common\dont_starve\mods\
         Standalone/Windows: C:\Program Files\dontstarve\mods
         Standalone/Mac: /Applications/Don't Starve/Don't Starve.app/Contents/mods/
         Steam/Linux: /home/username/.local/share/Steam/SteamApps/common/dont_starve/mods
         ```

       + 然后打开饥荒，并在饥荒中启用该mod

2. 开始之前

     需要对困难有一定的认知。

     要学一点lua语言。

3. Mod API更新(API V6, 2014)

   **MOD_API_VERSION** 如名，当时的API版本是6，在Don’t Starve Together之前不会增加新API了。

   **Mod Capatibility** Mod兼容性很重要，最好在modinfo.lua中指明兼容性，不指名会有警告的log。(我猜大概是`modinfo.lua` 中的`api_version`参数)

> 把你的mod升级到新的mod 兼容性

3.1.打开modinfo.lua

3.2.添加`dont_starve_compatible` 和 `reign_of_giants_compatible` 布尔变量到`modinfo`，比如`dont_starve_compatible = falsereign_of_giants_compatible = true`, 这些兼容性信息将会在mod列表的名字处显示。

一个例子是(`modinfo.lua`)：

![image-20190410092130663](..assets/2019-04/image-20190410092130663.png)

**Mod Configuration Options** 是新特性，允许给他们的mod设一组选项。就是在modinfo.lua 中设置mod的一些参数。这些参数可以通过函数获得。



## Art 篇





