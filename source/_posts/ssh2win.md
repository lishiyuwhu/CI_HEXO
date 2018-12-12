---
title: ssh连接windows
date: 2018/12/10 13:29
tags: 
---

# 需求

连接别人的win10机器, 跑模型.

# 1. WSL的ssh

本来挺好, 但是WSL不认GPU, pass


<!-- more -->

# 2. Powershell Server

Free版是只支持单人连接,凑合用吧

1. 下载安装powershell server软件
2. 设置好port, 默认22, client的Public key, 以及Other里面的Text encoding为hz-gb-2312
3. 防火墙打开对应端口  powershell管理员, 运行```New-NetFirewallRule -Protocol TCP -LocalPort 22 -Direction Inbound -Action Allow -DisplayName SSH```

然后就client ssh过去就可以, 但是有一些东西在powershell下面没法用, vim还要挂载

# 3. 走 Jupyter lab

我之前实习的时候, 开发机要走堡垒机所以sftp不能用, 就一直在用Jupyter lab. 局域网内直接开就好了, 其他的还要点操作

1. 配置好powershell server, 确保能ssh过去
2. `jupyter notebook password`  设置jupyter lab密码. 本地浏览器过remote session要密码
3. server上把jupyter lab开起来`jupyter lab --port=9000 --no-browser`
4. ssh 过去, 把远程端口映射为localhost的端口 `ssh -N -f -L 8888:localhost:9000 1.1.1.1 -p 22`
5. 网页打开 localhost.com:8888


http://www.blopig.com/blog/2018/03/running-jupyter-notebook-on-a-remote-server-via-ssh/


# 4. Pycharm的remote ssh解释器

之前搞过, 但没有terminal真挺麻烦, 还要单独开个

==================================

# 5. win10自带的openssh

https://null-byte.wonderhowto.com/how-to/create-native-ssh-server-your-windows-10-system-0181871/

按照这个配置, "Step 4 Install the OpenSSHUtils Helper Module"跳过, 遇见服务开不了的问题就手工去服务里面打开
注意 sshd和ssh-keygen这两个服务名字变了, 现在是以OpenSSH开头的两个服务.

配置完, 走账号密码登录就行, 然后进powershell.

要开jupyter后, 继续端口映射完了就ok

`ssh -N -f -L 8888:localhost:9000 username@server_ip -p 22`


