---
title: 《Docker-从入门到实践》 
date: 2018-09-05 15:50:55
tags:
- docker
- notes
- tools
categories:
- book
---

## 基本概念
三个基本概念：镜像（Image）、容器（Container）、仓库（Repository）
### Docker镜像
操作系统分为内核和用户空间。对于 Linux 而言，内核启动后，会挂载 root文件系统为其提供用户空间支持。而 Docker 镜像（Image），就相当于是一个 root 文件系统。比如官方镜像 ubuntu:16.04 就包含了完整的一套 Ubuntu 16.04 最小系统的 root 文件系统。
Docker 镜像是一个特殊的文件系统，除了提供容器运行时所需的程序、库、资源、配置等文件外，还包含了一些为运行时准备的一些配置参数（如匿名卷、环境变量、用户等）。镜像不包含任何动态数据，其内容在构建之后也不会被改变。
#### 分层存储
镜像构建时，会一层层构建，前一层是后一层的基础。每一层构建完就不会再发生改变，后一层上的任何改变只发生在自己这一层。
分层存储的特征还使得镜像的复用、定制变的更为容易。甚至可以用之前构建好的镜像作为基础层，然后进一步添加新的层，以定制自己所需的内容，构建新的镜像。
### Docker容器
镜像（ Image ）和容器（ Container ）的关系，就像是面向对象程序设计中的 类 和 实例一样，镜像是静态的定义，容器是镜像运行时的实体。容器可以被创建、启动、停止、删除、暂停等。
容器的实质是进程，但与直接在宿主执行的进程不同，容器进程运行于属于自己的独立的命名空间。因此容器可以拥有自己的 root 文件系统、自己的网络配置、自己的进程空间，甚至自己的用户 ID 空间。前面讲过镜像使用的是分层存储，容器也是如此。每一个容器运行时，是以镜像为基础层，
在其上创建一个当前容器的存储层，我们可以称这个为容器运行时读写而准备的存储层为容器存储层。
容器存储层的生存周期和容器一样，容器消亡时，容器存储层也随之消亡。因此，任何保存于容器存储层的信息都会随容器删除而丢失。
按照 Docker 最佳实践的要求，容器不应该向其存储层内写入任何数据，容器存储层要保持无状态化。所有的文件写入操作，都应该使用 数据卷（Volume）、或者绑定宿主目录，在这些位置的读写会跳过容器存储层，直接对宿主（或网络存储）发生读写，其性能和稳定性更高。
数据卷的生存周期独立于容器，容器消亡，数据卷不会消亡。因此，使用数据卷后，容器删除或者重新运行之后，数据却不会丢失。
### Docker Registry
一个 Docker Registry 中可以包含多个仓库（ Repository ）；每个仓库可以包含多个标签（ Tag ）；每个标签对应一个镜像。
通常，一个仓库会包含同一个软件不同版本的镜像，而标签就常用于对应该软件的各个版本。我们可以通过 <仓库名>:<标签> 的格式来指定具体是这个软件哪个版本的镜像。如果不给出标签，将以 latest 作为默认标签。
仓库名经常以 两段式路径 形式出现，比如 jwilder/nginx-proxy ，前者往往意味着 Docker　Registry 多用户环境下的用户名，后者则往往是对应的软件名。
#### Docker Registry 公开服务
最常使用的 Registry 公开服务是官方的 Docker Hub，这也是默认的 Registry，并拥有大量的高质量的官方镜像。除此以外，还有 CoreOS 的 Quay.io，CoreOS 相关的镜像存储在这里；Google 的 Google Container Registry，Kubernetes 的镜像使用的就是这个服务。
由于某些原因，在国内访问这些服务可能会比较慢。国内的一些云服务商提供了针对 Docker Hub 的镜像服务（ Registry Mirror ），这些镜像服务被称为加速器。常见的有 阿里云加速器、DaoCloud 加速器 等。使用加速器会直接从国内的地址下载 Docker Hub 的镜像，比直接从 Docker Hub 下载速度会提高很多。
#### 私有 Docker Registry
除了使用公开服务外，用户还可以在本地搭建私有 Docker Registry。Docker 官方提供了Docker Registry 镜像，可以直接使用做为私有 Registry 服务。
#### 高级服务
开源的 Docker Registry 镜像只提供了 Docker Registry API 的服务端实现，足以支持docker 命令，不影响使用。但不包含图形界面，以及镜像维护、用户管理、访问控制等高级功能。在官方的商业化版本 Docker Trusted Registry 中，提供了这些高级功能。
除了官方的 Docker Registry 外，还有第三方软件实现了 Docker Registry API，甚至提供了用户界面以及一些高级功能。比如，VMWare Harbor 和 Sonatype Nexus。

