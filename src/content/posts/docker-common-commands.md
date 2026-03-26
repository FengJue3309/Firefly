---
title: Docker常用命令
description: 本文整理了Docker日常开发与运维中最高频使用的命令清单，涵盖镜像管理、容器生命周期控制、数据卷操作、网络配置及系统清理等核心模块。通过结构化的分类与实例演示，助你快速掌握容器化操作精髓，提升云原生环境下的工作效率。
published: 2026-03-27
tags: [Docker, Linux, 容器技术, 运维]
category: Docker
lang: zh-CN
image: /images/Docker_150x150px-01-01-01.png
---

# 安装 Docker

```bash
yum update #更新 yum 版本

yum install -y yum-utils device-mapper-persistent-data lvm2 # 安装 docker 依赖包

yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo #设置 yum 源为阿里云的镜像源

yum install docker-ce #安装 docker

docker -v #查看 docker 版本
```

# 设置 Docker 镜像源

编辑 `/etc/docker/daemon.json` 文件加入下面内容：

```json
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```

然后重启 Docker 服务使配置生效。

# Docker 服务命令

```bash
systemctl start docker    # 启动 Docker
systemctl stop docker     # 停止 Docker
systemctl restart docker  # 重启 Docker
systemctl status docker   # 查看 Docker 状态
systemctl enable docker   # 设置开机自启
docker info              # 查看 Docker 详细信息
docker --help           # 查看帮助信息
```

# 镜像相关命令

```bash
docker images                    # 查看本地镜像
docker search 镜像名称           # 搜索镜像
docker pull 镜像名称             # 拉取镜像
docker rmi 镜像ID               # 删除指定镜像
docker rmi `docker images -q`   # 删除所有镜像
```

> **镜像信息说明：**
> - **REPOSITORY**：镜像名称
> - **TAG**：镜像标签
> - **IMAGE ID**：镜像 ID
> - **CREATED**：镜像的创建日期（不是获取该镜像的日期）
> - **SIZE**：镜像大小
>
> 这些镜像都是存储在 Docker 宿主机的 `/var/lib/docker` 目录下
>
> **搜索镜像说明：**
> - **NAME**：仓库名称
> - **DESCRIPTION**：镜像描述
> - **STARS**：用户评价，反应一个镜像的受欢迎程度
> - **OFFICIAL**：是否官方
> - **AUTOMATED**：自动构建，表示该镜像由 Docker Hub 自动构建流程创建的

# 容器相关命令

## 1. 查看容器

```bash
docker ps                 # 查看正在运行的容器
docker ps -a              # 查看所有容器（包括已停止）
docker ps -l              # 查看最后一次运行的容器
docker ps -f status=exited # 查看已停止的容器
```

## 2. 创建与启动容器

### 创建容器常用参数说明

| 参数 | 说明 |
|------|------|
| `-i` | 表示运行容器 |
| `-t` | 容器启动后会进入其命令行，分配一个伪终端 |
| `--name` | 为创建的容器命名 |
| `-v` | 目录映射关系（前者是宿主机目录，后者是映射到宿主机上的目录） |
| `-d` | 守护式容器，在后台运行 |
| `-p` | 端口映射（宿主机端口:容器端口） |

### 交互式方式创建容器

```bash
docker run -it --name=容器名称 镜像名称:标签 /bin/bash
```

> `/bin/bash` 为加载命令，这时我们通过 `ps` 命令查看，可以看到启动的容器，状态为启动状态

**退出当前容器：**

```bash
exit
```

### 守护式方式创建容器（常用）

```bash
docker run -di --name=容器名称 镜像名称:标签
```

**登录守护式容器方式：**

```bash
docker exec -it 容器名称(或者容器ID) /bin/bash
```

## 3. 停止与启动容器

```bash
docker stop 容器名称（或者容器ID）  # 停止容器
docker start 容器名称（或者容器ID）  # 启动容器
docker restart 容器名称（或者容器ID） # 重启容器
```

## 4. 文件拷贝

```bash
# 将文件拷贝到容器内
docker cp 需要拷贝的文件或目录 容器名称:容器目录

# 将文件从容器内拷贝出来
docker cp 容器名称:容器目录 需要拷贝的文件或目录
```

