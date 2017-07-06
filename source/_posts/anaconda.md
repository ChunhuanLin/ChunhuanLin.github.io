---
title: Anaconda 
date: 2017-07-04
categories: notes
tags:
- tools
- python
- linux
---

## 配置路径  

安装完anaconda后，numpy出错，原因不明，这时将anaconda安装路径下的./lib/python2.7/site-package/ 路径放到python路径的首选项中，解决问题  

--------------------修改解决方案------------------------

发现似乎是因为使用了anaconda的python，出错的原因不清楚，为了不让之前安装的库白费功夫，将默认使用的python改成原来的python，只需要在添加PATH路径时，让anaconda的路径排到最后。同样将anaconda的python库放到最后。   

------------------- 最终解决方案-----------------------

Jupyter使用的python是由anaconda提供的，因此我不能用上一个方案，这样使用anaconda版的python时，有些库会不能导入。因此，我把根目录地下python的一些库全部搬到anaconda底下了……
