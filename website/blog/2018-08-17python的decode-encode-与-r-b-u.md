---
title: python的decode/encode 与 r/b/u
author: woobamboo
authorURL:
authorFBID:
---


python中的decode/encode 与 r/b/u

<!--truncate-->

## 1.`decode` 与 `encode`

> 字符串在Python内部是`unicode`编码，在做编码转换时，通常要以unicode作为中间编码；即先将其它编码的字符串解码(decode)成unicode，然后再编码`encode`成另一种编码。
>
> **在计算机内存中，统一使用`Unicode`编码，当需要保存到硬盘或者需要传输的时候，就转换为`utf-8`编码。**

`decode`：将其他编码的字符串**转成`unicode`**编码，如`str1.decode('gb2312')`; 表示将`gb2312`编码的字符串`str1`转换成`unicode`编码；

`encode`：将**`unicode`**编码转换成其他编码的字符串，如`str2.encode('gb2312')`; 表示将`unicode`编码的字符串转成`gb2312`编码。

`sys.getdefaultencoding()`：查看系统默认编码

`ord()`获取字符的整数表示

`chr()`把编码转换成对应的字符

## 2.`u`/`r`/`b`







## 3.`utf-8` 与 `unicode`



+ [字符编码](https://www.liaoxuefeng.com/wiki/0014316089557264a6b348958f449949df42a6d3a2e542c000/001431664106267f12e9bef7ee14cf6a8776a479bdec9b9000)