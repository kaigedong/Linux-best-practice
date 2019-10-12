---
title: golang笔记
author: woobamboo
authorURL:
authorFBID:
---


记录一些Golang

<!--truncate-->

##初识

> 编辑器

推荐[liteide](https://github.com/visualfc/liteide/releases/tag/x34.1)

> 统一代码风格：

```
$gofmt -w program.go # 格式化该源文件的代码并覆盖源文件
$gofmt map1 # 格式化并重写map1文件夹下所有的go源文件
```

> 生成代码文档

go doc 工具会从 Go 程序和包文件中提取顶级声明的首行注释以及每个对象的相关注释，并生成相关文档。

> GO自带的其他工具

`go install`: 安装GO包的工具；`go fix`：将GO代码从旧的发行版迁移到新的发行版; `go test`是轻量级的单元测试框架。

## 了解

工具`cgo`退工了对FFI(外部函数接口)的支持，能够使用Go代码安全的调用C语言库。

如要在Go中使用cgo，需在单独一行使用`import "C"` ，一般还要`import "unsafe"`. 然后可以在`import "C"`之前使用注释(单行或多行注释均可)的形式导入C语言库(甚至是有效的C语言代码)，它们之间没有空行。

```
// #include <stdio.h>
// #include <stdlib.h>
import "C"
```

























