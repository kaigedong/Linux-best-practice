---
title: shell变量及进程及set,env,export
author: woobamboo
authorURL:
authorFBID:
---


shell中，有三种变量：`系统变量`，`环境变量`，`用户变量`。

<!--truncate-->

## shell 变量

### 1.1系统变量

Shell常用的系统变量并不多，但却十分有用，特别是在做一些参数检测的时候。下面是`Shell常用的系统变量`

| [表示方法] | [描述] |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `$n`                                                         | `$1 表示第一个参数，$2 表示第二个参数 ...`                  |
| `$#`                                                         | `命令行参数的个数`                                           |
| `$0`                                                         | `当前程序的名称`                                             |
| `$?`                                                         | `前一个命令或函数的返回码`                                   |
| `$*`                                                         | `以"参数1 参数2 ... " 形式保存所有参数`                      |
| `$@`                                                         | `以"参数1" "参数2" ... 形式保存所有参数`                     |
| `$$`                                                         | `本程序的(进程ID号)PID`                                      |
| `$!`                                                         | `上一个命令的PID`                                            |

### 1.2 shell进程

> 在利用上面的系统变量进行编程时，`$$`,`$!`并没有按预想的那样，输出上个`命令`的PID，这里需要学习一下shell中的进程。

1：什么是shell子进程

2：shell什么情况下会产生子进程

3：子进程的特点与注意事项

4:`$`变量`$$`在脚本里的意义，及如何得到子进程里的进程号



> 1. 什么事shell子进程

子进程，是从父子进程的概念出发的，unix操作系统的进程从init进程开始（init进程为1,而进程号0为系统原始进程，以下讨论的进程原则上不包括进程0)均有其对应的子进程，就算是由于父进程先行结束导致的孤儿进程，也会被init领养，使其父进程ID为1。

也因为所有的进程均有父进程，事实上，所有进程的创建，都可视为子进程创建过程。在apue一书里提及unix操作系统进程的创建，大抵上的模式都是进行fork+exec类系统调用。

理解子进程的创建执行，需要至少细分到二个步骤，包括

+ 通过fork创建子进程环境，
+ 通过exec加载并执行进程代码。
+ 其间诸如继承的环境变量等细节，可以查看apue第八章相关章节。

而shell子进程（以下均称subshell），顾名思义，就是由“当前shell进程”创建的一个子进程

> 2. shell什么情况下会产生子进程

以下几个创建子进程的情况。（以下英文摘自info bash)

1. `&`提交后台作业

If a command is terminated by the control operator `&', the shell executes the command asynchronously in a subshell.

2. 管道

Each command in a pipeline is executed in its own subshell

3:括号命令列表 `()`操作符

Placing a list of commands between parentheses causes a subshell environment to be created

4：执行外部脚本、程序：

When Bash finds such a file while searching the `$PATH` for a command, it spawns a subshell to execute it.  In other words, executing filename ARGUMENTS is equivalent to executing bash filename ARGUMENTS

 

说明：大致上子进程的创建包括以上四种情况了。需要说明的是只要是符合上边四种情况之一，便会创建(fork)子进程，不因是否是函数，命令，或程序，也不会因为是内置函数(buitin)或是外部程序。

此外，上边提到子进程创建与执行的二个步骤，shell子进程的创建在步骤之一并无多大差别，一般还是父进程调用fork产生进程环境，估在第二步exec的时候，是存在差别的。

shell 作为解释语言程序，提供给第二步exec加载和执行的程序体并不是脚本本身，而是由第一行`#!`指定的，默认为shell程序,当然也可以是awk,sed等程序。



只不过子进程的执行会根据情况而有所差别，对于***内置函数***，exec程序体为shell程序，并在会在子shell直接调用内置函数， 

而***外部函数或程序***，在创建了子进程环境后，大致会有二种执行情况：

+ 直接exec外部程序，

比如下边例子中直接执行的sleep,pstree命令等

+ subshell exec程序体为shell程序，在此基础上会进一步创建一个子进程以执行函数。

