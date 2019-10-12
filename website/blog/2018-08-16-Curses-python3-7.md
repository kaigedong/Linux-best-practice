---
title: Curses_python3.7
author: woobamboo
authorURL:
authorFBID:
---

Python curses模块：控制终端

<!--truncate-->

参考：https://docs.python.org/3/howto/curses.html

版本：Release 2.04

Python版本：3.7.0

## 什么是curses?

curses库提供许多基本功能：更改窗口内容：添加为本，擦除文本，改变外观等。但curses不提供用户界面如按钮、复选框或对话框，如果你要使用这些，请考虑`Urwid`.

curses最初是为BSD Unix编写的，AT&T的第五个版本的Unix添加了很多增强和新功能。BSD curses已经被ncurses所替代。

Win版本的curses不包含curses，但可使用`UniCurses`这个移植版本。

### Python curses

curses 的python模块是curses提供的C函数的简单包装。最大的区别是Python接口将不同的C函数（如`addstr()`,` mvaddstr()`, `mvwaddstr()`) 合并到了单个的`addstr()`方法中了。

## 开始和结束curses

+ 开始之前，curses必须先调用`initscr`进行初始化，这将确定终端类型、向终端发送需要的设置代码、创建内部数据结构。如果成功，将返回一个windows 类代表整个屏幕，根据C变量这通常命名为`stdscr`。

```
import curses
stdscr = curses.initscr()
```

+ 通常情况下，curses会关闭对屏幕对按键的自动显示(自动回显)，以便读取它们并在特定时候显示。这就需要调用`noecho()`函数。

```
curses.noecho()
```

+ 对按键立即做出回应，而不需要按下`Enter`键，这称为`cbreak模式`，与通常的缓冲输入模式(buffered input mode)相反。

```
curses.cbreak()
```

+ 终端通常返回特殊键，如光标键或导航键(如Page Up 和 Home)，作为多字节转移序列。`curses`能自动为你处理，返回一个特殊的值比如`curses.KEY_LEFT`. 为了让curses 使用这个功能，你需要开启键盘模式(keypad mode)：

```
stdscr.keypad(True)
```

+ 关闭curses需要调用：

```
curses.nocbreak() # 关闭字符终端功能
stdscr.keypad(False) 
curses.echo() # 打开输入回显功能
```

+ 来恢复前面的设置，并调用`endwin()`恢复终端之前的设置：

```
curses.endwin()
```

+ **Python中，可能会因为bug使得终端无法恢复到初始状态，而对键盘无响应。可以通过调用`curses.wrapper()`来避免**：

```
from curses import wrapper

def main(stdscr):
    # Clear screen
    stdscr.clear()

    # This raises ZeroDivisionError when i == 10.
    for i in range(0, 11):
        v = i-10
        stdscr.addstr(i, 0, '10 divided by {} is {}'.format(v, 10/v))

    stdscr.refresh()
    stdscr.getkey()

wrapper(main)
```

`wrapper()`函数接受一个可调用对象并进行上述初始化，如果存在颜色支持，也会初始化颜色。当可调用对象运行完毕，`wrapper()`会将终端恢复到原始状态,如果出错，会报出错误(内部通过try...except实现)，最后恢复终端。



## `windows` 和 `pads`

### window

> `windwos`是curses最基本的抽象，一个`windw`类表示屏幕上的一个长方形区域，支持显示文本、释放、允许用户输入字符等。
>

+ `initscr()`方法返回的`stdscr`对象是表示整个屏幕的windows 类；你可能想要划分成更小的区域来分别制：`newwin()`方法创建给定大小的`window` 类：

```
begin_x = 20; begin_y = 7
height = 5; width = 40
win = curses.newwin(height, width, begin_y, begin_x)
```

> + 需要注意curses的***坐标***，坐标总是按***`y，x`***的顺序传递；左上角坐标是`(0,0)`。 它第一次就是这么写的，所以没法改了☁️
>
> + 可通过`curses.LINES` 和 `curses.COLS`变量来获取`y & x` 的大小；合法的坐标从`(0，0)`到`(curses.LINES-1,curses.COLS-1)`
> + 当调用方法显示或删除时，终端不会立即显示，需要调用**`refresh()`**方法来更新屏幕。如`stdscr.refresh()`或其他windows的`refersh()`方法。

### `pad`

> `pad`是`window`的特例，可以比实际的显示屏幕大，并且每次只显示`pad`的一部分。创建`pad`需要的`height`和`width`，更新`pad`需要给定`pad`子区域的坐标。

