---
title: Docker
date: 2023/07/10
categories:
- [计算机科学, Linux学习]
tags: 虚拟化容器
cover: assets/pexels-02.jpg
---

# Docker

+++info 阅读提示
本文从“虚拟化 vs 容器”的直觉类比出发，介绍 Docker 的定位、优势、集装箱思想与基础架构（Client / Docker Host / Registry），最后整理常用命令速查。适合快速建立整体认识与日常查阅。
+++

> 将硬件资源比作开发商拥有的一块地：物理机像一栋别墅，虚拟机像一栋楼房，而容器更像在房间里做隔断——在保证隔离的同时最大化利用资源。

## 虚拟化

虚拟化（Virtualization）是一种资源管理技术：将计算机的各种实体资源（如服务器、网络、内存、存储等）进行抽象与转换后呈现出来，**打破实体结构间不可切割的障碍，使用户能够以更灵活的方式使用资源**。这些虚拟出来的资源不再受既有架设方式、地域或物理组态的限制。一般所指的虚拟化资源包括计算能力与数据存储。

## Docker

> Docker 是一个用于开发、交付与运行应用程序的开放平台。它使你可以将应用与基础设施分离，从而更快交付软件。

### 优点

使用 Docker，你可以像管理应用程序一样管理**基础设施（OS/依赖/运行环境）**。通过利用 Docker 的方式快速构建、测试与部署代码，可以显著减少“写完代码”到“线上可运行”之间的延迟。

容器提供了隔离性，因此能为各种测试提供很好的**沙盒环境**。并且容器具备“标准化”的特征，非常适合为服务创建构建块。常见应用场景包括：

- 加速本地开发与构建流程：开发人员可以构建、运行并分享容器；容器在开发环境构建后可轻松进入测试与生产；开发与运维职责更易做逻辑分离。
- 让独立服务在不同环境中得到一致的运行结果：对面向服务架构与微服务部署尤其有用。
- 用 Docker 创建隔离环境进行测试：例如使用 Jenkins CI 启动测试容器进行持续集成。
- 在本机先搭建复杂程序/架构并验证，再部署到生产环境。
- 构建多用户平台即服务（PaaS）基础设施。
- 为开发/测试提供轻量级的独立沙盒环境。
- 提供软件即服务（SaaS）应用，例如 Memcached 即服务。
- 高性能、超大规模宿主机部署（单机运行大量容器）。

> 沙盒（sandbox）：在计算机安全领域，沙盒是一种安全机制，为运行中的程序提供隔离环境，常用于运行来源不可信或可能具破坏性的程序进行实验。

### 集装箱思想

Docker 借鉴了标准**集装箱**的概念：标准集装箱将货物运往世界各地，而 Docker 将这一模型用于软件交付。不同点在于：集装箱运输货物，Docker 运输软件与应用程序。

和集装箱一样，Docker 在执行上述操作时**不关心容器里装的是什么**：可以是 Web 服务、数据库、应用服务器等；所有容器都以相同方式“装载”与运行。

Docker 也不关心容器要被“运到哪里”：可以在笔记本构建容器，上传到 Registry，再在物理机或虚拟机下载测试，最终部署到目标主机。容器易替换、可叠加、便于分发，并尽量通用。

### Docker 架构

Docker 常见三部分组成：Client、Docker Host、Registry。

#### Client（客户端）：用户操作 → 发送命令

- `docker build`
- `docker pull`
- `docker run`

#### Docker Host（Docker 引擎）：解析并执行客户端命令

主要涉及两类核心对象：

- Image（镜像）：由多层文件叠加而成，通常从镜像仓库拉取。
- Container（容器）：容器由镜像创建，运行时是镜像的一个实例。

#### Registry（镜像仓库）

用于存储、分发镜像，例如 Docker Hub 或私有镜像仓库。

## Docker 命令

- 运行容器：`docker run <镜像名>`

- 列出本地镜像：`docker images`（镜像名、版本、ID、创建时间、大小）

- 查找镜像：`docker search <镜像名>`

- 拉取镜像：`docker pull <镜像名>[:version]`

  > Docker Hub 包含官方镜像与其他公开镜像。受网络环境影响，国内拉取镜像可能较慢，可配置镜像加速器（镜像内容与官方一致，但下载更快）。

- 删除镜像

```bash
# 1) 删除一个镜像
docker rmi <镜像名|镜像ID>

# 2) 删除多个镜像
docker rmi <镜像名1|ID1> <镜像名2|ID2> ...

# 3) 删除所有镜像
docker rmi $(docker images -q)
```

* 创建容器

```bash
# docker run [options] image command [ARG...]

# 创建交互式容器（exit 容器停止）
docker run -it --name=c1 centos /bin/bash

# 创建守护式容器（exit 容器不停止）
docker run -d --name=c2 centos /bin/bash

# 常见 options：
# -i: 交互式
# -t: 分配 tty 终端
# -d: 后台运行（输出容器 id）
# --name: 指定容器名称
```

* 进入容器

```bash
# 方式一：attach（exit 会导致容器停止）
docker attach <容器名|容器ID>

# 方式二：exec（exit 不会导致容器停止）
docker exec -it <容器名|容器ID> /bin/bash
```

- 查看容器

```bash
docker ps        # 查看正在运行的容器
docker ps -a     # 查看运行过的容器（历史）
docker ps -l     # 最后一次运行的容器
```

- 启动 / 停止容器

```bash
docker start <容器名|容器ID>
docker stop  <容器名|容器ID>
```

- 获取容器 / 镜像元数据


{% raw %}
```bash
# 查看全部信息
docker inspect <容器名|容器ID|镜像名|镜像ID>

# 查看部分信息（示例：IP）
docker inspect -f='{{.NetworkSettings.IPAddress}}' <容器名|容器ID>
# -f 可用 --format 替代
{% endraw %}

* 删除容器

```bash
# 删除一个容器
docker rm <容器名|容器ID>

# 删除多个容器
docker rm <容器名1|ID1> <容器名2|ID2> ...

# 删除所有容器
docker rm $(docker ps -a -q)

# 注意：无法删除正在运行的容器
```

* 查看容器日志：`docker logs <容器名|容器ID>`

* 文件拷贝

```bash
# 拷贝到容器内
docker cp <本地文件或目录> <容器名|容器ID>:<容器路径>
# 例：docker cp 1.txt c2:/root

# 从容器拷贝到本地
docker cp <容器名|容器ID>:<容器路径> <本地路径>
# 例：docker cp c2:/root/2.txt /root
```

* 目录挂载

```bash
# 创建容器时将宿主机目录映射到容器目录：-v 宿主机目录:容器目录
docker run -id --name=c4 -v /opt/:/usr/local/myhtml centos
```

{% links %}

- site: "Docker 官方文档"
  owner: "Docker"
  url: "[https://docs.docker.com/](https://docs.docker.com/)"
  desc: "安装、概念、命令与最佳实践"
  image: "assets/pexels-10.jpg"
  color: "#e9546b"
- site: "Docker Hub"
  owner: "Docker"
  url: "[https://hub.docker.com/](https://hub.docker.com/)"
  desc: "官方与社区镜像仓库"
  image: "assets/pexels-05.jpg"
  color: "#4e7cff"
  {% endlinks %}
