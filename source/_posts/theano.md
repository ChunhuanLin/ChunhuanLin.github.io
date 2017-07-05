---
title: Theano
date: 2017-07-01
categories: Deep Learning
tags: 
- Deep Learning
- libs
---

## 使用GPU  
theano需要手工设定使用GPU，方法如下:  
`vim ~/.theanorc`

add these content
[global]
device=gpu
floatX=float32
