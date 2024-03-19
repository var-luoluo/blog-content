---
title: Hi docker
date: 2024-03-19 12:01:55
updated: 2024-03-19 12:01:55
tags: docker
excerpt: docker安利--新手友好型
index_img: https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/docker-moby.png
---

# Hi docker~

> 本文章仅面对非专业人员，以便于使用docker的最基本功能，不会涉及太多原理与命令

![docker](https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/png/docker-moby.png)

## 前言

在本篇文章，我们会暂时放弃某些概念的严谨性，以便于概念的解释，同时我们会在合适的地方指出这些不严谨，这可能看起来会很啰嗦，抱歉。不过不必太在意这些不严谨，它并不影响你现阶段的正常理解和使用。

~~以及，我写东西的语言风格比较奇怪，看起来像是翻译生硬的外文课本，emmm，大抵如此~~

在这篇文章中，你将会看到:
- docker原理笼统的解释；
- Windows系统下,docker desktop桌面软件的安装和详细配置；
- docker中基本概念的解释；
- ~~如何处理docker的网络问题；~~
- docker命令的不详细解释；
- 常见docker容器的部署案例；
- 如何获取需要的compose文件；

你不会看到:
- ~~docker原理细致的介绍；~~
- ~~类Unix系统的docker安装；~~
- ~~使用docker run运行docker容器；~~
- ~~如何自己编写compose文件；~~
- ~~如何自己编写Dockerfile；~~
- ~~K8s以及其它相关技术；~~

## 所以，什么是docker

首先需要明确的是，`docker` 是一系列概念的代表：它可能代表着一家公司，一项技术，一个软件甚至是某种符号。

本篇文章涉及概念的时候，我们将其视为一项技术，即容器管理技术。
而到实际的安装、使用过程中，它代表着运行在操作系统上的一款软件。

所以，我们说，`docker`是一项容器管理技术；
这里出现了新的概念–容器。

### 容器

想象一下一个运行在操作系统中的本地图片查看器，它大概率不会需要使用网络服务，也不会用到声卡……对于一个完整的操作系统，它不会使用其提供的绝大部分的功能。

现在我们将该应用程序和其依赖的环境(比如文件的IO、显示器交互逻辑等)以某种方式打包，就构成了一个**容器**。即**容器**是特定的程序及其依赖的运行环境组成的，我们称呼这个整体为一个**容器**。

`docker` 技术可以使这些容器以独立的形式运行在完整的操作系统上，这听起来有些奇怪，将程序和依赖环境打包，最终不还是要运行在操作系统上?

是的，这听起来似乎很蠢，但这样做有一个好处：容器**不再依赖**特定的**外部环境**(这里是不依赖环境而非不依赖系统，windows系统还是无法直接运行linux程序)，只要容器内部环境安排好了，只要系统内核支持，放到任何机器上都可以直接运行而不用担心因为环境的不同导致的程序运行差异；

这里你应该能知道为何开发和运维人员需要掌握`docker/容器`技术了,因为它极大的便利了应用程序的部署和维护，将人们从繁琐的环境部署中解放出来，一次配置，处处运行。

另外一个好处就是，容器独立于当前的文件系统，它不会影响你对操作系统的正常使用，同时也让同名应用程序可以不受限制的同时运行；

至于为什么可以独立于当前的使用，这得益于Linux内核所提供的命名空间(以及控制组)特性，简单理解为一个全新的用户和你一起使用了这个操作系统(他甚至可以拥有与你相同的用户标识)，他和你共同享用系统的资源，同时使用独立的用户空间，和你以及你的文件并无直接交互。

这样解释应该比较容易理解容器概念了，而`docker`则是为管理容器而创造的。不过我们不需要去了解`docker`如何在容器和系统内核之间进行通信，这不在本文讨论范围，重要的是容器这个概念而非`docker`，很多人将`docker`等同于容器(实际上本文也在很多地方混用这个概念)，这是非常错误的，`docker`是管理容器的一系列技术的一种实现。

不可否认`docker`对容器技术的发展起到了巨大的作用，但这里我们必须阐明二者的绝对不同。实际上还有相当多的容器管理(编排)技术组，比如`podman`、`K8s`……
另外，在和谷歌`k8s`的皇城pk中,`docker`落得满盘皆输，早已不复往日霸主地位……
当然，如果仅做个人或者说入门使用，业界老前辈`docker`仍然是最好的选择，我们也以此为基础介绍相关技术，它足够简单，兼容性强，拥有图形化界面，同时拥有丰富的社区支持。

最后需要指出的是，上述的描述仍有不是那么严谨的部分，譬如Windows内核同样支持容器技术(早期docker依赖于Linux的lxc，但是后来逐步发展……)，不过它并不影响你对容器的认识和接下来对`docker`的使用，我们不希望因为一些太过繁琐的概念或是理论影响你对问题本质的理解；

