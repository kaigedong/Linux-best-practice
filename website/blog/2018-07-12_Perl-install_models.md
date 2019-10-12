---
title: Perl安装模块与卸载
author: woobamboo
authorURL:
authorFBID:
---

+ 安装Perl 模块的不同方法

<!--truncate-->

## 1. 使用`cpanm` 来安装模块

首先安装`cpanm`, 在终端输入：

```
cpan App::cpanminus 
```

然后可安装你需要的模块：

```
cpanm Module::Name
```

`brew`中的perl 安装好cpanm之后，`which cpanm`还是找不到。

需要：

```shell
cd /data/rnomics6/kaigeD/src/linuxbrew/bin/
ln -s /data/rnomics6/kaigeD/src/linuxbrew/Cellar/perl/5.30.0/bin/cpanm
```



## 2. cpan

```
cpan
install Set::IntervalTree
```

## 3. `perl -MCPAN -e "shell"`

```shell
perl -MCPAN -e "shell"
install Set::IntervalTree

# 或者直接：
perl -MCPAN -e "install Set::IntervalTree"
```

## 4. 测试是否已经安装成功

```
perl -MHTTP::Date -e "print\"module installed\n\""
```

`-M`后紧跟着模块名，如果输出`module installed`的结果，则是安装了的。

## perl 的 @INC

perl中的INC变量中包含了所有的perl module的查找路径。

有时候model安装之后，仍然提示在一些@INC中找不到模块，这时候要进行检查

> 1. 看文件头部是不是`#!/usr/bin/perl`，这时要修改成自己的`perl`
>
> 2. 查看@INC的内容，看我们的模块的路径是否已经被安装上了。@INC可以通过`pelr -V`来查看；路径可以通过`perldoc -l DBD::mysql` 这样查看。
>
> 3. 可以设置环境变量`PERL5LIB`来增加到INC：`set perl5lib=/path/modelspath/` 设置之后，再查看是否成功。
>
> 4. 也可在perl程序中修改INC，毕竟@INC是一个数组而已。
>
>    ```perl
>    #!/usr/bin/perl -w 
>    push(@INC,"/home/test"); 
>    
>    # 或者
>    #!/usr/bin/perl -w 
>    BEGIN{push(@INC,"/home/test")};
>    
>    #!/usr/bin/perl -w 
>    use lib '/home/test';
>    ```
>

## 5.修改cpan源

```
# 显示cpan源的配置文件位置
perl -MCPAN -e shell
cpan> o conf init 

# 发现是在/home/dongkaige/.cpan/CPAN/MyConfig.pm 中配置的
# 修改/home/dongkaige/.cpan/CPAN/MyConfig.pm 文件中的

'urllist' => [q[http://mirrors.163.com/cpan/]],

# 即可。

```

### cpanm 指定源

```
cpanm -M http://mirrors.163.com/cpan/ App::perlbrew
```



## 其他有用的命令

#### 列出已安装模块
```
perldoc perllocal
# 或
instmodsh # 推荐这一种
```

#### 查询perl模块安装路径
```
perldoc -l DBD::mysql
```

#### 查询模块版本
(`-M`紧跟着跟着模块)
```
$ perl -MHTTP::Date -e 'print HTTP::Date->VERSION. "\n"'
6.02
```



## 参考

+ [Linux 检查是否安装perl模块及列出所有已安装的perl模块(安装路径、版本号)](https://blog.csdn.net/u010533843/article/details/54971428)
+ [非root用户安装perl模块](https://blog.csdn.net/yuehedou/article/details/78406518)
+ [perl的INC设置](https://blog.csdn.net/frt007/article/details/45270623)