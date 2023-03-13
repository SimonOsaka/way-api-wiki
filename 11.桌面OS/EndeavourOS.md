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

### 安装JDK

```shell
> paru -S amazon-corretto-17

> archlinux-java status
Available Java environments: 
    java-17-amazon-corretto
No Java environment set as default

> sudo archlinux-java set java-17-amazon-corretto
> archlinux-java status
Available Java environments: 
    java-17-amazon-corretto (default)

> java -version
Openjdk version "17.0.3" 2022-04-19 LTS
OpenJDK Runtime Environment Corretto-17.0.3.6.1  (build 17.0.3+6-LTS)
OpenJDK 64-Bit Server VM Corretto-17.0.3.6.1  (build 17.0.3+6-LTS, mixed mode, sharing)
```

可以安装多个jdk，然后进行切换。

### 安装xfce dock
点击链接 [xfce4-docklike-plugin](https://aur.archlinux.org/packages/xfce4-docklike-plugin)
```shell
# 安装
sudo pacman -S xfce4-docklike-plugin
```
安装完成后，如何使用
1. 右键任务栏 -> 面板 -> 面板首选项
2. 点击`+`，新建一个面板
3. 切换至新建的面板，点击`项目`tab，点击`添加`
4. 搜索`dock`，然后点击搜索结果`Docklike Taskbar`，点击添加
5. 将新建的面板拖到最底部，然后锁定
6. 打开一个应用，dock栏会出现图标，如果想一直使用，右键图标，点击`pinned to Dock`
7. 配置`Docklike Taskbar`，在第四步已经添加的项目`Docklike Taskbar`上双击即可出现配置选项。
8. 如果不想在任务栏和dock栏同时出现应用图标，右键任务栏 -> 面板 -> 面板首选项 -> `项目`tab，移除`窗口按钮`，添加`分隔符`并设置透明

### 使用paru替换yay

```shell
sudo nano /etc/eos-script-lib-yad.conf

# 修改EOS_AUR_HELPER="paru"
```



## 常见问题
### 1. “签名是勉强信任的”，”无效或已损坏的软件包 (PGP 签名)“，”签名是未知信任的“
**解决方案**：[[FAQ] Issues with “signature is marginal trust”, “signature is unknown trust”, or “invalid or corrupted package”](https://forum.endeavouros.com/t/faq-issues-with-signature-is-marginal-trust-signature-is-unknown-trust-or-invalid-or-corrupted-package/6756)