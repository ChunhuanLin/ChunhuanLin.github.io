---
title: hexo
date: 2017-07-14
layout: post
updated: 2017-07-15
comments: True
tags:
- notes
- guide
categories:
- notes
---
## 配置

* 安装npm  
```
sudo add-apt-repository ppa:chris-lea/node.js  
sudo apt-get update  
sudo apt-get install npm  
```

* 安装node.js  
  首先，在[官网](https://nodejs.org/en/)下载node.js的安装文件  
  然后将可执行文件复制到`/usr/bin`下：
  ```
  sudo ln -s ~/Libraries/node-v6.11.0-linux-x64/bin/node /usr/bin/node
  ```
  > 也可以将 `npm` 放到 `usr/bin` 下面，但是我已经装过了，就没有这么做  
  > 使用默认的node-v4.2.0版本，material的1.4版本会出错
	
* 根据官方的github主页配置  
  https://github.com/hexojs/hexo  

* 配置远程服务器
  - 在github创建一个名为`yourname.github.io`的仓库，然后在仓库处，设置域名（域名自行购买）  
  - 在站点配置文件`_config.yml`修改自己的github信息和url（这里填你的域名即可）  
  - 在站点的`source`文件夹下，放入一个CNAME文件，里面填入你的域名信息。事实上，在hexo的文件中，`/`这个路径都指`source`文件夹。  

## 使用的主题

  https://github.com/viosey/hexo-theme-material  
  配置站点文件`_config.yml`的language，设为zh-CN，否则会报错  

## 使用  

> 参考链接： https://hexo.io/docs/  

### 基本用法
* 创建一个post或者page  

  创建的命令为：`$ hexo new [layout] <title>`  
  - layout  
    有三种：`post`、`page`和`draft`，生成的路径分别为：`source/_posts`、`source`和`source/_drafts`   
  - 生成的文件的名字  
    文件名的默认设置在`_config.md`文件中可以设置，这里我设置默认生成的文件名为`title.md`  
  - draft  
    draft是不会被显示到网页上的，可以使用：  
    `$ hexo publish [layout] <title>`  
    来讲`draft`移动到`source/_posts`下，使其成为`post`  
* Front-matter  
  Front-matter是在YAML文件或者JSON文件头部，用来描述书写设置的东西。YAML以`---`结束，而JSON以`;;;`结束  
  - 可以设置的参数：  


  | Setting             | Description 	                              	        | Default|
  | :------------------ | :---------------------------------------------------- | :----- |
  | layout 		| Layout                                                |        |
  | title 		| Title 						|        |
  | date   		| Published dat			          		| File created date|
  | updated 		| Updated date            				| File updated date|
  | comments 		| Enables comment feature for the post 			| true   |
  | tags  		| Tags (Not available for pages) 			|        |
  | categories 		| Categories (Not available for pages) 	  		|        |
  | permalink 		| Overrides the default permalink of the post 	        |        | 

  - Categories & Tags  
  `categories`是按一定顺序组织的，因此书写时要按一定顺序，而`tags`则无顺序要求，书写方式如下：  
    ```
    categories:
    - Sports
    - Baseball
    tags:
    - Injury
    - Fight
    - Shocking
    ```
* 标签插件（Tag Plugins）的语法  
  > 原文链接：https://hexo.io/docs/tag-plugins.html     

  - 引用（Block Quote）  
    形如：  
    ```
    {% blockquote [author[, source]] [link] [source_link_title] %}  
    content  
    {% endblockquote %}  
    ```
    例如引用网页链接：  
    ```
    {% blockquote Seth Godin http://sethgodin.typepad.com/seths_blog/2009/07/welcome-to-island-marketing.html Welcome to Island Marketing %}  
    Every interaction is both precious and an opportunity to delight.  
    {% endblockquote %}  
    ```
  - 代码块  
    形如：  
    ```
    {% codeblock [title] [lang:language] [url] [link text] %}  
    code snippet  
    {% endcodeblock %}   
    ```
    例如使用objc语言：  
    ```
    {% codeblock lang:objc %}  
    [rectangle setX: 10 y: 10 width: 20 height: 20];  
    {% endcodeblock %}  
    ```
  - 加入其他post的链接  
    ```
    {% post_path slug %}  
    {% post_link slug [title] %}  
    ```
  - 插入固定大小的图片  
    ```
    {% img [class names] /path/to/image [width] [height] [title text [alt text]] %}  
    ```
  - 插入YouTube视频  
    ```
    {% youtube video_id %}  
    ```
  - 更多其他功能请看[原文链接](https://hexo.io/docs/tag-plugins.html)  

* Asset Floders  
  - Global Asset Folder  
    Assets指`source`文件夹里的非post文件，比如图片，CSS，JavaScript等等。 比如获得图片使用这种方式就可以：  
    `![](/images/image.jpg)`  
  - Post Asset Folder  
    在`_config.yml`文件中，做如下修改：  
    `post_asset_folder: true`  
    那么在你使用`hexo new [layout] <title>`生成一个新的post的时候，那么会生成跟这个post同名的一个Asset，用来存储他相关的文件。  
  - 使用markdown格式的相对路径会无法在网页上显示，因此要使用Tag Plugin的语法  
    ```
    {% asset_path slug %}  
    {% asset_img slug [title] %}  
    {% asset_link slug [title] %}  
    ```

* Server  
  - 指定启动服务器ip和端口  
    ```
    hexo server -i 192.168.1.1  
    hexo server -p 5000  
    ```
  - 静态模式  
    通常，直接使用server查看的并不是生成后的静态网页，生成静态网页首先要使用`genrate`，要查看静态网页要加上`-s`参数：  
    `hexo server -s`  

* 生成页面  
  `hexo generate --watch` 只将有修改的文件进行重新生成  
   
 生成后配置，下面两条命令是等价的：  
  ```
  $ hexo generate --deploy  
  $ hexo deploy --generate  
  ```

* 配置  
  `hexo deploy` 将站点配置到远程服务器  

### 个性化

* Permalinks  
  可以使用这些变量对`_config.yml`进行设置  


  | Variable 	| Description|
  |:------------|:-----------|
  | `:year` 	| Published year of posts (4-digit)
  | `:month` 	| Published month of posts (2-digit)
  | `:i_month` 	| Published month of posts (Without leading zeros)
  | `:day` 	| Published day of posts (2-digit)
  | `:i_day`	| Published day of posts (Without leading zeros)
  | `:title` 	| Filename
  | `:id` 	|  Post ID
  | `:category` | Categories. If the post is uncategorized, it will use the default_category value.


   
  修改`_config.yml`文件中的`deploy`项，hexo也可以同时进行多个部署：  
  ```
  deploy:
  - type: git
    repo:
  - type: heroku
    repo:
  ```
  - Git 
  `$ npm install hexo-deployer-git --save`  
  编辑设置：  
  ```
  deploy:
  type: git  
  repo: <repository url>  
  branch: [branch]  
  message: [message]  
  ```
* 主题  
  官方主题库： https://hexo.io/themes/  

* 变量  
  可以在[这个页面](https://hexo.io/docs/variables.html)看到所有变量。  

### Material的使用
> 参考链接： https://material.viosey.com/  



## Debug
* 由于不明原因，默认启动的iP若使用`localhost`则打不开，而使用`127.0.0.1`却能打开。因此修改这个文件`/node_modules/hexo-server/lib/server.js`,将其中配置ip的那行改为：  
  `var ip = args.i || args.ip || '127.0.0.1' || config.server.ip;`
