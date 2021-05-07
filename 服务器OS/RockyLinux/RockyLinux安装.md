## 安装

- 官方网站[Rocky Linux](https://rockylinux.org/)
- 进入下载页面[Rocky Linux Download](https://rockylinux.org/download)，下载最小安装版本`Minimal`

- 启动加载ISO文件
- 安装过程略，按照提示做。在网络界面，打开可以连接网络的开关`OFF`->`ON`，否则无法上网。
- 安装完成，成功登录系统。执行`ping www.baidu.com`如果无法ping通，进入`/etc/sysconfig/network-scripts/`，修改对应的网卡`ifcfg-网卡名称`，将`onboot`改为`yes`

- 安装软件命令`sudo dnf install xxx`，不再使用`yum`



如果RockyLinux不知如何使用，搜索参考CentOS 8，基本一模一样。

