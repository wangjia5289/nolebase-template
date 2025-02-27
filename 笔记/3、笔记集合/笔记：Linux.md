

![[Pasted image 20250107183918.png]]


# 配置yum 源

安装 `yum-utils` 工具
```
sudo yum install -y yum-utils

```
- **`yum-utils`** 是一组用于扩展和增强 `yum` 命令功能的工具集。`yum-config-manager` 就是 `yum-utils` 包中的一个工具。
- 安装 `yum-utils` 后，你可以使用更多高级功能，比如管理 Yum 仓库、查看和清理缓存等。

配置 Docker 的 Yum 源
```
sudo yum-config-manager \
--add-repo \
http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

```
- **`yum-config-manager --add-repo`**：这个命令的作用是添加一个新的 Yum 仓库源（repository）。在这里，它用来将阿里云的 Docker 官方 Yum 仓库地址添加到 CentOS 的 Yum 源配置中。
- **`http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo`**：这是阿里云提供的 Docker 仓库地址，包含了 Docker CE（Community Edition）的相关软件包。通过这个源，系统可以从阿里云的服务器上下载并安装 Docker。


Yum 源的配置文件通常位于 `/etc/yum.repos.d/` 目录下。你可以列出该目录中的所有文件来查看系统配置了哪些 Yum 源。
```
ls /etc/yum.repos.d/

```
该命令会列出 `/etc/yum.repos.d/` 目录下的所有 `.repo` 文件，这些文件包含了系统配置的各个 Yum 源。