```
pad = curses.newpad(100, 100) 
# These loops fill the pad with letters; addch() is 
# explained in the next section 
for y in range(0, 99): 
	for x in range(0, 99): 
		pad.addch(y,x, ord('a') + (x*x+y*y) % 26) 
# Displays a section of the pad in the middle of the screen. 
# (0,0) : coordinate of upper-left corner of pad area to display. 
# (5,5) : coordinate of upper-left corner of window area to be filled with pad content. 
# (20, 75) : coordinate of lower-right corner of window area to be : filled with pad content. 
pad.refresh( 0,0, 5,5, 20,75)
```

> `refersh()`调用显示pad从`(5,5)` 到 `(20,75)`的区域，除了这种区别，pad 和 windows 完全相同，并支持相同的方法。
>
> 如果屏幕上有多个windows和pad，则可以更有效地更新屏幕以防止闪烁。`refersh()` 实际上做了两件事情：1.调用每个窗口的`noutrefresh()`来更新表示屏幕所需的数据结构；2.调用`doupdate()`来改变物理屏幕以匹配数据结构中记录的信息



## 显示文本

`stdsccr`窗口的`addstr()`方法接受多个参数，

|   格式   |  描述    |
| ---- | ---- |
|  str or ch    | 在当前位置显示`str`或`ch` |
| str or ch, attr     | 使用属性`attr`在当前位置显示`str`或`ch` |
| y,x,str or ch     | 移动到窗口`y,x`位置，显示`str`或`ch` |
| y, x , str or ch, attr | 移动到窗口`y,x`位置，使用属性`attr`显示`str`或`ch |

> 属性允许以突出模式显示，如`粗体`、`下划线`、`反向代码`、`彩色`。
>
> `addstr()`接受`string`或`bytestring`作为输入，`bytestirng`的内容会被原样发送到终端。`string`会使用windows 的 encoding 属性，将`string`编码城`bytes`; 这个默认值是`locale.getpreferredencoding()`的返回值。
>
> `addch()`方法接受一个`字符`，或`长度为1的string`，`长度为1的bytestring`，或一个整数。
>
> 如果不需要闪烁光标，可调用`curs_set(False)`来使其不可见。

### 属性和颜色

> 字符可以以不同的方式显示。在基于文本的命令行里，状态栏通常以`reverse video`的方式显示，或者一个文本查看器需要高亮一些单词。curses通过允许指定屏幕上的每个`cell`的属性。
>
> 属性是整数，每个位表示不同的属性。

| Attribute   | Description             |
| ----------- | ----------------------- |
| A_BLINK     | 闪烁文字                |
| A_BOLD      | 超亮或粗体文字          |
| A_DIM       | 半明亮文字              |
| A_REVERSE   | `reverse-video text` ?? |
| A_STANDOUT  | 可用的最佳高亮模式      |
| A_UNDERLINE | 带下划线的文字          |

在屏幕上展示`reverse-video status line`

```
stdscr.addstr(0, 0, "Current mode: Typing mode", curses.A_REVERSE) 
stdscr.refresh()
```

> Curses库 还支持一些终端提供的颜色。最常见的是`Linux console`, 其次是`color xterms`

**为了使用颜色，需要在调用了`initscr()`之后调用`start_colr()`方法来初始化默认颜色设置 (`curses.wrapper()`方法自动进行这些)。**完成之后，如果终端能显示颜色，`has_colors()`方法返回`TRUE`。

curses 维护了一些颜色对，包含前景色和背景色。可使用`color_pair()`方法获得颜色对的属性值，这可以与其他属性`按位或`如`A_REVERSE`。

例如，使用color pair 1 显示一样文本：

```
stdscr.addstr("Pretty text", curses.color_pair(1)) 
stdscr.refresh()
```

> 一个颜色对包含前景色和背景色，`init_pair(n,f,b)`方法可以改变颜色对`n`的前景色`f`和背景色`b`。颜色对0被设定为`white on black`的颜色对，且无法更改。

颜色被编号，`start_color()`方法在激活颜色模式时，初始化了8种基本颜色：`0:black`; `1:red`; `2:green`; `3:yellow`; `4:blue`; `5:magenta`; `6:cyan`; `7:white`; 

要在***白色背景上将颜色1改为红色***，可以：

```
curses.init_pair(1, curses.COLOR_RED, curses.COLOR_WHITE)
```

当改变了一个颜色对，所有使用这个颜色对的文本将会被改为新颜色。也可以展示新文本的颜色：

```
stdscr.addstr(0,0, "RED ALERT!", curses.color_pair(1))
```

> 甚至可以通过RGB值改变颜色。如果`can_change_colr()`为`True`，则可以。



## 用户输入

有两个方法获取windows中的输入：

`getch()`: 刷新屏幕然后等待用户输入一个按键，如果`echo()`方法在之前被调用过，则会显示这个按键。

`getkey()`： 与上面相同，只不过是字符串。

可以使用`nodelay()`方法表示不等待用户。在`nodelay(True)`之后，window中的`getch()`和`getkey()`变为非阻塞的。为了表示输入没有就绪，`getch()`会返回`curses.ERR`(值是-1)，而`getkey()`引发异常。`halfdelay()`方法，可以为每个`getch()`设定一个`timer`,如果特定延时中(以十分之一秒为单位)没有获得输入，将引发异常。

`getch()`方法返回一个整数，如果在`0`和`255`之间，代表按下的是`ASCII`，大于255的值是特殊键，如`Page Up`, `Home`,或`光标键`。可以将返回的值与`curses.KEY_PPAGE`,`curses.KEY_HOME`, `curses.KEY_LEFT`这些常量进行比较。主循环类似于：

```
while True:
    c = stdscr.getch()
    if c == ord('p'):
        PrintDocument()
    elif c == ord('q'):
        break  # Exit the while loop
    elif c == curses.KEY_HOME:
        x = y = 0
