## Rust 安装

#### 使用vscode

1. 安装rust插件
2. 安装rustup
3. 重启vscode，安装rust server

#### debug

安装`CodeLLDB`插件，如果有发生*Acquiring CodeLLDB platform package*，执行[离线下载](https://github.com/vadimcn/vscode-lldb/releases/)对应版本。在扩展里，选择`···`，选择`从VSIX安装`，选择下载好的`*.vsix`文件，安装重启。

#### 问题：rustup下载慢

```shell
# 执行如下两行在命令行中
export RUSTUP_DIST_SERVER="https://mirrors.ustc.edu.cn/rust-static"
export RUSTUP_UPDATE_ROOT="https://mirrors.ustc.edu.cn/rust-static/rustup"
```

修改后，继续执行安装。

