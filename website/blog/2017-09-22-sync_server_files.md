---
title: 同步服务器文件夹
author: woobamboo
authorURL:
authorFBID:
---


一个简单的脚本，将本地文件夹和服务器文件夹内容互相同步。

<!--truncate-->



```
#!/usr/bin/env python3
# -*- coding:utf-8 -*- 
import os,paramiko,time
 

'''
需要安装paramiko! Python3下已测试通过。
部分代码参考：http://www.cnblogs.com/kex1n/p/6021445.html
已知问题：不能文件夹内的文件夹。
'''

###############################
#####      参数设置      ######
###############################
synPath = '/home/***/Syn' # 远程文件夹
localSynPath = '/home/***/Downloads/Syn' # 本地文件夹
sleepTime = 300 # 设置同步时间间隔(秒)
ip = '10.10.***.***' #设置服务器IP地址
usrName = ''
passwd = '' # 服务器账号密码
###############################

ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
ssh.connect(ip,username=usrName,password=passwd,timeout=300)


sftp = paramiko.SFTPClient.from_transport(ssh.get_transport())
sftp = ssh.open_sftp()

while True:
    fileList = set(sftp.listdir(synPath)) # 服务器同步文件夹中的内容
    localFileList = set(os.listdir(localSynPath))

    for file in fileList:  # 将远程文件下载到本地
        if file not in localFileList:
            remotName = synPath +'/'+ file
            localName = localSynPath +'/'+ file
            try:
                sftp.get(remotName,localName)
            except:
                print('err:下载到本地出错！')
                next


    for file in localFileList: # 将本地文件上传到服务器
        if file not in fileList:
            remotName = synPath +'/'+ file
            localName = localSynPath +'/'+ file
            try:
                sftp.put(localName,remotName)
            except:
                print('err:上传到服务器出错！')
                next
    
    time.sleep(sleepTime)

```

### 使用方法：
+ 在本地运行该脚本。
> 建议加入环境变量：alias syn='nohup python3 scpFile.py 1>.log 2>&1 &'