或者说，从历史的发展顺序来了解、学习事物才是最符合人类认知逻辑的……点名批评苏联以及~

### 容器和虚拟化技术

如果你阅读过其他的教程或文章，就会发现作为另一种虚拟化技术，虚拟机通常会拿来同容器进行对比。

和容器不同，虚拟机模拟了完整的操作系统，同时包括硬件资源，所占据的系统资源相较于容器多了几个量级。当然虚拟机有其独有的应用范围，我们无意进行指摘。

实际上在Windows系统和MacOS系统上使用`docker`，就需要借助虚拟机(一般情况是这样，不过windows也支持windows容器，开启需要一定条件，不推荐个人使用，~~另外水果真吸血鬼~~)，这是因为容器技术依赖于linux内核的几个特性，这在前文稍有提及。

先由系统模拟出一个linux环境,然后再由`docker`程序在这个虚拟机上运行所指定的容器。不过不用担心虚拟机的问题，`docker`软件在安装时会自动处理关于虚拟机的问题。

在这里我们多提一下，容器作为成熟的虚拟化技术，并非一个新概念。实际上早在Unix系统诞生之时，先驱们就已经开始考虑隔离进程，使用`chroot`系统调用来分配虚拟文件系统；比较清晰的使用发布于FreeBSD，随后linux开始进行内核级的支持并逐渐发展，而后docker由其易用性将其彻底引爆，然后docker和谷歌干架，跌落神坛，最终形成了今天如此繁盛开放的容器技术。


## 安装 Docker desktop

`Docker Desktop` 是`docker`在windows下的图型管理工具，他能便捷的下载，控制和销毁容器；
提供丰富的操作选项,同时监控容器对各类系统资源的占用;

