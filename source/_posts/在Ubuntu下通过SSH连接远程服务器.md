---
title: 在Ubuntu下通过SSH连接远程服务器
date: 2017-10-07 15:20:37
tags: [Linux,服务器]
---
登录远程服务器是搭建、维护网站的常用操作。在Windows环境下我们会选择XSHELL等工具进行登录，而在Linux环境下可以通过终端快捷登录。

### 环境准备  
装有Ubuntu操作系统的本地机和远程腾讯云服务器各一台
### 步骤一:  
打开腾讯云官网，登录云服务器，在服务器上安装SSH服务器端    
`$ sudo apt-get install aptitude`  
`$ sudo aptitude install openssh-server` 
### 步骤二:  
启动ssh-server  
`$ /etc/init.d/ssh restart`  
### 步骤三：  
确认ssh-server已经正常工作  
`$ netstat -tlp`  
tcp6 0 0 \*:ssh \*:\* LISTEN -  
看到上面一行说明ssh-server已经在运行了  
### 步骤四：  
在本地机终端通过ssh登录服务器  
`$ ssh -l 远程服务器用户名 服务器ip地址`  
接下来会提示输入密码，然后就能成功登录到服务器上了

### 参考  
<http://blog.csdn.net/wh_19910525/article/details/7585257>
