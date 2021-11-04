> 着重叙述参考文献内发生的问题，补充一些经验和配置
## 主要内容
* 假设安装samba4机器ip为`192.168.3.37`
* 设置samba4机器hostname为`samba4`
* 设置samba4机器的域名称为`JICU.VIP`

### 问题&方法
* 问题：dns解析不到
```
使用 nslookup jicu.vip 命令刷新dns
```
* 问题：win10修改域，提示域名称没有找到
```
使用 ping jicu.vip 看看ip是否为192.168.3.37
使用 telnet 192.168.3.37 53 测试dns端口是否可以访问，按ctrl+]然后输入quit退出
```
### 补充
* /etc/hosts 追加
```shell
192.168.3.37 samba4.jicu.vip samba4
```

* /etc/resolv.conf
```shell
domain jicu.vip
nameserver 192.168.3.37 #本机ip
```

* 所有机器保持在同一网段`192.168.3.0`，子网掩码`255.255.255.0`，网关`192.168.3.1`
* samba4机器的DNS设置为本机ip`192.168.3.37`
* samba-tool配置的dns forwarder为`114.114.114.114`，可能`192.168.3.1`也好使，但是改了114之后就没有问题。
* 使用命令`systemctl start samba`后，查看`systemctl status samba`是否成功，如果log提示`dns update fail`切换root用户执行`systemctl restart samba`一次。默认应该在同一个用户中执行。

## 参考文献
1. [Samba4官网](https://www.samba.org)提供文件共享、域和ldap管理。
2. [在CentOS 7上安装Samba 4域控制器](https://www.howtoing.com/samba-4-domain-controller-installation-on-centos)`以此篇文章作为参考，基于此内容做补充`
3. [Samba 系列（四）：在 Windows 下管理 Samba4 AD 域管制器 DNS 和组策略](https://www.jianshu.com/p/d9017c66795a)
4. [Samba 系列（三）：使用 Windows 10 的 RSAT 工具来管理 Samba4 活动目录架构](https://linux.cn/article-8097-1.html)
5. [Samba服务器的配置与使用](https://www.cnblogs.com/Skyar/p/3667957.html)