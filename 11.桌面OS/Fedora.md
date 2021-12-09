## 安装Fedora Xfce桌面

安装fedora xfce版本[下载 Fedora Xfce 桌面](https://spins.fedoraproject.org/xfce/download/index.html)

## 升级Fedora

```shell
sudo dnf upgrade
```

## 如何安装软件

- 多种安装方式
  
  - 通过系统仓库安装`sudo dnf install xxxxx`
  
  - 手动安装
    
    - **提供**RPM，下载后鼠标双击安装。或命令行`rpm -ivh 软件名.rpm`
    
    - **未提供**RPM，下载后将目录统一放到一处，如果未提供`xxxxx.desktop`，新创建一个`xxxxx.desktop`文件，最后放到`/usr/share/applications`内。文件内容参考如下（或`/usr/share/applications`任意文件内容）
      
      ```shell
      [Desktop Entry]
      Encoding=UTF-8
      Name=xxxxx
      Exec=/home/用户名/software/xxxxx/可执行文件名称
      Icon=/home/用户名/software/xxxxx/app/icons/icon_128x128.png
      Terminal=false
      Type=Application
      Categories=Development;
      ```
  
  - flathub.org安装软件
    
    - 系统已默认安装flatpak，先安装仓库`flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo`
    - 每个软件最下方都有安装命令
  
  - snapcraft.io安装软件
    
    - snap安装`sudo dnf install snapd`和`sudo ln -s /var/lib/snapd/snap /snap`
    - snap软件安装`sudo snap install xxxxx`
    - 在fedora35版本，applications没有xxxxx.desktop，导致无法从菜单启动和查找
  
  - AUR(archlinux)参考pkgbuild打包
    
    - 打开软件页面，右侧`view PKGBUILD`，看到aur打包内容。抽取必要信息，下载对应源包并制作。
    
    - 如果源包是snap，执行命令`curl -H 'X-Ubuntu-Series: 16' https://api.snapcraft.io/api/v1/snaps/details/<snap软件名>`获取详细json信息，复制`download_url`属性内容，下载源包。执行命令`unsquashfs -f -d <解包后的路径> <软件名>.snap`解包，完成后按照<u>*手动安装（未提供RPM）*</u>方法制作。

### VScode

Official [Running Visual Studio Code on Linux](https://code.visualstudio.com/docs/setup/linux#_rhel-fedora-and-centos-based-distributions)

### Mark Text

RPM [Releases · marktext/marktext (github.com)](https://github.com/marktext/marktext/releases)

### Postman

Official [Postman.com download](https://dl.pstmn.io/download/latest/linux64)

```shell
tar -xzvf xxxxx.tar.gz

mkdir /home/<username>/software

mv xxxxx /home/<username>/software
```

新建文件postman.desktop，内容如下

```shell
[Desktop Entry]
Encoding=UTF-8
Name=Postman
Exec=/home/<username>/software/xxxxx/Postman
Icon=/home/<username>/software/xxxxx/app/icons/icon_128x128.png
Terminal=false
Type=Application
Categories=Development;
```

保存！

```shell
sudo cp postman.desktop /usr/share/applications
```

点击`菜单`->`开发`->`Postman`启动。

### LibreOffice

Official [下载 LibreOffice | LibreOffice 简体中文官方网站 - 自由免费的办公套件](https://zh-cn.libreoffice.org/download/libreoffice/)

或者

```shell
sudo dnf install libreoffice
sudo dnf install libreoffice-langpack-zh-Hans # 中文语言包
```

### Nginx

```shell
sudo dnf install nginx

sudo -i

nginx
```

### Git图形客户端

[gitg—Linux Apps on Flathub](https://flathub.org/apps/details/org.gnome.gitg)

[Gittyup—Linux Apps on Flathub](https://flathub.org/apps/details/com.github.Murmele.Gittyup)

### Filezilla

```shell
sudo dnf install filezilla
```

### Switchhosts!

Official [oldj/SwitchHosts: Switch hosts quickly! (github.com)](https://github.com/oldj/SwitchHosts)

手动制作`switchhosts.desktop`和`icon_128x128.png`

### OmniDB

Official RPM [OmniDB - Open Source Collaborative Environment For Database Management](https://omnidb.org/)

### Termius

参考AUR [AUR (en) - termius (archlinux.org)](https://aur.archlinux.org/packages/termius/)

## 问题

1. Fedora35为什么没有声音？

[Fedora又一次哑了，又如何？ - 掘金 (juejin.cn)](https://juejin.cn/post/7026922180847861796)

```shell
# 执行命令，重启，声音正常
sudo dnf swap wireplumber pipewire-media-session
```
