## MacOS (Catalina)：显示Read-only file system的对应方法

在Catalina版的macOS上以root身份登录，发现创建类似/data之类的目录会会提示Read-only file system，这篇文章介绍一下原因和对应方法。

### OS版本

```shell
liumiaocn:util liumiao$ sw_vers
ProductName:    Mac OS X
ProductVersion:    10.15.2
BuildVersion:    19C57
liumiaocn:util liumiao$ 
```

### 现象

```shell
liumiaocn:/ root# id
uid=0(root) gid=0(wheel) groups=0(wheel),1(daemon),2(kmem),3(sys),4(tty),5(operator),8(procview),9(procmod),12(everyone),20(staff),29(certusers),61(localaccounts),80(admin),701(com.apple.sharepoint.group.1),33(_appstore),98(_lpadmin),100(_lpoperator),204(_developer),250(_analyticsusers),395(com.apple.access_ftp),398(com.apple.access_screensharing),399(com.apple.access_ssh),400(com.apple.access_remote_ae)
liumiaocn:/ root# mkdir -p /data
mkdir: /data: Read-only file system
liumiaocn:/ root# 
```

### 原因

根据官方提示，升级至Catalina之后，硬盘会分为两部分：

- 只读部分
- 可写部分

![在这里插入图片描述](https://img-blog.csdnimg.cn/20200215112038738.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdW1pYW9jbg==,size_16,color_FFFFFF,t_70)

> With macOS Catalina, you can no longer store files or data in the read-only system volume, nor can you write to the “root” directory ( / ) from the command line, such as with Terminal.

在Catalina版本，使用者无法在只读系统卷进行数据的存储，使用root在通过命令行的方式也无法对/根目录下进行写操作了。

详细可参看：https://support.apple.com/en-us/HT210650

### 对应方法

首先设定SIP，详细可参看：https://liumiaocn.blog.csdn.net/article/details/104328486

```shell
liumiaocn:~ root# csrutil status
System Integrity Protection status: disabled.
liumiaocn:~ root# mkdir -p /data
mkdir: /data: Read-only file system
liumiaocn:~ root#
```

可以看到此时仍然无法创建目录，因为/仍然是只读方式, 修改一下/使之具有write的权限可以看到即可进行操作：

```shell
liumiaocn:~ root# mount -uw /
liumiaocn:~ root# mkdir -p /data
liumiaocn:~ root# 
```

### 注意事项

注意此种方式如果重启，mount设定的/的write属性就会失去

```shell
liumiaocn:~ root# ls /data
liumiaocn:~ root# mkdir -p /test
mkdir: /test: Read-only file system
liumiaocn:~ root# csrutil status
System Integrity Protection status: disabled.
liumiaocn:~ root# 
```

这种情况下可以使用软连接的方式解决这个问题

```shell
liumiaocn:/ root# mount -uw /
liumiaocn:/ root# rm -rf /data/
liumiaocn:/ root# mkdir -p /usr/local/data
liumiaocn:/ root# 
```

创建软连接

```shell
liumiaocn:/ root# ln -s /usr/local/data /data
liumiaocn:/ root# ls -l /data
lrwxr-xr-x  1 root  admin  15 Feb 15 16:32 /data -> /usr/local/data
liumiaocn:/ root#
```

这样在重启之后就没有问题了，详细可参看如下日志：

```shell
liumiaocn:~ root# mkdir /test
mkdir: /test: Read-only file system
liumiaocn:~ root# cd /data
liumiaocn:data root# mkdir test
liumiaocn:data root# ls
test
liumiaocn:data root# 
```

————————————————
版权声明：本文为CSDN博主「liumiaocn」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
原文链接：https://blog.csdn.net/liumiaocn/article/details/104324664