比如下边例子中通过函数提交后台程序中的shell命令等

 ```
例：内置函数(直接在subshell里执行，不管是否通过函数)
$ mkfifo a

$ type echo
echo is a shell builtin

$ b(){echo a>a;}
$ b&
[2] 129180
[2]  + 129180 done       b

$ echo a>a &
[2] 129649
[2]  + 129649 done       echo a > a


$ pstree -pa $$
zsh,130152
  └─pstree,130233 -pa 130152

例：定义函数并提交后台运行（函数调用中的sleep在subshell之下又创建一个子进程,而pstree,sleep命令的直接执行，则是直接在子进程上进行）

$ a(){ sleep 30;  } ;

$ sleep 40 &
[1] 131052

$ a &
[2] 131059

$ pstree -pa $$
zsh,130152
  ├─pstree,2133 -pa 130152
  ├─sleep,131052 40
  └─zsh,131059
      └─sleep,131060 30

要注意，shell脚本的执行模式，在第四点的二种模式下，shell是会创建子进程的：

filename ARGUMENTS
bash filename ARGUMENTS

但shell同时提供二种不创建子程序的进程创建方式

1：source命令，使用方法

source   filename ARGUMENTS
或
. filename ARGUMENTS

此种方法，直接在当前shell进程中执行filename脚本，filename结束后继续返回当前shell进程


2：exec命令，使用方法

exec filename ARGUMENTS

此种方法直接在当前shell进程中执行filname脚本，filename结束后退出当前shell进程
 ```



> 3. 子进程特点与注意事项

这方面不具体展开，只提一点写脚本容易出现的错误。

做为子进程，其进程环境与父进程的环境是独立的， 所以在变量传递过程中，需要注意子进程内部不能更改到父进程的变量。比如如下通过管道求和并赋给外部变量sum例子，结果sum值并不会因此改变：

 ```
$ sum=0
$ echo '1 2 3 4' | sed 's/ /\n/g' | while read line;do sum+=$line; done
$ echo $sum
01234

很尴尬我的会变。
 ```



> 4. 变量`$$`在脚本中的意义

变量`$$`代表的是当前shell进程的进和id，这里要特别留意***`当前shell`***，

看看info bash里的说明

`$`

Expands to the process ID of the shell.  In a `()` subshell, it expands to the process ID of the invoking shell, not the subshell.

所以在实际环境中，`$$`并不一定“当前进程”的进程号，而是`当前shell进程`的进程号。

从文档中，需要留意的便是 invoking shell (info) 或  current  shell（man) 与 当前subshell进程的关系了

这就引出了几个问题

+ 到底怎么样算是current  shell

+ 子进程里的$$对应的是哪个current  shell
+ 如何猎取子进程的`$$`?

做为调试和测试，下边的例子引用几个变量,



 

















### 2.环境变量

***shell 环境变量是所有shell 程序都会接受的参数。shell程序运行时，都会接收一组变量，这组变量就是环境变量。常用的环境变量：***

***在shell程序中，使用export导出的环境变量仅仅在本shell程序中管用（包括调用的其他shell程序文件）；当本shell程序结束，该环境变量就不起作用了。***



### 3.用户变量

不管系统变量有多少，对于需求来说，总是不够的。用户变量是最常用到的变量，使用也十分简单。

```
# 定义：
name="Winter Is Coming!"

# 使用：
echo $name

# 切割变量的方法：
a = ${name%Coming}
b = ${a#Winter}
```



## set,env,export

###  1.set命令

作用：

+ 显示系统中已存在的shell变量
+ 设置shell变量的新变量值

> set 命令不能定义新的shell变量。如果要定义新的变量，可以用`declare`命令及`变量名=值`的格式进行定义。

### 实例

