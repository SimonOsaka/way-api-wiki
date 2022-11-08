# Tauri安装
[官网Tauri](https://tauri.app/)

## Tauri
### 为cargo添加tauri模块
[tauri-cli](https://tauri.app/v1/guides/getting-started/setup/integrate#create-the-rust-project)

```shell
cargo install tauri-cli
```

### Tauri开发
[开发安装](https://tauri.app/v1/guides/getting-started/setup/)

## Tauri打包安装
### 执行打包
```shell
cargo tauri build
```

## 错误参考
1. 如果安装过程中出现错误#include <vips/vips8>
    - 需要安装vips/vips8
    - macOS环境
```shell
brew install vips
```

2. 如果安装过程中出现如下：
    - Error: No such file or directory @ rb_sysopen - /Users/XXXXX/Library/Caches/Homebrew/downloads/5d8d3b407d121e7be8e4eede6c5e72ef56f490098ddbb40e92fcc7cde8ad27d3--**hdf5**-1.12.2_1.catalina.bottle.tar.gz
    - 说明hdf5没有
```shell
brew install hdf5
```

3. 如果MacOS提示 Failed to load resource: The resource could not be loaded because the App Transport Security policy requires the use of a secure connection.
    - [issue](https://github.com/tauri-apps/tauri/issues/4722)
```json
// tauri.conf.json
// tauri -> bundle -> macOS
// 加入localhost
exceptionDomain: "localhost"
```
4. `Macos 10.15`执行`cargo tauri build`编译出错error: failed to run custom build command for `mac-notification-sys v0.5.6`
	网络有几种答案
	- 删除`/Library/Developer/CommandLineTools`后，再重装一次
	- 安装`brew install llvm` 
	- 安装`xcode`。✅奏效，但体积太过庞大，寻求其它解决方式
	最终方案：删除`/Library/Developer/CommandLineTools/SDKs`目录内的`MacOSX10.15.sdk`。✅问题解决