# 一、理论+实操


### 1、Docker 概述

Docker 是一种**容器化技术**，能够**跨平台**的**加速**项目的**构建**、**分享**、**运行**。

Docker 致力于以下三个方面：
1. **解决“运行环境不一致”问题**
	- 应用开发中常见一句话：**“在我电脑上没问题！”**。这是因为不同环境下（操作系统、库版本）可能导致程序无法正常运行。而 Docker 的容器包含了应用运行的全部环境，保证在开发、测试、生产等环境中都一致。
2. **轻量、快速启动**
	- Docker 容器很轻，占用资源少，启动速度快。和传统的虚拟机相比，它更高效。
3. **方便部署和迁移**
	- 通过 Docker，你可以把应用快速部署到任何支持 Docker 的服务器或云平台上，而无需关心底层环境的差异。
---



### 2、Docker Engine

#### 2.1、认识 Docker Engine

Docker 引擎是运行和管理 Docker 容器的核心程序，它包括一个守护进程（Daemon），负责构建、运行容器，并提供与用户交互的命令行工具（CLI）。

安装 Docker 就是安装的 Docker Engine。


#### 2.2、安装 Docker Engine

##### 2.2.1、安装方法

我们有多种方法安装 Docker：
1. **手动安装**：下载 RPM 包，手动安装
2. **存储库安装**：可以设置 Docker 的存储库并从中安装，此方法还方便升级任务，是推荐的安装方法
3. **自动化脚本安装**：还可以使用自动化编写脚本进行安装
详细方法请查看 [Docker Engine 安装文档](https://docs.docker.com/engine/install/centos/) ，在使用存储库安装时，由于我们通常通过国内的 Yum 源进行存储库安装，因此提供的安装方法与官方文档有所不同。


##### 2.2.2、存储库安装
```
# 1. 移除旧版本 Docker
sudo yum remove -y docker \
docker-client \
docker-client-latest \
docker-common \
docker-latest \
docker-latest-logrotate \
docker-logrotate \
docker-engine

# 2. 配置 Docker Yum 源
sudo yum install -y yum-utils
sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 3. 安装最新版本的 Docker
sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 4. 检查 Docker
docker --version
或
docker info

# 5. 启动 Docker 并设置开机自启
systemctl enable --now docker
```


#### 2.3、配置 Docker Engine

Docker 1.10.0之后的版本，可以用以下方式为 Docker 配置国内镜像加速器，以提高 Docker 镜像的拉取速度。
```
# 1. 创建 Docker 配置目录（如果不存在）
sudo mkdir -p /etc/docker

# 2. 配置 Docker 镜像加速器
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://dh-mirror.gitverse.ru"]
}
EOF

# 3. 重新加载 Docker 配置并重启服务
sudo systemctl daemon-reload
sudo systemctl restart docker

# 4. 检查是否配置成功
docker info
```

> [!NOTE] 加速器
> 加速器的工作原理是：当你从 Docker Hub 拉取镜像时，加速器会自动将你请求的镜像（如果它之前没有缓存）从 Docker Hub 拉取到加速器服务器，并且在加速器缓存中存储该镜像。这样，其他使用该加速器的用户会更快地下载到相同的镜像。
> 常见镜像加速器：
> 1. 腾讯云：https://mirror.ccs.tencentyun.com/    （加速器已变动，无法再使用）
> 2. 阿里云：https://ckzj9wzs.mirror.aliyuncs.com  （加速器已变动，无法再使用）
> 3. GitVerse：https://dh-mirror.gitverse.ru    （慢）
> 4. DaoCloud：https://docker.m.daocloud.io    （快）
> 5. 社区提供：
> 	1. https://hub.rat.dev/    （慢）
> 	2. https://docker.1panel.live/    （慢）
>
> 阿里加速配置文档：[阿里加速配置文档](https://help.aliyun.com/zh/acr/user-guide/accelerate-the-pulls-of-docker-official-images)
> GiteVerse 加速配置文档：[GiteVerse 加速配置文档](https://gitverse.ru/docs/artifactory/registry-mirrors/dh-mirror/)

> [!NOTE] 注意事项
> 1. 您可能会发现，许多加速器已发生变动，无法继续使用。这通常是由于运营商网络等不稳定因素导致镜像加速器无法成功拉取指定版本的容器镜像。因此，建议在生产环境中谨慎依赖 Docker Hub 提供的容器镜像。为了确保稳定性和安全性，阿里云推荐使用其[制品中心](https://help.aliyun.com/zh/acr/user-guide/artifact-center)提供的官方支持的容器基础镜像。
> 2. 配置加速器后，能加速 pull，但是仍旧无法进行 login


#### 2.4、Docker Engine 相关命令
```
# Docker 版本
docker --version

# Docker 详细信息，包括容器数、镜像数、运行状态等。
docker info
```



