## 安装Docker
详情见书籍，注意使用国内源，并配置加速器

## 使用镜像
### 获取镜像
`docker pull [选项] [Docker Registry 地址[:端口号]/]仓库名[:标签]`
* 选项：`docker pull --help`查看
* Docker 镜像仓库地址：地址的格式一般是 <域名/IP>[:端口号] 。默认地址是 Docker Hub
* 仓库名：如之前所说，这里的仓库名是两段式名称，即 <用户名>/<软件名> 。对于 Docker Hub，如果不给出用户名，则默认为 library ，也就是官方镜像   

### 运行
`docker run -it --rm ubuntu:16.04 bash`
* -it ：这是两个参数，一个是 -i ：交互式操作，一个是 -t 终端。我们这里打算进入bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
* --rm ：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm 。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
* ubuntu:16.04 ：这是指用 ubuntu:16.04 镜像为基础来启动容器。
* bash ：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash 。  

### 列出镜像
`docker image ls`  

#### 镜像体积
* docker image ls
- Docker Hub上是压缩体积，而本地是展开后的体积，因此本地大
- 由于镜像的多层结构，有一些可以继承、复用，因此实际占用空间小于列表中显示的空间总和
* docker system df
- 便捷的查看镜像、容器、数据卷所占用的空间  

#### 虚悬镜像
* 特点：仓库名、标签等信息都是<none>
* 显示这类镜像：docker image ls -f dangling=true
* 产生原因：旧镜像名称与新镜像同名，故被抹去信息
* 产生环境：docker pull或docker build时，可能产生这种现象
* 删除：docker image prune  

#### 中间层镜像
* docker image ls -a 将会把中间层镜像也显示出来
* 特点：无标签
* 删除： 不能删除此类镜像，在将依赖他们的镜像删除后，他们自然会被删除  

#### 列出部分镜像
* 根据仓库名：docker iamge ls <仓库名>
* 指定仓库名和标签：docker image ls <仓库名：标签>
* 过滤器：加入--filter或-f参数，常用的filter参数有：since/before/label等
* 只列出id: -q参数
* 按一定格式输出，--format 参数， 并利用GO的模板语法  

### 删除本地镜像
`docker image rm [选项] <镜像1> [<镜像2> ...]`  

#### 用ID、镜像名、摘要删除镜像
* 短ID和长ID
  - 可以用镜像的长ID，也即完整ID
  - 更方便的，可以用短ID，即长ID的前几位（一般3位），只要能够区分不同镜像即可。
* 镜像名
  - 即<仓库名>:<标签>
* 镜像摘要
  - `docker iamge ls --digests` 列出摘要
  - `docker iamge rm <res>@<digest>`
* 配合`docker image ls`命令
  - `docker image rm $(docker image ls -q redis)`
  - `docker image rm $(docker image ls -q -f before=mongo:3.2)`  

##### Untagged 和Deleted
* 删除镜像时，会显示Untagged或Deleted信息
* 镜像的唯一标识是ID和摘要，一个镜像可以有多个标签
* 删除镜像时，实际上是删除某个标签的镜像，因此得到Untagged信息。 
* 输出的是Deleted信息，必须*同时*满足：
  - 该镜像所有标签都没有了
  - 没有任何层依赖当前层
  - 此时没有这个镜像启动的容器存在  

### 利用commit理解镜像构成
* 用于学习，也可以用于被入侵后保留现场
* 不要用`docker commit`定制镜像，应该使用`Dockerfile`
* 具体学习内容见书籍
* 进行一个简单的实验，我们运行一个镜像的容器，然后对这个容器进行修改，那么容器的*存储层*会发生改变，换言之，原有镜像+容器的存储层==新镜像，这时候就可以commit：
  `docker commit [选项] <容器ID或容器名> [<仓库名>[:<标签>]]`
  例如可以这样保存：
  ```
  $ docker commit	\
	--author "Tao Wang <twang2218@gmail.com>" \ //作者信息
	--message "修改了默认网页" \ // 修改的内容说明
	webserver \ // 容器名称
	nginx:v2 // 修改后保存的镜像名称
  ```
