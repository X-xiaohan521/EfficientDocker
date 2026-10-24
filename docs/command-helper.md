# Docker 命令复制版

## 安装 Docker Engine (Linux)
> 官方安装指南：https://docs.docker.com/engine/install/

```bash
sudo curl -fsSL https://get.docker.com | sudo sh

sudo usermod -aG docker $USER
sudo systemctl start docker
sudo systemctl enable docker

docker version
```

## 配置 docker pull 镜像

编辑：

```bash
sudo nano /etc/docker/daemon.json
```

加入：

```json
{
  "registry-mirrors": [
    "https://docker.1ms.run",
    "https://docker.xuanyuan.me"
  ]
}
```

然后重启：

```bash
sudo systemctl daemon-reexec
sudo systemctl restart docker
```

## 安装 Portainer

官网：https://www.portainer.io/

安装命令：

```bash
docker volume create portainer_data

sudo docker run -d \
  --name portainer \
  -p 9000:9000 \
  --restart unless-stopped \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:2.33.6
```

##  Docker Desktop (Linux Desktop & Windows & Mac)

> 官方安装包：https://docs.docker.com/get-started/get-docker/

## 安装 docker compose (For Docker Engine Only)

### Ubuntu / Debian

```bash
sudo apt-get update
sudo apt-get install docker-compose-plugin
```

### RPM-based distributions (Fedora, RHEL, CentOS)

```bash
sudo yum update
sudo yum install docker-compose-plugin
```

### Arch Linux

```bash
sudo pacman -S docker-compose
```

## 例子：一个 debian 容器的生命周期
### 1. 拉取 `debian:latest` 镜像

```bash
docker pull debian:latest
docker images
```

### 2. 拉起容器

```bash
docker run --name debian-test -i -t debian:latest /bin/bash
```

* `-t`: tty 分配一个伪终端
* `-i`: interactive 交互式（Keep STDIN open even if not attached）

### 3. 查看容器状态

```bash
docker ps -a
```

### 4. 启动/停止容器

```bash
docker start/stop debian-test
```

### 5. 进出 shell

```bash
docker attach debian-test
```

### 6. 穿透执行命令
> 在宿主机 shell 直接向容器内发送指令

```bash
docker exec debian-test ls -lah /
docker exec -it debian-test /bin/bash
```

### 7. 删除容器
> 容器文件系统内容将会丢失（卷除外）

```bash
docker rm debian-test
```

## 复杂 `docker run`

```bash
docker run -it -d \
  --name debian-test \
  -p 8080:8080 \
  -v ./:/mnt \
  debian:latest /bin/bash
```

* `-d`: detach 在后台运行
* `-p`: port 暴露端口（左外右内）
* `-v`: volume 挂载卷（左外右内）

> 所有 docker run 可用的参数可在 https://docs.docker.com/reference/cli/docker/container/run/ 找到。

## docker-compose
> 一个用于规范化容器创建的 docker 插件
> 配置 `compose.yml` / `docker-compose.yml`

官方文档：https://docs.docker.com/reference/compose-file/

加载当前目录下 `docker-compose.yml` 或 `compose.yml` 并启动为 stack：

```sh
docker compose up -d
```

## 构建镜像
<!-- 以 hello-web 项目为例 -->

### 方法一：Commit
<!-- > 不推荐，因为构建过程难以复现 -->

e.g. 将 `hello-web` 打包进镜像
```bash
docker start debian-test
docker attach debian-test
cp /mnt/Downloads/hello-web /srv
chmod 755 /srv/hello-web
exit
```
关闭容器后，打包镜像：

```bash
docker commit debian-test hello-web
docker images
```
> `commit` 相当于：**固化** topfs 的内容并叠加在原先的 image 上

可以从新镜像启动容器：

```bash
docker run -it -d --name hello-web -p 8080:8080 hello-web:latest /srv/hello-web
```

### 方法二：从 `Dockerfile` 构建 image

```bash
docker build /path/to/folder/that/contains/Dockerfile
```

## 向 Registry 上传 Image
### 1. 选择 Registry
> Docker Hub / GHCR / 阿里云镜像仓库
<!-- 关于自己搭建 Registry，如果有空放到番外篇讲 -->

### 2. 创建账号并登录

```bash
docker login -u <USERNAME> -p <PASSWORD> <REGISTRY>
```

### 3. 打 `tag` 并上传
> tag = registry/maintainer/repo:version

```bash
docker tag <Image ID> <Image Tag>
docker push <Image Tag>
```
