## podman

### Github仓库

https://github.com/containers/podman

### 配置镜像源

/etc/containers/registries.conf

```shell
unqualified-search-registries = ["docker.io", "k8s.gcr.io"]

[[registry]]
prefix = "docker.io"
location = "fo8v7zw4.mirror.aliyuncs.com"

[[registry]]
prefix = "k8s.gcr.io"
location = "registry.aliyuncs.com/google_containers"
```

## podman-compose

### Github仓库

https://github.com/containers/podman-compose

```shell
# 先安装python3
sudo dnf install python3

pip3 install podman-compose

# podman-compose需要手动创建volume
podman volume create xxxxx

# 执行podman-compose
podman-compose -f docker-compose.yml up -d 
```

### 注意

- 如果podman-compose执行出错，安装开发版devel.tar.gz
- ~~如果开发版也执行出错，那么可能是linux系统少安装一些软件（试过完全安装linux后，执行命令正常执行），没有详细的提示，不知道少什么。~~
- 毕竟是开源软件，要有足够的心理准备。