```

`curses.ascii`模块提供`ASCII`类成员函数，接受整数或者1个字符的string参数；也提供转换函数，以整数或1个字符的string为参数，返回相同的类型。如`curses.ascii.ctrl()`返回与其参数对应的控制字符。

还有一种方法可以检索整个字符串:`getstr()`,但不经常用，因为其功能有限，唯一可用的编辑键是退格键和Enter键。

```
curses.echo()            # Enable echoing of characters

# Get a 15-character string, with the cursor on the top line
s = stdscr.getstr(0,0, 15)
```

`curses.textpad`模块提供了一个类似于`Emacs`的键绑定的文本框。`Textbox`类的不同方法支持使用输入验证进行编辑，并使用或不适用尾随空格获取编辑结果；例如：

```
import curses
from curses.textpad import Textbox, rectangle

def main(stdscr):
    stdscr.addstr(0, 0, "Enter IM message: (hit Ctrl-G to send)")

    editwin = curses.newwin(5,30, 2,1)
    rectangle(stdscr, 1,0, 1+5+1, 1+30+1)
    stdscr.refresh()

    box = Textbox(editwin)

    # Let the user edit until Ctrl-G is struck.
    box.edit()

    # Get resulting contents
    message = box.gather()
```
## 高级特性

`curses` 还支持高级特性，如读取`屏幕的内容`，在`xterm实例中捕获鼠标事件`等。



## 例子

```
import curses

stdscr = curses.initscr() # 必须先初始化；返回一个window 对象。

def display_info(str, x, y, colorpair=2):
    '''使用指定的colorpair显示文字'''
    global stdscr
    stdscr.addstr(y,x,str,curses.color_pair(colorpair))
    stdscr.refresh()

def get_ch_and_continue():
    '''演示press any key to continue'''
    global stdscr
    # 设置nodeley，为False是会变成阻塞式等待
    stdscr.nodelay(False)
    # 输入一个字符
    ch=stdscr.getch()
    # 重置nodelay，使得控制台可以以非阻塞方式接受控制台输入，超时1秒
    stdscr.nodelay(1)
    return True

def set_win():
    '''控制台设置'''
    global stdscr
    # assert curses.has_colors() == False
    # 使用颜色先调用这个方法：
    curses.start_color()
    assert curses.has_colors() == True

    # 文字和背景设置，设置了连个color pair，分别是1和2；
    curses.init_pair(1, curses.COLOR_GREEN, curses.COLOR_BLACK)
    curses.init_pair(2, curses.COLOR_RED, curses.COLOR_BLACK)
    # 关闭屏幕回显
    curses.noecho()

    # 输入时不需要回车确认
    curses.cbreak()

    # 设置nodelay, 使得控制台可以以非阻塞方式接受控制台输入，超时1秒


def unset_win():
    '''控制台重置'''
    global stdscr
    # 恢复控制台默认设置（若不恢复，会导致即使程序退出了，控制台仍然是没有回显的。）
    curses.nocbreak()
    stdscr.keypad(0)
    curses.echo()
    # 结束窗口
    curses.endwin()

if __name__=='__main__':
    try:
        set_win()
        display_info('Hola, curses!', 0, 5)
        display_info('Press any key to continue...', 0, 10)
        get_ch_and_continue()
    except (ValueError,TypeError) as e:
        print("Error, exit...", e)
    finally:
        unset_win()
```

+ 例子[来自](https://www.cnblogs.com/starof/p/4703820.html)