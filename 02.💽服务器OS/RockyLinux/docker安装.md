## docker安装
### 配置docker源
安装yum-config-manager
```shell
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

官方源
```shell
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
或者
```shell
# 阿里云
sudo yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
```shell
# 清华源
sudo yum-config-manager \
    --add-repo \
    https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/centos/docker-ce.repo
```

### 安装docker
```shell
sudo yum install docker-ce docker-ce-cli containerd.io
```

启动docker
```shell
sudo systemctl start docker
```

开机启动docker
```shell
sudo systemctl enable docker
```

检测安装是否成功
```shell
sudo docker run hello-world
```

### 卸载docker
```shell
# 删除安装包：
yum remove docker-ce
```
```shell
# 删除镜像、容器、配置文件等内容：
rm -rf /var/lib/docker
```

参考
- [CentOS Docker 安装](https://www.runoob.com/docker/centos-docker-install.html)