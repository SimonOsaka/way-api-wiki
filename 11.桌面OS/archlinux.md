## archlinux

### 安装前
* 官方网站下载ISO
* 准备用虚拟机（virtualbox/vmware）或者usb（rufus）安装

### 安装
1. 启动ISO
2. 安装选项第一项
3. 修改`mirrorlist`，增加一个距离近的mirror
```shell
rm -rf /etc/pacman.d/mirrorlist
vim /etc/pacman.d/mirrorlist
:wq
```
3. 执行`archinstall`
4. `archinstall`提供安装选项，根据选项配置要安装系统参数，也提供安装桌面环境的选项（KDE\Xfce\...）
5. 一切配置完成后，执行`install`

### 安装后
1. 进入系统，安装`yay` `paru` ...