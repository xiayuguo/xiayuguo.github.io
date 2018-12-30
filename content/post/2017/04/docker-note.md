---
title: Docker学习笔记
date: 2017-04-23 23:17:56
tags:
- Docker
categories:
- Docker
---

## Docker简介
> [What is Docker](https://www.docker.com/what-docker)
Docker is the world’s leading software container platform. Developers use Docker to eliminate “works on my machine” problems when collaborating on code with co-workers. Operators use Docker to run and manage apps side-by-side in isolated containers to get better compute density. Enterprises use Docker to build agile software delivery pipelines to ship new features faster, more securely and with confidence for both Linux and Windows Server apps.

> Docker是世界领先的软件容器平台。开发人员使用Docker来消除与同事的代码协作时的“我机器上的工作”问题。运营商使用Docker在独立的容器中并行运行和管理应用程序，以获得更好的计算密度。企业使用Docker构建灵活的软件传送管道，可以更快，更安全地运行新功能，并且对于Linux和Windows Server应用程序都有信心。

### 什么是容器?
> [What is Container](https://www.docker.com/what-docker)
Using containers, everything required to make a piece of software run is packaged into isolated containers. Unlike VMs, containers do not bundle a full operating system - only libraries and settings required to make the software work are needed. This makes for efficient, lightweight, self-contained systems and guarantees that software will always run the same, regardless of where it’s deployed.

> 使用容器，使一件软件运行所需的一切都被打包成隔离的容器。与虚拟机不同，容器不捆绑完整的操作系统 - 只需要使软件工作所需的库和设置。这使得高效，轻便，自包含的系统，并保证软件将始终运行相同，无论它在哪里部署。

- Linux容器技术 vs 虚拟机
![](https://hugo-1256107396.cos.ap-chengdu.myqcloud.com/blog/201704/vmvsdocker.png)
