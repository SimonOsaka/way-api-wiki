## 安装Fedora Xfce桌面

安装fedora xfce版本[下载 Fedora Xfce 桌面](https://spins.fedoraproject.org/xfce/download/index.html)

## 升级Fedora

```shell
sudo dnf upgrade
```

## 安装软件

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
