---
id: version-0.1.2-doc2
title: Manjaro的安装、配置到GPU
sidebar_label: Manjaro的安装、配置到GPU
original_id: doc2
---

## 安装并配置Manjaro

### 制作Manjaro启动镜像

1. 从[中科大镜像](https://mirrors.ustc.edu.cn/manjaro-cd/)下载、安装Manjaro (kde) 

2. Mac OS系统下制作U盘启动：

```shell
$ sudo diskutil list
...
/dev/disk8 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *31.0 GB    disk8
   1:             Windows_FAT_32 KINGSTON                31.0 GB    disk8s1

$ diskutil umount /dev/disk8s1 
Volume KINGSTON on disk8s1 unmounted

$ sudo dd if=/path/of/manjaro-kde-18.1.0-stable-x86_64.iso  of=/dev/disk8 bs=1m
```

### 修改系统语言
+ 安装。系统语言先选英文。装好系统再改为中文 (这样`~`下的文件夹就会是英文的了)。
   ```
   首先：修改`/etc/locale.gen`，取消zh.UTF-8的注释
   然后：运行命令`locale-gen`
   然后：修改`locale.conf`，并设置为：
   LANG="zh_CN.UTF-8"
   LANGUAGE="zh_CN:en_GB:en"
   然后，从系统设置里下载语言包
   ```

### 配置国内镜像源
Manjaro 国内源 + ArchlinuxCN 国内源

+ Manjaro 国内源

   ```
   $ sudo pacman-mirrors -i -c China -m rank  # 生成可用中国镜像站列表，勾选 http://mirrors.ustc.edu.cn/manjaro/ ，然后按 OK 键两次。
   $ sudo pacman -Syyu # 升级系统到最新
   ```

+ ArchLinuxCN 中科大源(Arch Linux 中文社区仓库是由 Arch Linux 中文社区驱动的非官方用户仓库)：

   ```
   # /etc/pacman.conf 文件末尾添加：

   [archlinuxcn]
   Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch

   # 然后安装 archlinuxcn-keyring 包以导入 GPG key。
   $ sudo pacman -Syyu
   $ sudo pacman -S archlinuxcn-keyring
   ```


### 开机挂载第二块硬盘

+ 查看硬盘信息:
  
   ```
   sudo fdisk -l # 查看我的是/dev/sdb1
   ls -l /dev/disk/by-uuid/ # 查看对应的UUID
   ```

+ 然后编辑`/etc/fstab`, 增加一行:

   ```
   UUID=a8786e51-eda0-41fe-... /home/bobo/Data ext4 defaults 0 0`
   ```

> 要注意建立个文件夹用于挂载硬盘，并有bobo的所有权，否则任何操作都要root。
>
> 然后**重启**之后就自动挂载了。

### xfce 添加网速监控

```
$ ip addr # 结果显示

1: lo: ...
2: enp7s0: ...
3. wlp6s0: ...
```

> 其中第三个是我的无线网卡，在面板下面`右键`，`面板`--`添加新项目`中添加`网速监控`，则面板下方出现一个`Net`。
>
> 然后在面板的Net上右键`网速监控模块`--`属性`--在网络设备填入`wlp6s0`即可！

## 安装常用软件

+ 安装AUR管理器
   ```shell
   git clone https://aur.archlinux.org/yay.git
   makepkg -si

   # 用法与pacman一样
   yay install some-package
   ```
   
+ 安装其他常用软件

   ```shell
   sudo pacman -S code vim guake zotero pycharm-professional typora skype dbeaver shadowsocks-qt5 telegram-desktop electronic-wechat teamviewer foxitreader chromium flameshot wps-office netease-cloud-music flashplugin

   sudo pacman -S adobe-source-han-serif-cn-fonts adobe-source-han-serif-tw-fonts adobe-source-han-sans-cn-fonts adobe-source-han-sans-tw-fonts # 安装中文简体、繁体字体

   sudo teamviewer --daemon start 
   sudo pacman -S mysql # https://wiki.archlinux.org/index.php/MariaDB_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)#%E5%AE%89%E8%A3%85
   
   #　安装powerline字体
   git clone https://github.com/powerline/fonts.git --depth=1
   cd fonts && ./install.sh
   cd .. && rm -rf fonts
   ```

> `chromium` 启用代理，用以同步各种插件： `chromium --proxy-server="socks5://127.0.0.1:1080`
>
> `flameshot` 最好的截图软件，截图的命令是 `flameshot gui`，设置到系统的自定义快捷键，并开机启动
>
> 安装完`Teamviewer`之后，打开网络有问题。执行`sudo teamviewer --daemon start`，再次打开，过一会就好了[[参考](https://bbs.archlinux.org/viewtopic.php?id=192406)]。

+ SSR指定SwitchOmega规则

  ```
  https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
  ```

+ git 指定代理：

  ```
  git config --global http.proxy 'socks5://127.0.0.1:1080'
  git config --global https.proxy 'socks5://127.0.0.1:1080'
  git config --global http.proxy 'http://10.10.114.193:1087'
  git config --global https.proxy 'https://10.10.114.193:1087'
  git config --global --unset https.proxy
  git config --global --unset http.proxy
  ```

## 安装搜狗输入法

```
$ sudo pacman -S fcitx fcitx-configtool
$ sudo pacman -S fcitx-gtk2 fcitx-gtk3 fcitx-qt4 fcitx-qt5 # 有些文章写可以用fcitx-im替代这些，其实im不会安装qt4，导致输入法异常！
$ sudo pacman -S fcitx-sogoupinyin

$ vim ~/.xprofile
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"

重启之后，配置即可。
```

## 终端、zsh的配置

+ 安装oh-my-zsh

   ```
   git clone https://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh && cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc

   修改默认shell, 或自动启动zsh，这个没必要，我使用的是guake，可设置开启终端时的默认shell。
   # chsh -s /bin/zsh or echo "exec /path/of/zsh --login" >> .bash_profile #
   ```

+ oh-my-zsh 主题及插件下载

   ```
   + 安装zsh主题：

   cd $ZSH_CUSTOM
   cd themes
   git clone https://github.com/sindresorhus/pure.git && cd pure
   ln -s async.zsh async
   ln -s pure.zsh prompt_pure_setup

   # .zshrc
   fpath=("/home/bobo/.oh-my-zsh/custom/themes/pure" $fpath)
   autoload -U promptinit; promptinit
   prompt pure

   # 2. zsh-syntax-highlighting 语法高亮插件
   $ git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
   ```

+ `.zshrc` 的一些配置

   ```
   # .zshrc: 
   # tldr代替man，fd 代替find，在rust那里下载安装。

   alias la="ls -lth"
   alias man="tldr"

   ZSH_THEME="spaceship"

   HIST_STAMPS="yyyy-mm-dd"

   plugins=(
     extract git zsh-syntax-highlighting
   )
   ```

+ dir 颜色的设置： [参考](https://github.com/trapd00r/LS_COLORS)

   ```
   wget https://raw.github.com/trapd00r/LS_COLORS/master/LS_COLORS -O $HOME/.dircolors
   echo 'eval $(dircolors -b $HOME/.dircolors)' >> $HOME/.zshrc
   zsh
   ```

+ 配置vim

   ```
   $ git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
   $ sh ~/.vim_runtime/install_awesome_vimrc.sh
   ```

+ 配置pet

   ```
   pet configure 的输出：
    [General]
      snippetfile = "/home/dongkaige/.config/pet/snippet.toml"
      editor = "vim"
      column = 40
      selectcmd = "fzf"
      auto_sync = "true"

    [Gist]
      file_name = "pet-snippet.toml"
      access_token = "3e442b8baaf53420741c398ba71e27d3eac21a08"
      gist_id = "e6d13028c0569c345884f429c4648d9c"
      public = false
      auto_sync = true
   ```


## 安装配置rust开发环境

   ```shell
# 1. 安装rust 中科大镜像加速rust安装 (参考https://lug.ustc.edu.cn/wiki/mirrors/help/rust-static)
# 添加到 .zshrc
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static  # (用于更新 toolchain)
export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup  #(用于更新rustup)

$ curl https://sh.rustup.rs -sSf | sh  # 官网的安装程序

# 2. 设置中科大Rust crates镜像 (https://lug.ustc.edu.cn/wiki/mirrors/help/rust-crates)
# 在 $HOME/.cargo/config 添加下面内容

[source.crates-io]
registry = "https://github.com/rust-lang/crates.io-index"
replace-with = 'ustc'
[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"

# 3. 安装 nightly 版本
rustup toolchain install --help
rustup toolchain install nightly
rustup toolchain list # 查看已经安装的版本
rustup default stable-x86_64-apple-darwin # 这种方式选择版本

# 4. 添加rust可执行程序文件夹到~/.zshrc
echo "export PATH=$PATH:$HOME/.cargo/bin" >> ~/.zshrc

# 5. 安装好用的rust程序 fd & tldr
cargo install fd-find
cargo install tealdeer
   ```

## 安装NVIDIA组件

### 可选步骤

我的笔记本：Acer VN7 592G；Intel + GTX 960M 双显卡

看了http://www.cnblogs.com/smlile-you-me/p/8830949.html，没成功。但很有帮助。

又看了https://www.youtube.com/watch?v=7KgX-LgDwQw 终于成功了！

+ 首先，确保系统已更新，并重启。

+ 修改GRUB (可选)

   ```
   # 上面的视频教程需要这个步骤，我没有ACPI错误，没进行这一步也没问题。
   $ sudo nano /etc/default/grub
   # 修改：GRUB_CMDLINE_LINUX_DEFAULT="quiet resume=UUID... acpi_osi=! acpi_osi='Windows 2009'"
   # 如果要运行NVIDIA显卡，需要添加后面两个 acpi_osi=! acpi_osi='Windows 2009'；
   # 不然有可能黑屏进不去系统，或者出现ACPI 错误。
   
   $ sudo update-grub # 更新grub， 再次重启系统
   ```

### 安装NVIDIA组件

   ```shell
$ sudo mhwd -a pci nonfree 0300

# 上面的步骤安装了9个软件： 
# Packages (9) egl-wayland-1.1.2-1  eglexternalplatform-1.0+3+g7c8f8e2-1  
primus-20151110-8  bumblebee-3.2.1-22  lib32-nvidia-utils-1:418.43-2  
lib32-primus-20151110-4  linux419-bbswitch-0.8-27  linux419-nvidia-1:418.43-9  
nvidia-utils-1:418.43-2

sudo gpasswd -a xxx bumblebee # 将用户添加到bumblebee组，否则运行NVIDIA的setting组件提示错误
   ```

+ **重启**检查是否安装好了

```shell
$ mhwd -li # 可以发现

--------------------------------------------------------------------------------
                     NAME               VERSION          FREEDRIVER           TYPE
--------------------------------------------------------------------------------
            network-r8168            2016.04.20                true            PCI
              video-linux            2018.05.04                true            PCI
video-hybrid-intel-nvidia-bumblebe   2018.08.09               false            PCI
   
$ sudo optirun glxgears # Ran后跑起来一个轮子测试一下，会出现一个小正方形动图。

$ watch cat /proc/acpi/bbswitch # 每2s刷新，查看GPU是`ON`或`OFF`

$ nvidia-smi  # 然后再看一下，有哪些程序运行在NVIDIA上
```



### 配置steam游戏自动调用NVIDIA显卡

> 到上面，就已经安装好了NVIDIA，当需要的时候是能够运行NVIDIA的。
>
> 那么怎么启动呢？其实，Manjaro网站上的[教程](https://wiki.manjaro.org/index.php?title=Configure_NVIDIA_(non-free)_settings_and_load_them_on_Startup)很详细.
>
> 当我们需要用显卡运行一个程序，运行命令 `primusrun program1` (program是steam主程序，我的会有点问题!!)
>

+ 具体步骤
当我们想要用显卡运行steam里面的游戏，进去steam的库，右键游戏，点`属性`，然后点`设置启动选项`，里面输入`primusrun %command%`。
然后再运行游戏，就能调用显卡就行了！！


### pytorch 调用显卡

   ```shell
# 首先安装组件
sudo pacman -S python-pytorch-cuda
#　这将安装以下包，之所以不用pytorch官网的方法，是太慢啦。
Packages (8) cblas-3.8.0-2  cuda-10.1.105-6  cudnn-7.5.0.56-1  nccl-2.4.2-4  
opencl-nvidia-1:418.43-1 opencv-4.0.1-6 python-numpy-1.16.2-1 python-pytorch-cuda-1.0.1-5

# 测试pytorch能否调用NVIDIA显卡
$ python
>>>import torch
>>>print(torch.cuda.is_available())
False

# 因为NVIDIA显卡没有开！下面命令打开和关闭NVIDIA程序，当是ON的时候，上面就是TRUE了！！

sudo tee /proc/acpi/bbswitch <<< ON
sudo tee /proc/acpi/bbswitch <<< OFF
   ```

