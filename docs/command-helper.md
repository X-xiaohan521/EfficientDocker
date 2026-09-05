# Docker 命令复制版

## 安装 Docker

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
    "https://docker.xuanyuan.me",
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