在Windows上安装 `Docker desktop` 是一件很容易的事情，安装程序会帮你妥善处理好依赖问题，如果需要更详细的安装指导或者错误诊断，请参考 [docker官方文档](https://docs.docker.com/desktop/install/windows-install/)、[windows官方文档](https://learn.microsoft.com/zh-cn/virtualization/windowscontainers/manage-docker/configure-docker-daemon#install-docker) 或是一个稍微有些 [老旧的中文指南](https://www.runoob.com/docker/windows-docker-install.html)

下面是我本机安装的过程

### 从官网安装

我们十分推荐你直接使用官网的安装程序来安装`Docker Desktop`，它会帮你处理好虚拟机等配置。

[官网下载指导页面](https://docs.docker.com/desktop/install/windows-install/)，直接下载exe安装就行，出错了再看它标注的信息；

注意`Docker Desktop`对系统版本有要求，具体限制在官网有详细标注，一般来说正常使用的Windows 10、11 都可以顺利安装，如果失败，请根据错误信息核对系统版本，考虑升级；

安装非常简单，和其他软件没有区别，它会帮你开启 `WSL` 并进行后续的操作；

![安装界面](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image.webp)


安装需要一定的时间...请耐心等待

![完成安装](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-1.webp)

安装完成后，docker会添加开机启动，并注册一些服务，如果不需要，请手动关闭(软件内也可以关闭)，当然如果是否禁止开机启动看个人需求~~，我一般不在Windows上用docker~~。

![禁止开机启动](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-2.webp)


### 启动Docker

安装之后如果没有自动启动，~~就手动；~~

![docker 状态栏图标](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-4.webp)


![docker 数据传输与隐私](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-3.webp)

接受该请求并回答一些调查问题，在询问是否登录时，可以选择不登录直接进入，然后进入 `Docker Desktop` 页面(我这里是卸载再安装的，配置文件没删完，一些提示我看不到了)，下面是最基本的 `Docker Desktop` 界面：


![docker desktop 主页面](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-5.webp)

大多数情况下，你只需要使用左边的 `Containers` `Images` 这两个按钮，偶尔会用到 `Volumes`。此外，我无意将本文发展成一篇过于琐碎的说明，所以关于其他部分便不在赘述；

### 设置镜像仓库

当然国内访问 `docker` 默认的服务器肯定会遇到网络问题，桌面软件的话我们可以设置镜像仓库

![更换默认镜像仓库](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-8.webp)

```json
# 注意用,分隔json的键值对以避免语法错误
{
  "registry-mirrors": [
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
  ]
}
```

设置完之后点击 `Apply & restart`；~~有时候它不生效，这是很蠢的事，请退出然后再打开~~……

## 使用 Docker Desktop

### Docker概念

在前面我们说过，`docker`是来管理容器的，所谓容器就是应用程序和它所依赖的环境的组合；

而实际应用中，`docker`对容器技术的实现由 动态容器、静态镜像和远程仓库 三者组成；

这三者也就是`docker`中最基本的概念 `Containers` `Images` 和 `Repository`

所以在软件左上角，我们可以看到

![左上角显示](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-20240319215430148.webp)

`Containers`，它代表了动态的容器；
`Images`，它代表了静态的镜像；
至于 `Repository` 也就是托管镜像的仓库，它在 `docker desktop` 被默认了(实际上已经在前面设置中被我们更换了，它默认使用 [docker hub](https://hub.docker.com/) )

所谓动态的容器，就是指运行着的程序与依赖环境(文件系统、环境变量等)，这些整体构成了一个容器。其实质是特殊的进程，容器内部是一个同宿主隔离的环境；`Containers` 后续我们称呼它为容器；

所谓静态的镜像，指的是生成容器的一个模板，它标注了目标容器所需要的环境、资源，是对这些内容的一个特殊打包；`Images` 后续我们称呼它为镜像；

存放镜像文件的地方，也是我们获取镜像的地方，就是仓库，这是一个非常容易理解的概念，`Repository` 后续我们称呼它为仓库，它通常是一个或几个中心服务器。

现在我们来梳理一下容器和镜像的关系：

镜像是一个标准，标注了某个容器的环境(文件系统、环境变量等)，其本身是静态的，当被创建之后便不再发生任何改变；可以被下载、删除、上传；(当然你也可以制作自己的镜像)

容器则是镜像的标准的实现，是实际运行的程序，在概念上是动态的，可以被创建、启动、暂停、销毁；

从镜像文件生成容器，就好像从模子里雕刻人一样，就好像面向对象里的类和实例一样；~~实际上这两个类比都不是那么的恰当2333，也许这里不需要类比；~~

### 运行nginx容器

在了解完概念之后，让我们实际体验一下。

点击左侧的 `Images`，可以看到大大的 *Search images to run*，提示我们可以搜索并下载需要的镜像；

![搜索镜像提示](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-6.webp)

![搜索框](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-7.webp)

这里我们选择下载它推荐的第一个 `nginx`(这是一个网络服务器，用于部署网页)

![搜索nginx镜像](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-9.webp)

速度有点迷~~~如果你搜索没有看到任何内容，且正确更换了镜像仓库，可以重新启动软件试试，，，~~

![浏览已安装的镜像](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-11.webp)

这样我们获得了 `nginx` 的镜像文件，可以看到他的一些信息，比如名字、Tag(版本)、使用状态，创建时间，文件大小，以及Action；现在我们通过这个镜像运行一个容器，在Actions里点击开始符号`Run`

![容器参数设置](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-12.webp)

我们需要给容器一些特殊的参数信息，只有名字是必须的，用来区分不同的容器；(不手动设置，就会分配一个随机的)

但是特殊的容器需要特殊的设置，比如 `nginx` 通过80端口展示页面，我们在 `Ports` 里需要给他指定一个端口，这个端口是我们外部的端口，我们从这个端口连通容器内部的80端口；如果你不知道端口号的含义，可以设置为 8081 端口(它通常用作web测试，前提是你的设备没有进程占用该端口)，如果有占用可以往后推 8082 ...

![随手打了个8888…](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-13.webp)

本地系统可能会有防火墙提醒，这是由于`docker`希望通过本机的端口通讯，请允许它；

![防火墙提醒](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-14.webp)

![Nginx 日志页面](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-15.webp)

然后该镜像就会运行出一个容器，名字为你设定的，其80端口被映射到本机8081端口，你可以通过 http://localhost:8081/ 来访问该容器的服务

![成功启动nginx容器](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-16.webp)

可以看到 `nginx` 服务已经正常启动；现在让我们开始查看该容器的信息·

![浏览已部署的容器](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-18.webp)

我们可以看到它的名字、镜像、状态、CPU占用、端口号映射、以及 Action；

Action 主要就是控制该容器，可以暂停、启动、删除(需先暂停容器)，还可以查看容器的各类信息

![Action 选项](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-19.webp)

如果你对计算机程序控制了解不多，可以不用在意容器里面的细节，优秀的镜像创建者应当为用户提供合适的控制接口，直接进入容器内部控制显然是不明智的(emmm)

### 一个更友好的例子

`nginx` 是非常强大的web服务器，但在这里并不是一个好的例子，因为展示网页需要一定的配置以及web文件；

对个人来说，使用 `docker` 更大的目的是为了一键部署自己想要的程序而不必了解其部署细节；所以我们现在用一个更加友好的，[alist](https://alist.nn.ci/)

![搜索alist镜像](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-20.webp)

~~不得不说，docker desktop的搜索和下载真是感人，我确定我的网络配置没问题，结果还是一会快一会慢~~

![alist和nginx镜像](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-21.webp)

这里我们直接使用随机配置，它会分配一个随机的name和随机的映射端口号

![使用随机配置](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-22.webp)

在 `Logs` 也就是日志信息里，我们看到服务已经启动，也看到了初始的admin和密码，我们通过它给的端口号进行本地访问 

![alist容器的logs页面](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-25.webp)

![本机访问5244端口](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-26.webp)

显然，`alist`接下来的配置就可以在web页面里进行了；而在 `Containers` 页面，我们可以任意控制容器的开关

![容器页面](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-27.webp)

### 一个不那么友好的例子

实际上，[docker hub](https://hub.docker.com/)上存储着数量惊人的镜像，因为镜像的制作十分简单，我们完全可以给自己的程序打包一个`docker`镜像，然后上传docker hub或是其他镜像仓库，以方便在任意地方拉取该镜像(上传不是必须的，只要有镜像文件我们就可以部署容器)；

所以你可以找到大量程序的docker镜像(毕竟系统都能打包)，而且不需要了解它是如何编译如何部署的，只要镜像的制作者足够贴心，你完全可以绕过这些细节，仅作简单的参数配置就可以轻松使用一些很难部署的项目；

而有时，你只是单纯需要一个外部的环境，来执行一些小脚本，但又不想使用服务器，那`docker`同样可以作为你的选择，只不过需要你自己去制作一下镜像，这并非一件困难的事情；

~~这里我列举一个不那么友好的例子，来展示一下：部署的是一个我写的 [telegram bot](https://github.com/var-luoluo/upload-bot)，它可以实现本地文件的上传任务，特别是当你的服务器受限于硬盘大小且没有意愿扩容的时候，也许本地部署容器是一个不错的选择。~~

Wait ...

## 通过命令行控制 docker

通过图形化的操作，我们似乎很容易部署一个容器，但是相比于命令行，图形化弱爆了。

图形操作有太多的局限：需要手动拉取镜像、手动设置参数，交互界面丑陋且毫无补全可用... 最重要的是，它没法借鉴他人的智慧，操作都是点点点.…..而使用命令操作`docker`，才能真正实现一键部署：把别人写好的文件copy一下，有时候都不用改，直接就能享受服务了。

在前面我们说过，不会涉及太多的命令，所以有关上述点点点操作的命令替代，比如

| 任务           | 命令                                         |
| -------------- | -------------------------------------------- |
| 拉取镜像       | docker pull nginx                            |
| 从镜像启动容器 | docker run --name nginx -p 80:80 -d Nginx    |
|                | -p 指定端口映射，前面的是本机的；-d 命名容器 |
| 停止镜像       | docker stop nginx                            |
| 。。。         | docker help                                  |

等等，这些都比较浅显易懂，且有图像操作与之对应，我们就不再展开了，请自行了解。但是，真正的懒人神器 `docker compose` 不容错过

### docker compose

`docker compose` 通过读取配置文件，来完成上述的拉取镜像、启动容器、设置参数等行为；

对任何服务，我们只需要 `docker compose up -d`(需要配置文件docker-compose.yml在当前目录下，-d的意思是后台运行，不会占据shell前台的io)就直接启动容器了，关闭也只需要 `docker compose down`

~~至于 `docker-compose.yml` 怎么写，本文不会涉及；~~

一般的，如果项目提供了docker镜像，它也会提供一份 compose 配置，复制粘贴然后 `docker compose up -d` 容器就启动了~

比如 `alist` 提供的(volumes需要说明一下，这是挂载本地文件到容器环境的，由此实现二者的文件共享，前面的是自己的文件目录，后面的是容器里的)

![alist compose file](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-28.webp)

万一没提供，热心的网友总会替你解决，比如这个

![中文搜索compose示例](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-29.webp)

![英文搜索更方便](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-30.webp)

最后，真没人写，那就自己动手吧，compose的配置相对来说不是那么难，如果不涉及多个程序交互的情况下~

### 有哪些好玩的项目

使用docker的一大便利就是，哪怕完全不懂也可以轻松部署大量优秀的开(闭)源项目，你要做的就是copy，稍微根据提示修改一下yml，然后 `docker compose up -d`

![alt text](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-31.webp)

我推荐使用这样的结构，为需要的容器新建一个文件夹，然后把compose文件一丢，该映射的文件夹新建一下，然后`docker compose up -d`，就可以了(我截图背后的这个文件看起来很复杂，但是都是环境变量而已，实际大部分的compose文件就几行)

比如 `calibre-web` 就这些，我只改了volumes文件映射，其他都是官方给的(虽然但是有点小坑，你最好自己准备一个calibre的数据库文件丢进去)

![alt text](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-32.webp)

但是它的效果，完美~

![alt text](https://cdn.jsdelivr.net/gh/var-kong/picx-images-hosting@master/blog/202403/docker/image-33.webp)

回归正题，有哪些~~好玩~~有价值的项目：

Wait ...
