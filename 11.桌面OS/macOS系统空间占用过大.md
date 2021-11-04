# macOS系统空间占用过大

### 开发工具

##### Xcode

> 核心思想将文件转移到其它空间，再链接到原有目录

1. 执行命令`cd /`
2. 执行命令`du -sh *`查找哪些目录下有大文件
3. 找到后确认能删除就删除，如果不能删除就使用命令`ln -s`将大文件迁出系统空间
4. 用iOS举例，最大的文件就是模拟器文件，目录`/Library/Developer/CoreSimulator/Profiles/Runtimes`，将此目录`mv -r`到其它空间(如：`/Volumn/dev/ios/Runtimes`)，移动时间取决于文件大小
5. 这时`/Library/Developer/CoreSimulator/Profiles`已经为空，使用命令`ln -s /Volumn/dev/ios/Runtimes ./`生成一个链接文件
6. 启动xcode模拟器，校验是否正常

##### Android Studio

> android将SDK目录和AVD迁移出即可，步骤如上类似



### 使用OmniDiskSweeper免费应用

> 计算所有目录下的文件大小

1. 打开应用，让它计算所有文件夹的大小。
2. 根据文件夹大小，找出最占空间的文件夹。

```
HomeBrew占用较大，主要是.git文件夹巨大，直接删除文件夹内的文件。
Docker应用，docker.raw空间占用巨大，先修改空间大小，再移动此文件到非系统磁盘目录。
```

