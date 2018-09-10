---
title: Ubuntu网络配置
date: 2018-09-10 09:47:00
tags:
- tools
- linux
categories:
- Linux
---

## 配置拨号网络
由于学校网络需要拨号，每次从网页进去相当麻烦，故直接在系统中设置好拨号
1. `sudo pppoeconf` 一路Yes，输入上网帐号
2. `sudo vim /etc/NetworkManager/NetworkManger.conf`将其中的managed=false改为managed=true
3. `sudo service network-manager restart` 
参考[链接](https://blog.csdn.net/essity/article/details/52618101)


## 安装chrome
参考[链接](https://blog.csdn.net/wql2014302721/article/details/78571362)

## 配置系统代理
在系统设置->Network中，修改Network proxy，其中的Socks Host为127.0.0.1|1080

## 配置并运行VPN

## 配置Chrome
1. 以代理方式启动chrome，`google-chrome --proxy-server="socks5://localhost:1080"`，此时应该可以google了～
2. 打开chrome[应用商店](https://chrome.google.com/webstore?utm_source=chrome-ntp-icon)，下载[SwitchyOmega](https://www.switchyomega.com/)
3. 在SwitchOmega中[设置autoswitch](https://www.switchyomega.com/settings/)上网模式，具体方法如下：
* 打开SwitchOmega->option
* 建立新的情景模式（Profiles),命名为SS，配置为：Protocol--SOCKS5、Server--127.0.0.1、Port--1080, 底下忽略规则删去127.0.0.1
* 修改情景模式auto swich，点击add condition,设置规则列表：
```
规则列表格式： AutoProxy   
规则列表网址： https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt
```
* 以auto switch模式运行switch omega，无需再配置pac

