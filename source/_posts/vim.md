---
title: vim
date: 2017-07-03
updated: 2018-09-05
categories: Linux
tags:
- linux
- vim
- tools
---

## 配置文件的位置  

   终端中输入`vim`  
   命令模式输入`:echo $VIM`  

## vim安装markdown插件  
   参考链接： http://www.jianshu.com/p/44d31327f953  
   项目链接： https://github.com/suan/vim-instant-markdown      
   安装了vim-instant-markdown插件,输入下面这个命令可以进行预览：  
   `:InstantMarkdownPreview`    

   * 安装新版的`node.js`  
     ```
     sudo add-apt-repository ppa:chris-lea/node.js
     sudo apt-get update
     sudo apt-get install nodejs
     ```
   * 安装instant-markdown-d  
     `sudo npm -g install instant-markdown-d`  
   * 按照参考链接的步骤配置vim配置文件/usr/share/vim/vimrc  
   * 下载项目链接里的文件，放在~/.vim下,修改配置文件，加入`Plugin 文件地址`    
   * 在vim里面运行PluginInstall,安装完成  
   * 在~/.vim/bundle/plugin里面寻找`instant-markdown-d`，设置InstantMarkdown禁止自动启动，而是通过输入命令启动，并更改其启动的命令的名称：  
	> vimrc中加入`command Preview InstantMarkdownPreview` 简单更改为`Preview`  

## 快捷键学习
参考www.openvim.com
* 移动方向：hjkl
* 移动到词首、词尾：web，前面可加数字，表示移动几个词
* 插入重复的自符，如：30i- Esc，插入30个-
* 向前或向后查找某个词，f/F，如3fo，找到第三个出现的o
* 跳转到括号前/后：％
* 跳转到句首或句尾：0/$
* 找到下/上一个跟光标一样的字：*/# 
* 文件首/尾/指定行号：gg/G/nG
* 关键字查找：/;也可以用正则表达式
* 插入行：o/O
* 删除光标处字符x/X
* 替换光标处字符：r
* 删除单词：dw;删除两个单词:d2e
* 重复上一个操作：.
* visual模式：v进入模式，然后hjkl选择文本，d删除
* u撤销;ctrl+R前进
* 帮助：:help

* 