* 几个命令
  - `docker diff <容器名>`
  - `docker history <镜像名>`

#### 慎用`docker commit`
* 利用commit方法，生成镜像时，产生大量无用文件
* 别人无从得知这个镜像是如何得到的，因此，这种方式得到的镜像也被称为“黑箱镜像”
* 每一次修改只会增加新层，对旧层无法删除，因此会使镜像变得臃肿

### 使用Dockerfile定制镜像
Dockerfile是一个文本文件,其内包含了一条条的指令(Instruction),每一条指令构建一层,因此每一条指令的内容,就是描述该层应当如何构建。

#### FROM指定基础镜像
* 必须是第一条指令
* 指示从哪个镜像开始修改
* [Docker Store](https://store.docker.com)有很多官方镜像。服务类镜像，如[python](https://store.docker.com/images/python/)、操作系统镜像，如[ubuntu](https://store.docker.com/images/ubuntu/)
* 特别地，存在一个空白镜像，名为`scratch`,适用于可执行文件直接复制到docker，而无需操作系统支持的情况；其次，使用Go语言开发的应用很多会使用这种方式制作镜像。

#### RUN执行命令
* `RUN`指令有两种格式：
  - shell格式，`RUN <命令>`
  - exec格式，`RUN ["可执行文件", "参数1", "参数2"]`
* RUN指令也是每个指令会新建一层，故不能每行都写RUN
* Union FS目前最大不得超过127层
* 注意能用一层写就用一层，可以使用*&&*连接符;在最后要进行*清理工作*

#### 构建镜像
得到`Dockerfile`之后就可以开始构建镜像了,在*Dockerfile所在目录*运行
`docker	build	[选项]	<上下文路径/URL/->`
如： `docker build -t nginx:v3 .`

#### 镜像构建上下文（Context）
* 构建镜像时用到的上下文路径并非指`Dockerfile`路径
* `docker build`的工作原理实则是C/S设计的模式，`docker`命令作为客户端，调用远程Docker引擎的API，进而进行build。而构建镜像时，可能需要用到本地的文件，为了使得远程引擎可以获得这些地址，需要告诉他们上下文信息，这样Dockerfile中的路径，就可以被正确的解读。
* 正确方法是：
  - 在空目录/项目根目录下放置Dockerfile
  - 将所需的文件拷贝一份到当前目录下
  - 如果当前目录下有不需要传给Docker引擎的，那么可以用`.gitignore`的语法，写一个`.dockerignore`
* 默认会将上下文目录中的`Dockerfile`文件视作Dockerfile，实际上可以通过-f参数，对Dockerfile进行配置

#### 其他`docker build`用法
1. Git repo, 如docker build https://github.com/twang2218/gitlab-ce-zh.git
2. tar压缩包，如`docker	build http://server/context.tar.gz`
3. 从标准输入中读取Dockerfile（文本文件），如`docker build - < Dockerfile`或`cat Dockerfile | docker build -`
4. 从标准输入中读取上下文压缩包（压缩包格式文件），如 `docker build - < context.tar.gz` 

### Dockerfile指令详解
除了`FROM`，`RUN`，`Dockerfile`还有很多其他指令。
参考文档：
  - [Dockerfile官方文档](https://docs.docker.com/engine/reference/builder/)
  - [Dockerfile最佳实践文档](https://docs.docker.com/engine/userguide/eng-image/dockerfile_best-practices/)
  - [Docker官方镜像的Dockerfile](https://github.com/docker-library/docs)

#### COPY复制文件
* 格式
  - `COPY <源路径>... <目标路径>`
  - `COPY ["<源路径1>",... "<目标路径>"]`
* 可以有多个源路径，可以使用通配符，规则与Go的[filepath.Match](https://golang.org/pkg/path/filepath/#Match)一致
* 会保留源文件的元数据，比如说文件权限，变更时间等

#### ADD更高级的复制文件
* 高配版COPY，具体表现在：
  - 源文件可以是`URL`，但这个命令有限制，所以不实用，不如直接使用`RUN`
  - 自动解压缩，如果源文件是压缩文件，会自动解压到`目标路径`
* 建议： *仅在需要自动解压缩的场合使用`ADD`*

#### CMD容器启动命令
* 容器就是进程，因此启动容器时，需要指定所运行的程序和参数，这就用到了CMD
* 在运行时，可以指定新的命令来替代镜像设置中CMD的命令。比如,`ubuntu`镜像默认的`CMD`是`/bin/bash`,如果我们直接`docker run -it ubuntu`的话,会直接进入`bash` 。我们也可以在运行时指定运行别的命令,如`docker run -it ubuntu cat /etc/os-release`。这就是用`cat /etc/os-release`命令替换了默认的`/bin/bash`命令了,输出了系统版本信息。
* 格式--推荐使用*exec*格式
  - shell格式: `CMD <命令>`
  - exec格式: `CMD ["可执行文件", "参数1", "参数2"...]`	
  - 参数列表格式: `CMD ["参数1", "参数2"...]`。在指定了`ENTRYPOINT`指令后,用`CMD`指定具体的参数。
* 容器内没有后台服务的概念，不能像虚拟机、物理机那样启动后台服务，应该直接执行可执行文件，并要求以前台形式运行，如：`CMD ["nginx", "-g", "daemon off;"]`

#### ENTRYPOINT入口点
* `ENTRYPOINT`和`CMD`一样，都用于指定容器启动程序和参数
* 当制定`ENTRYPOINT`后，`CMD`的内容就只能是作为`ENTRYPOINT`的参数
* `ENTRYPOINT`可以在容器启动时，被替代；不过与`CMD`不同的是，他需要特别的使用`--entrypoint`参数来指定
* 使用场合：
  - 让镜像变成像命令一样使用
    由容器运行的格式，`docker run <镜像> <CMD>`，那么如果我们在Dockerfile里指定的是`ENTRYPOINT`，我们<CMD>就可以作为参数传递给`ENTRYPOINT`，这样就使得镜像可以像命令一样使用（可以传递参数）
  - 应用运行前的准备工作
    可以写一个脚本，这个脚本由`ENTRYPOINT`启动运行，这里将会执行一些主程序运行前的准备工作；然后在`CMD`写如最后需要执行的命令，它将作为参数传入到该脚本中，交给该脚本最终运行。

#### ENV设置环境变量
* 格式:
  - `ENV <key> <value>`
  - `ENV <key1>=<value1> <key2>=<value2>...`
* 使用方法
  - `\`可以用于换行，对含空格的值可以用双引号括起来
  - 使用：`$key`

#### ARG构建参数
* 格式：`ARG <参数名>[=<默认值>]`
* 同样用于设置环境变量，但这个变量在容器运行时是不会存在的
* `ARG`设置的变量和默认值可以在构建命令（`docker build`）时，加入`--build-arg <参数名>=<值>`来覆盖

#### VOLUME定义匿名卷
* 格式：
  - `VOLUME ["<路径1>", "<路径2>"...]`
  - `VOLUME <路径>`
* 为保证容器存储层不发生写操作，对数据库类需要保存动态数据的应用，其数据库文件应该保存到`卷（volume）`中
* 可以事先制定某些目录挂载为匿名卷

#### EXPOSE声明端口
* 格式：`EXPOSE <端口1>	[<端口2>...]`
* `EXPOSE`指令是声明运行时容器提供服务端口,这只是一个声明,在运行时并不会因为这个声明应用就会开启这个端口的服务。在`Dockerfile`中写入这样的声明有两个好处,一个是帮助镜像使用者理解这个镜像服务的守护端口,以方便配置映射;另一个用处则是在运行时使用随机端口映射时,也就是`docker run -P`时,会自动随机映射`EXPOSE`端口。
* 要将`EXPOSE`和在运行时使用`-p <宿主端口>:<容器端口>`区分开来。`-p`,是映射宿主端口和容器端口,换句话说,就是将容器的对应端口服务公开给外界访问,而`EXPOSE`仅仅是声明容器打算使用什么端口而已,并不会自动在宿主进行端口映射。

#### WORKDIR指定工作目录
* 格式：`WORKDIR <工作目录路径>`	
* 使用`WORKDIR`指令可以来指定工作目录(或者称为当前目录),以后各层的当前目录就被改为指定的目录,如该目录不存在,`WORKDIR`会帮你建立目录。
* 理解工作目录：
  ```
  RUN cd /app
  RUN echo "hello" > world.txt
  ```
  上述命令并不会真正的cd到/app目录，并在该目录下创建文件。这是由于在shell中连续两行是在同一个进程中执行，而docker中，两个`RUN`实质实在不同的执行环境中执行的，两个镜像层之间互不影响，因此此时就需要使用WORKDIR。

#### USER指定当前用户
* 格式：`USER <用户名>`
* `USER`改变之后的层的用户，但这个用户必须在事先建立好
* 案例一：建立用户并执行命令
  ```
  RUN groupadd -r redis && useradd -r -g redis redis
  USER redis
  RUN ["redis-server"]
  ```
* 案例二：使用`gosu`，以`root`来执行脚本，并且在执行期间改变身份。
  ```
  # 建立redis用户,并使用gosu换另一个用户执行命令
  RUN groupadd -r redis && useradd -r -g redis redis
  # 下载gosu
  RUN wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/1.7/gosu-amd64" \
      && chmod +x /usr/local/bin/gosu \
      && gosu nobody true
  # 设置CMD,并以另外的用户执行
  CMD ["exec", "gosu", "redis", "redis-server"]
  ```
#### HEALTHCHECK健康检查
* 格式：
  - `HEALTHCHECK [选项] CMD <命令>`:设置检查容器健康状况的命令
  - `HEALTHCHECK NONE`:如果基础镜像有健康检查指令,使用这行可以屏蔽掉其健康检查指令
* 与`CMD`，`ENTRYPOINT`一样，`HEALTHCHECK`只可以出现一次，如果写了多个，只有最后一个生效
* 选项：
  - `--interval=<间隔>`：两次健康检查的间隔,默认为30秒
  - `--timeout=<时长>`：健康检查命令运行超时时间,如果超过这个时间,本次健康检查就被视为失败,默认30秒
  - `--retries=<次数>`：当连续失败指定次数后,则将容器状态视为`unhealthy`,默认3次 
* 使用:	
  - 命令返回值分别为：0:成功;1:失败;2:保留,不要使用这个值，可以在命令后加`|| exit 1`以只显示0或1
  - 运行镜像后，运行`docker ps`查看健康状态；`docker inspect`查看健康检查命令的输出

#### ONBUILD为他人做嫁衣裳
* 格式：`ONBUILD <其它指令>`
* 它后面跟的是其它指令,比如`RUN`,`COPY`等,而这些指令,在当前镜像构建时并不会被执行。只有当以当前镜像为基础镜像,去构建下一级镜像的时候才会被执行。
* 用于写基础镜像，方便其他镜像继承

### 多阶段构建

#### 之前的做法
* 全部放入一个Dockerfile
  - Dockerfile 特别长,可维护性降低
  - 镜像层次多,镜像体积较大,部署时间变长
  - 源代码存在泄露的风险
* 分散到多个Dockerfile
  - 部署过程复杂

#### 多阶段构建
* 写到一个Dockerfile里
* 不同阶段用`FROM`隔开

#### 其他制作镜像的方式
1. 从rootfs压缩包导入
2. `docker save`和`docker load`
  * 用于镜像迁移，现在更推荐`Docker Registry`
  * 保存镜像：`docker save alpine | gzip > alpine-latest.tar.gz`
  * 加载镜像：`docker load -i alpine-latest.tar.gz`


## 操作容器
简单的说,容器是独立运行的一个或一组应用,以及它们的运行态环境。对应的,虚拟机可以理解为模拟运行的一整套操作系统(提供了运行态环境和其他系统环境)和跑在上面的应用。

### 启动
启动容器有两种方式,一种是基于镜像新建一个容器并启动,另外一个是将在终止状态(`stopped`)的容器重新启动。
因为`Docker`的容器实在太轻量级了,很多时候用户都是随时删除和新创建容器。

#### 新建并启动
* 命令：`docker run`
* 当利用`docker run`来创建容器时,Docker	在后台运行的标准操作包括:
  - 检查本地是否存在指定的镜像,不存在就从公有仓库下载
  - 利用镜像创建并启动一个容器
  - 分配一个文件系统,并在只读的镜像层外面挂载一层可读写层
  - 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
  - 从地址池配置一个`ip`地址给容器
  - 执行用户指定的应用程序
  - 执行完毕后容器被终止 
* 常用参数：
  - -t：让Docker分配一个伪终端(pseudo-tty)并绑定到容器的标准输入上,	 
  - -i：让容器的标准输入保持打开。

#### 启动已终止容器
* 命令：`docker container start`

### 后台运行
* 使用`-d`参数运行容器，则执行结果不会打印到宿主机上
* 可以用`docker container ls`查看容器信息
* `docker container logs [container ID or NAMES]`查看容器输出信息

### 终止
* 命令：`docker container stop`
* 此外,当`Docker`容器中指定的应用终结时,容器也自动终止。
* 终止状态的容器可以用`docker container ls -a`命令看到

### 进入容器
`-d`会使容器进入后台，某些时候需要进入容器进行操作,包括使用`docker attach`命令或`docker exec`命令,推荐大家使用`docker exec`命令

#### `attach`命令
示例：
```
docker run -dit ubuntu
docker attach <container>
```

#### `exec`命令
* 与`attach`的使用类似，但是在`exec`的`stdin`中`exit`不会导致容器停止，因此推荐`exec`命令

### 导出和导入

#### 导出
* 命令：`docker export`
* 示例：
  ```
  docker ps -a
  docker export <container id> > <name.tar>
  ```
#### 导入
* 命令：`docker import`
* 示例：
  ```
  cat ubuntu.tar | docker import - test/ubuntu:v1.0
  docker image ls
  ```

### 删除
* 删除一个处于终止状态的容器
  `docker container rm <container>`
* 删除所有处于终止状态的容器
  `docker container prune`

## 访问仓库
仓库(`Repository`)是集中存放镜像的地方。
一个容易混淆的概念是注册服务器(`Registry`)。实际上注册服务器是管理仓库的具体服务器,每个服务器上可以有多个仓库,而每个仓库下面有多个镜像。从这方面来说,仓库可以被认为是一个具体的项目或目录。例如对于仓库地址`dl.dockerpool.com/ubuntu`来说,`dl.dockerpool.com`是注册服务器地址,`ubuntu`是仓库名。大部分时候,并不需要严格区分这两者的概念。

### Docker Hub
* 注册与登录
  1. [官网](https://cloud.docker.com)注册
  2. `docker login`登录；`docker logout`登出
* 查找镜像
  - `docker search <关键字>`
  - 镜像名称：官方镜像通常是单个单词；用户创建的通常格式为`username/image`
  - `--filter=stars=N`参数，指定只搜索star数量为N以上的镜像
* 下载镜像
  `docker pull <镜像名>`
* 推送镜像
  1. `docker tag ubuntu:17.10 username/ubuntu:17.10`
  2. `docker image ls` 查看是否以生成名为`username/ubuntu:17.10`的镜像
  3. `docker push username/ubuntu:17.10`
  4. `docker search username`可以找到自己上传的镜像了
* 自动创建
  自动创建允许用户通过`Docker Hub`指定跟踪一个目标网站(目前支持`GitHub`或`BitBucket`)上的项目,一旦项目发生新的提交或者创建新的标签(tag),`Docker Hub`会自动构建镜像并推送到`Docker Hub`中。

## 数据管理
这一章介绍如何在`Docker`内部以及容器之间管理数据

### 数据卷
`数据卷`是一个可供一个或多个容器使用的特殊目录,它绕过`UFS`,可以提供很多有用的特性:
* `数据卷`可以在容器之间共享和重用
* 对`数据卷`的修改会立马生效
* 对`数据卷`的更新,不会影响镜像
* `数据卷`默认会一直存在,即使容器被删除
> 注意:  数据卷的使用,类似于Linux下对目录或文件进行mount,镜像中的被指定为挂载点的目录中的文件会隐藏掉,能显示看的是挂载的`数据卷`。
常用操作与注意事项：
* 建议使用`--mount`参数，而不是`-v`参数
* 创建数据卷：`docker volume create <数据卷>`
* 查看所有数据卷：`docker volume ls`
* 查看指定数据卷信息：`docker volume inspect <数据卷>`
* 启动一个挂载数据卷的容器：`docker run`后面加入`--mount source=<volume>,target=<directory>`
* 查看数据卷的具体信息：`docker inspect <container>`
* 删除数据卷：`docker volume rm <volume>`，数据卷的生命周期独立于容器，容器删除后不会删除数据卷，如果要在删除容器时，同时删除数据卷，那么可以使用`docker rm -v`这个命令
* 删除所有无主的数据卷：`docker volume prune`

### 挂载主机目录
* 仍旧推荐使用`--mount`参数
* 挂载一个主机目录作为数据卷：`docker run`后面加入`--mount type=bind,source=/src/webapp,target=/opt/webapp,readonly`,readonly表示只读
* 挂载一个主机文件作为数据卷，如下方法就可以记录在容器中输入过的命令：
  ```
  docker run --rm -it \
    # -v $HOME/.bash_history:/root/.bash_history	\
    --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history	\
    ubuntu:17.10 \
    bash
  ```




## 使用网络
