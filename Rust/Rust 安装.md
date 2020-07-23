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



## Rust升级

#### rustup update更新慢

```shell
# 执行如下命令，该命令只是一段，全部内容去清华mirror的rust页面中查看
echo 'export RUSTUP_DIST_SERVER=https://mirrors.tuna.tsinghua.edu.cn/rustup' >> ~/.bash_profile
```

或者

```shell
vi ~/.bash_profile
export RUSTUP_DIST_SERVER=http://mirrors.rustcc.cn
export RUSTUP_UPDATE_ROOT=http://mirrors.rustcc.cn/rustup rustup install stable
```

`source ~/.bash_profile`再次执行更新即可。

## Rust交叉编译

开发在Mac，产品最后运行在Linux。两种环境编译的二进制包是无法在彼此环境上运行的。

#### macOS编译，linux运行

```shell
# 查看可用的目标环境
rustup target list

# 引入linux静态编译包
rustup target add x86_64-unknown-linux-musl

# 执行编译
cargo build --release --target=x86_64-unknown-linux-musl

Finished release [optimized] target(s) in 54.64s
######################################################
# 说明编译成功！！！
# 在target/x86_64-unknown-linux-musl/release目录内获得。
######################################################
```

#### 编译过程中发生错误

##### 提示：/bin/sh: musl-gcc: command not found

```shell
# 安装
brew install filosottile/musl-cross/musl-cross

# 编辑cargo
vi $HOME/.cargo/config
# 加入如下内容，保存
[target.x86_64-unknown-linux-musl]
linker = "x86_64-linux-musl-gcc"

# 执行目标linux平台的编译命令
CC_x86_64_unknown_linux_musl="x86_64-linux-musl-gcc" cargo build --release --target=x86_64-unknown-linux-musl
```

##### 提示：openssl err的错误

```shell
# 安装openssl 1.1
brew install openssl@1.1

# 修改环境变量
vi ~/.bash_profile
export OPENSSL_DIR="/usr/local/opt/openssl@1.1" # 根据实际目录填写，这是brew默认安装目录
# 保存退出

# 执行source完成
source ~/.bash_profile
```

参考：[https://www.qttc.net/529-rust-cross-compile-mac-to-linux.html](https://www.qttc.net/529-rust-cross-compile-mac-to-linux.html)

参考：[https://blog.csdn.net/u013195275/article/details/106070326/](https://blog.csdn.net/u013195275/article/details/106070326/)

