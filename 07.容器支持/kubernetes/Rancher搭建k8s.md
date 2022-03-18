> rancher安装k8s只需要2个机器，一个安装rancher，一个安装k8s

## Rancher安装

1. `docker-compose.yml`

```yaml
version: "3"
services:
  rancher:
    image: rancher/rancher:v2.5.8-patch1
    container_name: rancher
    restart: unless-stopped
    privileged: true
    ports:
      - 80:80
      - 443:443
    volumes:
      - /data/rancher:/var/lib/rancher
```

2. `docker-compose up -d`
3. 访问url`https://主机ip`
4. 几个初始化设置

## k8s安装

接上

5. 点击`添加集群`，`自定义`，名称自拟，其余默认，勾选☑️`Etcd`\☑️`Control Plane`\☑️`Worker`，复制安装命令

6. 在另外一个虚拟机中执行安装命令。

## 执行k8s安装的虚拟机配置

* 关闭 selinux 
  
  `sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config`

* 关闭防火墙
  
  `systemctl stop firewalld.service && systemctl disable firewalld.service`

* 关闭swap
  
  `sed -i '/swap/s/^/#/' /etc/fstab`

* 时区
  
  `ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime`

* 语言
  
  `sudo echo 'LANG="en_US.UTF-8"' >> /etc/profile;source /etc/profile`

* 性能优化
  
  ```shell
  cat >> /etc/sysctl.conf<<EOF
  net.ipv4.ip_forward = 1
  net.ipv4.conf.all.forwarding = 1
  net.ipv4.neigh.default.gc_thresh1 = 4096
  net.ipv4.neigh.default.gc_thresh2 = 6144
  net.ipv4.neigh.default.gc_thresh3 = 8192
  net.ipv4.neigh.default.gc_interval=60
  net.ipv4.neigh.default.gc_stale_time=120
  EOF
  sudo sysctl -p
  ```

* 文件打开数
  
  ```shell
  cat >> /etc/security/limits.conf <<EOF
  * soft nofile 65535
  * hard nofile 65536
  EOF
  ```

> 以上命令根据操作系统不同而不同

关闭selinux和防火墙后，部署的docker环境端口就可以访问，否则不行。

如果不关闭这两个功能，估计可能走防火墙配置，如果防火墙不开放端口，可能就无法访问容器映射端口（纯属猜测，没有验证）。
