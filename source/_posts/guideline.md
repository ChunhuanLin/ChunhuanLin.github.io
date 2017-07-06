---
layout: post
title: 服务器使用指南
categories: Server
date: 2017-04-10
tags: server
---
<div class="message">
考虑到服务器的安全问题，下文中，服务器的ip地址用ip_address表示
</div>

为了方便大家使用，下面总结了一些服务器提供的服务，供大家作为参考。

## ssh服务
ssh服务为用户提供远程登录的服务。我们可以使用ssh登录我们的服务器。 

- **Windows**  
在Windows中，大家可以使用putty来进行ssh服务。  
在网上，直接搜索安装putty即可。

- **Linux**  
首先打开终端，在终端中输入:  
` ssh username@ip_address`  
然后在终端中输入该帐号对应的密码即可。

## FTP服务
FTP服务为我们提供了远程文件传输服务。  
建议大家通过Filezilla使用ssh服务，它除了可以提供ftp服务外，还可以提供SFTP、FTPS、FTPES等服务，实在相当好用。
Filezilla同时拥有Windows和Linux版本。Filezilla的安装比较简单，请大家自行安装，谢谢！
> 具体操作方法，请参考[putty和Filezilla的相关操作](http://www.cnblogs.com/yiyi-xuechen/p/3566965.html)

## 远程桌面
由于某些同学需要使用服务器的桌面，因此，我安装了x2go来提供远程桌面服务。但是，使用远程桌面会占据一部分服务器/home或/temp下的空间，这部分空间并不充足，同时，使用远程桌面还会占用一些带宽，因此，**如无必要，请勿使用**。下面，我给大家提供x2go的使用方法。

- 安装  
  Windows或Mac OS X请在[这个网站](http://wiki.x2go.org/doku.php/start)下载安装。
  Linux直接在终端输入:  
` sudo apt-get install x2goserver`  
- 使用方法  
  * 打开x2go，点击左上方的编辑按钮
  ![png](/images/x2go1.png)
  * 点击New Session，创建一个新的连接 
  ![png](/images/x2go2.png)
  * 按下图方式配置，Session Name可以自己定义，注意Session Type选择xfce
  ![png](/images/x2go3.png)
  * 点击ok后，进入这个session，输入用户名和密码，进入系统
  ![png](/images/x2go4.png)
  * 由于现在的默认终端有问题，需要对终端进行重新设置。在桌面终端图标处,点击右键，设置终端属性。  
  ![png](/images/x2go5.png)
  * 点击编辑按钮
  ![png](/images/x2go6.png)
  * 编辑command的路径，使其与下图一致
  ![png](/images/x2go7.png)
  * 这样就可以使用这个远程桌面啦！不过终端的环境变量还要重新设置一下，这样就可以正常使用了！
  ![png](/images/x2go8.png)