## 5. 目录挂载

我们可以在创建容器的时候，将宿主机的目录与容器内的目录进行映射，这样我们就可以通过修改宿主机某个目录的文件从而影响容器。

```bash
docker run -di -v /usr/local/myhtml:/usr/local/myhtml --name=mycentos3 centos:7
```

> 如果你共享的是多级的目录，可能会出现权限不足的提示。这是因为 CentOS7 中的安全模块 selinux 把权限禁掉了，我们需要添加参数 `--privileged=true` 来解决挂载的目录没有权限的问题

```bash
docker run -di --privileged=true -v /usr/local/myhtml:/usr/local/myhtml --name=mycentos3 centos:7
```

## 6. 查看容器 IP 地址

```bash
# 查看容器运行的各种数据
docker inspect 容器名称（容器ID）

# 直接输出 IP 地址
docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器名称（容器ID）
```

## 7. 删除容器

```bash
docker rm 容器名称（容器ID）  # 删除已停止的容器
docker rm -f 容器名称（容器ID） # 强制删除运行中的容器
```

# 应用部署

这里以 MySQL 为例说明如何通过 Docker 部署应用。

## 1. 拉取 MySQL 镜像

```bash
docker pull centos/mysql-57-centos7
```

## 2. 创建容器

```bash
docker run -di --name=tensquare_mysql -p 33306:3306 -e MYSQL_ROOT_PASSWORD=123456 centos/mysql-57-centos7
```

> `-p` 代表端口映射，格式为 `宿主机映射端口:容器运行端口`
> `-e` 代表添加环境变量，`MYSQL_ROOT_PASSWORD` 是 root 用户的登录密码

## 3. 远程登录 MySQL

连接宿主机的 IP，指定端口为 `33306`。

# 迁移与备份

## 容器保存为镜像

我们可以通过以下命令将容器保存为镜像：

```bash
docker commit mynginx mynginx_i
```

## 镜像备份

我们可以通过以下命令将镜像保存为 tar 文件：

```bash
docker save -o mynginx.tar mynginx_i
```

## 镜像恢复与迁移

首先删除掉 `mynginx_i` 镜像，然后执行此命令进行恢复：

```bash
docker load -i mynginx.tar
```

> `-i` 表示输入的文件

执行后再次查看镜像，可以看到镜像已经恢复。

# Dockerfile

Dockerfile 是由一系列命令和参数构成的脚本，这些命令应用于基础镜像并最终创建一个新的镜像。

| 命令 | 作用 |
|------|------|
| `FROM image_name` | 定义了使用哪个基础镜像启动构建流程 |
| `MAINTAINER user_name` | 声明镜像的创建者 |
| `ENV key value` | 设置环境变量（可以写多条） |
| `RUN command` | 是 Dockerfile 的核心部分（可以写多条） |
| `ADD source_dir/file dest_dir/file` | 将宿主机的文件复制到容器内，如果是一个压缩文件，将会在复制后自动解压 |
| `COPY source_dir/file dest_dir/file` | 和 ADD 相似，但是如果有压缩文件并不能解压 |
| `WORKDIR path_dir` | 设置工作目录 |

## 使用脚本创建镜像

**步骤：**

1. 创建目录

```bash
mkdir -p /usr/local/dockerjdk8
```

2. 下载 `jdk-8u171-linux-x64.tar.gz` 并上传到服务器中的 `/usr/local/dockerjdk8` 目录

3. 创建文件 `Dockerfile`

```dockerfile
# 依赖镜像名称和 ID
FROM centos:7
# 指定镜像创建者信息
MAINTAINER ITCAST
# 切换工作目录
WORKDIR /usr
RUN mkdir /usr/local/java
# ADD 是相对路径 jar,把 Java 添加到容器中
ADD jdk-8u171-linux-x64.tar.gz /usr/local/java/
# 配置 Java 环境变量
ENV JAVA_HOME /usr/local/java/jdk1.8.0_171
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib:$CLASSPATH
ENV PATH $JAVA_HOME/bin:$PATH
```

4. 执行命令构建镜像

```bash
docker build -t='jdk1.8' .
```

> 注意：后边的空格和点，不要省略

5. 查看镜像是否建立完成

```bash
docker images
```
