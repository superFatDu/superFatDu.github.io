---
title: Docker基础知识
date: 2020-10-12 21:35:16
tags: Docker
categories: 后端运维
comment: true
---

# Docker入门知识

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的容器中,然后发布到任何流行的Linux机器或Windows 机器上,也可以实现虚拟化,容器是完全使用沙箱机制,相互之间不会有任何接口。一个完整的Docker有以下几个部分组成：DockerClient客户端；Docker Daemon守护进程；Docker Image镜像；DockerContainer容器。

## 1.Docker vs 虚拟机

> 服务器虚拟化解决的核心问题是资源调配，而容器解决的核心问题是应用开发、测试和部署。

1. 首先需要明确一点，**Docker容器不是虚拟机**。
2. 虚拟机与Docker框架，直观上来讲虚拟机多了一层Guest OS，同时**Hypervisor会对硬件资源进行虚拟化，Docker直接使用硬件资源**，所以资源利用率相对Docker低也是比较容易理解的。
3. openstack能够以10台/min的速度创建虚拟机，在Docker面前就弱爆了，因为Docker是利用宿主机的系统内核，所以可以做到在几秒钟之内创建大量容器，它们的启动速度是在数量级上的差距。

![](https://user-images.githubusercontent.com/13176622/98439574-b773da00-212d-11eb-8156-9864427c838a.png)

## 2.Docker主要特性

1. 文件系统隔离：每个进程容器运行在完全独立的根文件系统里。
2. 资源隔离：可以使用cgroup为每个进程容器分配不同的系统资源，例如CPU和内存。
3. 网络隔离：每个进程容器运行在自己的网络命名空间里，拥有自己的虚拟接口和IP地址。
4. 写时复制：采用写时复制方式创建根文件系统，这让部署变得极其快捷，并且节省内存和硬盘空间。
5. 日志记录：Docker将会收集和记录每个进程容器的标准流（stdout/stderr/stdin），用于实时检索或批量检索。
6. 变更管理：容器文件系统的变更可以提交到新的映像中，并可重复使用以创建更多的容器。无需使用模板或手动配置。
7. 交互式Shell：Docker可以分配一个虚拟终端并关联到任何容器的标准输入上，例如运行一个一次性交互shell。

## 3.Docker安装（Centos）

```bash
// 1.删除牢记版本（可以跳过）
$ sudo yum remove docker

// 2.安装必须的依赖
$ sudo yum install -y yum-utils

// 3.添加stable的Docker-ce源
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

// 4.安装Docker-ce
$ sudo yum install docker-ce docker-ce-cli dontainerd.io

// 5.操作Docker
$ systemctl start/stop/restart docker => 启动/停止/重启
$ systemctl status docker => Docker状态
```

## 3.Docker常用命令

![](https://user-images.githubusercontent.com/13176622/98439585-d07c8b00-212d-11eb-8661-902bf56f208b.png)

- Docker环境信息 — docker [info|version]
- 容器生命周期管理 — docker [create|exec|run|start|stop|restart|kill|rm|pause|unpause]
- 容器操作运维 — docker [ps|inspect|top|attach|wait|export|port|rename|stat]
- 容器rootfs命令 — docker [commit|cp|diff]
- 镜像仓库 — docker [login|pull|push|search]
- 本地镜像管理 — docker [build|images|rmi|tag|save|import|load]
- 容器资源管理 — docker [volume|network]
- 系统日志信息 — docker [events|history|logs]

## 4.Docker运行nginx示例

```bash
// 1.拉取nginx镜像
$ docker pull nginx

// 2.启动nginx
$ docker run --name my-nginx -p 80:80 -d nginx

// 3.配置nginx
$ docker exec -it my-nginx bash

// 4.进入到nginx目录下
$ cd /etc/nginx

// 5.安装vim
$ apt-get update
$ apt-get install vim

// 6.编辑nginx.conf
$ vim nginx.conf

// 7.暂停容器
$ docker stop nginx

// 8.重启
$ docker start nginx
```
## 5.注意事项

### 5.1修改nginx配置文件时遇到了403的问题

```bash
// 1.nginx.conf中user nobody
修改例如：
user root;

// 2.根目录 location / {}时，
// root /usr/share/nginx/html可以用，
// 但是修改为 alias /var/www/test报403
// 注意最后的斜杠（/）
alias /var/www/test/

// 3.二级域名访问时 lacation /test {}时，
// 不加斜杠是ok的

alias /var/www/test
try_files $uri $uri/ /test/index.html
```
