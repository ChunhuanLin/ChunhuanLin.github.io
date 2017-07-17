---
title: pip
date: 2017-07-02
categories: notes
tags: 
- linux
- tools
- python
---

## 安装

使用apt-get install pip会导致pip无法升级的问题  
因此这里使用的是  
`sudo easy_install pip`  

## 使用国内镜像源
以安装`pandas`为例：  
`pip  install --index https://pypi.mirrors.ustc.edu.cn/simple/ pandas`  
别的镜像：http://mirrors.sohu.com/python/
> 有时候，国内镜像更新不够及时（比如我装jupyter的时候），这时候就用默认的吧

## pip的使用
http://www.jianshu.com/p/9acc85d0ff16

## 安装位置
pip install ... 会直接安装在~/.local/lib/python2.7/site-packages这个目录下  
sudo pip install ... 安装在/usr/local/lib/... 这个目录下  
sudo pip install -t destination-dir package-name  指定安装路径  


