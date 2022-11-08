错误提示：`stdio.h not found`

## Macos 10.14解决方法
```shell
open /Library/Developer/CommandLineTools/Packages/macOS_SDK_headers_for_macOS_10.14.pkg
```
这种方法**不适用**macos 10.15

## Macos 10.15解决方法

### 方法1
```shell
#找到sdk路径
xcrun --show-sdk-path

#修改软连接
sudo ln -s /Library/Developer/CommandLineTools/SDKs/MacOSX10.15.sdk/usr/include/* /usr/local/include/ 
```

### 方法2
```shell
# reset to the default command line tools path
sudo xcode-select --reset

# 将CMAKE_OSX_SYSROOT的值改为： /Library/Developer/CommandLineTools/SDKs/MacOSX10.15.sdk
```