---
title: 使用telnet发送HTTP请求报文
date: 2017-11-19 23:30:25
tags: 计算机网络
---
我们可以使用telnet给特定的Web服务器发送HTTP请求报文，得到服务器的HTTP相应报文。

比如，打开终端输入下面命令：  

telnet www.baidu.com 80  Web服务器ip或域名、端口
GET https://www.baidu.com/  HTTP/1.1  
//请求行：方法字段、URL字段、HTTP版本字段  
Host: www.baidu.com //首部行

在输入最后一个首部行之后连续按两次回车，这就打开一个到主机 www.baidu.com 的80端口的TCP连接，并发送一个HTTP请求报文。你将会看到一个携带包括百度主页的HTML基本文件的相应报文(HTML文件，即对象被封装在相应报文的实体体中)。如果只是想看一下HTTP协议的报文行，而不是获取对象本身的话，可以用HEAD代替GET。
