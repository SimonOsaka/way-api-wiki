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

### 安装

旧（废弃但可用）：

```shell
# 先安装python3
sudo dnf install python3

pip3 install podman-compose

# podman-compose需要手动创建volume
podman volume create xxxxx

# 执行podman-compose
podman-compose -f docker-compose.yml up -d 
```

新：

```shell
sudo dnf install epel-release

sudo dnf update

sudo dnf install podman-compose

podman-compose version
```

### 使用

官方docker-compose.yaml例子，假如文件存放目录`/app/docker/busybox`

```yaml
version: "3"
services:
  redis:
    image: redis:alpine
      ports:
        - "6379"
      environment:
        - SECRET_KEY=aabbcc
        - ENV_IS_SET

  frontend:
    image: busybox
    command: ["/bin/busybox", "httpd", "-f", "-p", "8080"]
    working_dir: /
    environment:
      SECRET_KEY2: aabbcc
      ENV_IS_SET2:
    ports:
      - "8080"
    links:
      - redis:myredis
    labels:
      my.label: my_value
```

执行命令

```shell
# 创建并启动
# 如果没有指定-p 工程名，默认使用yaml目录的名称busybox来创建pod
podman-compose up -d

# 显示容器状态
podman-compose ps

# 停止并删除
podman-compose down

# 停止
podman-compose stop frontend redis

# 启动
podman-compose start frontend redis
```

* 如果要使用卷，那么命令`podman volume create xxx`是**没有用的**，`podman-compose`会自动创建一个以`project_name + _ + source_vol`为名称的卷。例如跟随上面的例子：
  * 假定`podman-compose -p my_project up -d`，结果生成`my_project_myvol`。
  * 假定`- myvol:/etc/xxxxx`，busybox为yaml目录，结果生成`busybox_myvol`。
  * 假定`container_name: mybusybox`，结果生成`mybusybox_myvol`。
* 如果不使用卷，那么加入:z `source:target:z`

### 注意

- 如果podman-compose执行出错，安装开发版devel.tar.gz
- ~~如果开发版也执行出错，那么可能是linux系统少安装一些软件（试过完全安装linux后，执行命令正常执行），没有详细的提示，不知道少什么。~~
- 毕竟是开源软件，要有足够的心理准备。
- `podman-compose`没有`docker-compose`那么完善和一致，导致的结果就是：命令大部分相同，剩余命令需要补全，执行逻辑有些不同，运行没问题。

## 参考文献

1. [RedHat/CentOS8[Podman]安装和配置 - 简书 (jianshu.com)](https://www.jianshu.com/p/d69017fac5dc)