```
# 使用declare命令定义一个新的环境变量"mylove"，并且将其值设置为"Visual C++"，输入如下命令：

declare mylove='Visual C++'   #定义新环境变量

# 再使用set命令将新定义的变量输出为环境变量，输入如下命令：
set -a mylove                 #设置为环境变量

# 执行该命令后，将会新添加对应的环境变量。用户可以使用env命令和grep命令分别显示和搜索环境变量"mylove"，输入命令如下：

env | grep mylove             #显示环境变量值

此时，该命令执行后，将输出查询到的环境变量值。
```

### export 

pass

### env

**env命令**用于显示系统中已存在的`环境变量`，以及在定义的环境中执行指令。该命令只使用"-"作为参数选项时，隐藏了选项"-i"的功能。若没有设置任何选项和参数时，则直接显示当前的环境变量。

 




## 附录：

### 常用的环境变量

| 名称            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| PATH            | 命令搜索路径,以冒号为分隔符.注意与DOS下不同的是, 当前目录不在系统路径里 |
| HOME            | 用户home目录的路径名,是cd命令的默认参数                      |
| COLUMNS         | 定义了命令编辑模式下可使用命令行的长度                       |
| EDITOR          | 默认的行编辑器                                               |
| VISUAL          | 默认的可视编辑器                                             |
| FCEDIT          | 命令fc使用的编辑器                                           |
| HISTFILE        | 命令历史文件                                                 |
| HISTSIZE        | 命令历史文件中最多可包含的命令条数                           |
| HISTFILESIZE    | 命令历史文件中包含的最大行数                                 |
| IFS             | 定义SHELL使用的分隔符                                        |
| LOGNAME         | 用户登录名                                                   |
| MAIL            | 指向一个需要SHELL监视其修改时间的文件.当该文件修改后, SHELL将发消息You hava mail给用户 |
| MAILCHECK       | SHELL检查MAIL文件的周期,单位是秒                             |
| MAILPATH        | 功能与MAIL类似.但可以用一组文件,以冒号分隔,每个文件后可跟一个问号和一条发向用户的消息 |
| SHELL           | SHELL的路径名                                                |
| TERM            | 终端类型                                                     |
| TMOUT           | SHELL自动退出的时间,单位为秒,若设为0则禁止SHELL自动退出      |
| PROMPT_COMMAND  | 指定在主命令提示符前应执行的命令                             |
| PS1             | 主命令提示符                                                 |
| PS2             | 二级命令提示符,命令执行过程中要求输入数据时用                |
| PS3             | select的命令提示符                                           |
| PS4             | 调试命令提示符                                               |
| MANPATH         | 寻找手册页的路径,以冒号分隔                                  |
| LD_LIBRARY_PATH | 寻找库的路径,以冒号分隔                                      |



## set 选项：

```
-a：标示已修改的变量，以供输出至环境变量。
-b：使被中止的后台程序立刻回报执行状态。
-C：转向所产生的文件无法覆盖已存在的文件。
-d：Shell预设会用杂凑表记忆使用过的指令，以加速指令的执行。使用-d参数可取消。
-e：若指令传回值不等于0，则立即退出shell。
-f：取消使用通配符。
-h：自动记录函数的所在位置。
-H Shell：可利用"!"加<指令编号>的方式来执行history中记录的指令。
-k：指令所给的参数都会被视为此指令的环境变量。
-l：记录for循环的变量名称。
-m：使用监视模式。
-n：只读取指令，而不实际执行。
-p：启动优先顺序模式。
-P：启动-P参数后，执行指令时，会以实际的文件或目录来取代符号连接。
-t：执行完随后的指令，即退出shell。
-u：当执行时使用到未定义过的变量，则显示错误信息。
-v：显示shell所读取的输入值。
-x：执行指令后，会先显示该指令及所下的参数。
```



## 参考

+ [unix shell的系统变量，环境变量，用户变量(引)](http://blog.sina.com.cn/s/blog_4b9eab320100rirb.html)
+ [set命令](http://man.linuxde.net/set)
+ [env命令](http://man.linuxde.net/env)
+ [实例解析shell子进程（subshell )](https://blog.csdn.net/sosodream/article/details/5683515)


















