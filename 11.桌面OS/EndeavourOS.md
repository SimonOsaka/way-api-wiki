# EndeavourOS

## 官网

[EndeavourOS &#8211; A terminal-centric distro with a vibrant and friendly community at its core](https://endeavouros.com)

## 安装

选择`offline`安装，默认安装的桌面环境是`xfce`。

## 使用

### 修改/etc/pacman.d/mirrorlist

1. 注释或删除所有Server

2. 加入`Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch`

3. 更新软件包缓存`sudo pacman -Syy`

### 修改/etc/pacman.d/endeavouros-mirrorlist

注释或删除非`China`的`Server`

### 安装使用者AUR仓库工具paru

`sudo pacman -S paru`

### 使用paru

- 安装vscode `paru -S visual-studio-code-bin`

- 删除vscode `paru -R visual-studio-code-bin`

### 仓库地址

- 官方 [Arch Linux - Package Search](https://archlinux.org/packages)

- AUR [AUR (zh_CN) - 软件包 (archlinux.org)](https://aur.archlinux.org/packages)

### 安装字体

- CascadiaCode，[搜索cascadia](https://archlinux.org/packages/?q=cascadia)
  
  `sudo pacman -S ttf-cascadia-code`
  
  `sudo pacman -S otf-cascadia-code`
  
  `sudo pacman -S woff2-cascadia-code`


