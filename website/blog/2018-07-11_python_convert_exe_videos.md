---
title: python批量转换exe视频
author: woobamboo
authorURL:
authorFBID:
---



如何使用python脚本在win上自动转换exe视频到MP4

<!--truncate-->

转自[python自动化工具之pywinauto(四)——批量转换exe视频](https://blog.csdn.net/shawpan/article/details/78170871)

```
# -*- coding: utf-8 -*-
"""
Created on Wed Oct  4 16:52:13 2017

@author: x
"""
from pywinauto.application import Application

program_path = r"D:\Program Files (x86)\tlxsoft\屏幕录像专家 共享版 V2017\屏录专家.exe"
app = Application().start(program_path)
dlg_spec = app.window(title_re='屏幕录像专家.*',class_name='TMainForm')
#dlg_spec.type_keys('%TP')
dlg_spec.menu_select(r"转换工具->EXE/LXE转成MP4")
dlg_spec = app[r'EXE/EXE 转 MP4']
dlg_spec.print_control_identifiers()    # 打印该窗体下的所有控件结构


for line in open('ToBeConvert.txt'):
    filename = line.strip()     # 去掉读取每一行时最后带着的空格和回车符
    dlg_spec.Button3.click()    # 点击“浏览”按钮
    dlg_open = app.window(title=r'打开')  # 获取“打开”对话框句柄
    dlg_open.Edit.type_keys(filename,with_spaces = True)
#    dlg_open.Edit.set_text(filename)     # 将文件绝对路径写入编辑框中
    dlg_open.Button0.click()             # 点击“打开”按钮
    dlg_open.wait_not('visible')

    dlg_spec.CheckBox0.check()          # 勾选自动扩帧
    dlg_spec.Button0.click()            # 点击“转换”

    app['另存为'].Button0.click()        # 点击“另存为”对话框的“保存”按钮

    app.window(title=r'屏幕录像专家',class_name='TMessageForm').Wait('enabled',timeout=300)       # 等待转换结束
    app.window(title=r'屏幕录像专家',class_name='TMessageForm').Ok.click()        # 关闭转换完成后弹出的对话框
```



> 用法:

1. 根据屏幕录像专家程序的安装位置修改变量`program_path`的值。
2. 在当前目录新建一个 ToBeConvert.txt 文档，每行写上一个带转换的源文件，目标文件目录默认与源文件相同。 