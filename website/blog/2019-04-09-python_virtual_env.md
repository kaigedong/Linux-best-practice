---
title: virtualenv 和 pyenv的使用
author: woobamboo
authorURL:
authorFBID:
---


virtualenv 和 pyenv的使用

<!--truncate-->

## 第一部分：virtualenv的使用

```
# 首先用pip3安装virtualenv
pip3 install virtualenv

# 创建独立的python运行环境：
virtualenv --no-site-packages venv

# 其他参数：
# --always-copy 默认行为是符号链接需要的文件， 可以强制复制，而非拷贝。
# -p PYTHON_EXE 指定python解析器


# 激活
source venv/bin/activate

# 退出
deactivate
```



```
# python环境的备份
pip freeze > requirements.txt
pip install -r requirements.txt
```



## 第二部分：pyenv的使用

### pyenv的工作原理

当我们使用命令比如`pip` 或者`python`时，Linux通过`PATH`环境变量来查找这个可执行程序。比如`PATH` 是`/usr/local/bin:/usr/bin:/bin`，则Linux从左到右查找是否存在`python`命令，在第一个文件夹`/usr/local/bin`下找到了`python`，就不再继续查找了。

**pyenv的工作原理**就是基于`PATH`变量。

`pyenv`在`PATH`前面插入一个目录，变成了`$(pyenv root)/shims:/usr/local/bin:/usr/bin:/bin`。

切换的秘诀就是`shims`文件夹下的`python文件内容`的改变 (`pip`等也变)。

```
# 首先安装一个python版本，参考下面。

$ cd ~/.pyenv/shims && pyenv versions
  system
* 3.7.3 (set by /Users/kaigedong/.pyenv/version)
$ ./python --version
Python 3.7.3

$ pyenv global system && pyenv versions
* system (set by /Users/kaigedong/.pyenv/version)
  3.7.3

$ ./python --version
Python 2.7.10

$ echo $PATH
/Users/kaigedong/.pyenv/shims:/Users/kaigedong/.pyenv/bin:........
```

### 安装pyenv

```
git clone https://github.com/pyenv/pyenv.git ~/.pyenv
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >>~/.zshrc
echo -e 'if command -v pyenv 1>/dev/null 2>&1; then\n  eval "$(pyenv init -)"\nfi' >> ~/.zshrc
exec "$SHELL"
```

### 安装一个python 版本

```
pyenv install -l # 列出所有能够安装的python版本
pyenv install 3.7.3 (Mac 下报错：zipimport.ZipImportError: can't decompress data; zlib not available，但是brew install zlib：Warning: zlib 1.2.11 is already installed and up-to-dat； 这是因为zlib的库没有正确链接。) 

ln -s /usr/local/Cellar/zlib/1.2.11/include/* /usr/local/include
ln -s /usr/local/Cellar/zlib/1.2.11/lib/* /usr/local/lib
pyenv install 3.7.3
```

### 版本的切换: 全局

```
$ pyenv versions
* system (set by /Users/kaigedong/.pyenv/version)
  3.7.3

$ pyenv global 3.7.3

$ pyenv versions
  system
* 3.7.3 (set by /Users/kaigedong/.pyenv/version)
```

### 版本的切换：local

```
$ cd ~ && mkdir test_pyenv
$ pyenv local system && pyenv versions
* system (set by /Users/kaigedong/test_pyenv/.python-version)
  3.7.3
$ ls -a
.python-version # 该目录下有这个文件，内容是system


$ cd .. && pyenv versions
  system
* 3.7.3 (set by /Users/kaigedong/.pyenv/version)
```



