## Rust 安装

```shell
curl --proto '=https' --tlsv1.2 -sSf https://rsproxy.cn/rustup-init.sh | sh
```

#### rustup国内源

```shell
# 执行如下两行在命令行中或者追加到~/.bashrc文件中，再source
export RUSTUP_DIST_SERVER="https://rsproxy.cn"
export RUSTUP_UPDATE_ROOT="https://rsproxy.cn/rustup"
```

### Cargo国内源

- 清华tuna源
- 中科大ustc源
- Rsproxy源

```shell
# 将如下内容复制粘贴到~/.cargo/config
[source.crates-io]
replace-with = 'rsproxy'

[source.ustc]
registry = "git://mirrors.ustc.edu.cn/crates.io-index"

[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"

[source.rsproxy]
registry = "https://rsproxy.cn/crates.io-index"

[net]
git-fetch-with-cli = true
```

<mark>提示</mark>：`cargo build`必须先安装`git`

## Rust升级

```shell
# 执行升级rust到稳定版本命令
rustup update stable
```

## 安装Sccache
```shell
cargo install sccache

vi ~/.cargo/config
# 配置已安装sccache路径
[build]
rustc-wrapper = "/path/to/.cargo/bin/sccache"
```

## 使用Rust in vscode

1. 安装rust-analyzer插件
2. 重启vscode

### debug in Rust

安装`CodeLLDB`插件，如果有发生*Acquiring CodeLLDB platform package*，执行[离线下载](https://github.com/vadimcn/vscode-lldb/releases/)对应版本。在扩展里，选择`···`，选择`从VSIX安装`，选择下载好的`*.vsix`文件，安装重启。

## Rust交叉编译

开发在Mac，产品最后运行在Linux。两种环境编译的二进制包是无法在彼此环境上运行的。

#### macOS编译，linux运行

```shell
x# 查看可用的目标环境rustup target list# 引入linux静态编译包rustup target add x86_64-unknown-linux-musl# 执行编译    cargo build --release --target=x86_64-unknown-linux-muslFinished release [optimized] target(s) in 54.64s####################################################### 说明编译成功！！！# 在target/x86_64-unknown-linux-musl/release目录内获得。######################################################shell
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
# 如果提示OPENSSL_DIR unset，但是环境变量一直都可用，解决方案是把OPENSSL_DIR="/usr/local/opt/openssl@1.1" 加入到命令前面
CC_x86_64_unknown_linux_musl="x86_64-linux-musl-gcc" cargo build --release --target=x86_64-unknown-linux-musl
```

##### 提示：openssl err的错误
MacOS
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

RockyLinux 9
```shell
# https://docs.rs/openssl/latest/openssl/
sudo dnf install pkg-config openssl-devel
```

##### 提示ring-x.xx.xx编译问题，没有找到文件或目录

```shell
# 切换为清华源tuna
[source.crates-io]
replace-with = 'tuna'

[source.tuna]
registry = "https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"
```

##### 提示linker `cc` not found
```shell
sudo dnf install gcc
```

##### 提示Unable to find libclang
```shell
sudo dnf install clang
```

## cargo开发协作
### 代码检查
```shell
rustup component add clippy
cargo clippy
```

### 更新crates库
```shell
cargo update
```

### 过期第三方库检查
```shell
cargo install -f cargo-outdated
cargo outdated
```

### 第三方库安全审计
```shell
cargo install -f cargo-audit
cargo audit
```

参考：[https://www.qttc.net/529-rust-cross-compile-mac-to-linux.html](https://www.qttc.net/529-rust-cross-compile-mac-to-linux.html)

参考：[https://blog.csdn.net/u013195275/article/details/106070326/](https://blog.csdn.net/u013195275/article/details/106070326/)
