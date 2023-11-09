## archlinux做服务器
archlinux能不能作为服务器，装了再说

### 安装之前
1. 下载`archinstall`
2. 执行
```
archinstall
```
会有安装界面，提供安装之前的配置
*提示：最好创建一个superuser用户，方便为makepkg使用*

### 安装之后
1. 安装`paru`为了使用`aur`
2. 命令
```shell
sudo pacman -S --needed base-devel
pacman -S git
pacman -S rust

# archlinux不能在root用户执行makepkg
su <superuser's name>

git clone https://aur.archlinux.org/paru.git
cd paru

makepkg -si
```
3. 安装`sshd`
```shell
paru -S openssh
paru -S vim

vim /etc/ssh/sshd_config
#修改permitRootLogin yes

systemctl restart sshd.service
systemctl enable sshd.service
```
*如果正在用virtualbox，将网络NAT进行端口转发，主机127.0.0.1端口2222，子系统10.0.x.xx（改成archlinux的ip）端口22。使用ssh工具进行连接127.0.0.1:2222即可访问。*