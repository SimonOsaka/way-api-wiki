# 挖矿病毒清理

> 中了多次挖矿病毒，一直找不到中的原因，只能被动查找并删除。阿里云的服务器是不是有漏洞？除了系统暴露的端口之外，曾经只开了一个MySQL：3306端口，中了netflix病毒之后就关闭了。

## 第一种：伪装netflix挖矿病毒

1. `top`，查看哪个进程CPU高。一般有两个`netflix`和`yqgnnb`
2. `lsof -p 进程id`，根据信息找出病毒目录进行删除。有一个.sazh的路径，全部删除
3. 删除`/tmp`目录所有内容，有些硬炮无法删除，执行`lsattr`，如有`---------i------------`，那么执行`chattr -i *`，解除权限后，再执行删除
4. `kill -9 netflix`
5. `kill -9 yqgnnb`
6. 查看`/var/spool/cron/root`是否有定时任务来启动挖矿脚本

## 第二种：watchdogs挖矿病毒

> 这种病毒较为常见，网上搜索有专门的解决方案和清理脚本。我只记录几个关键的命令和路径。

* `lsattr`查看当前文件夹内的程序权限。**目的是找出有--------------i-------------的病毒文件**

* `chattr -i 程序名`解除-----------i-------------权限

* `/etc/rc.d/init.d`此目录有两个挖矿病毒`watchdogs`和`kthrotlds`，删除它

* `rm /usr/local/lib/libioset.so`删除此文件

* `cd /tmp`并`rm -rf *`删除/tmp内的文件

* 查看`/var/spool/cron/root`是否有定时任务来启动挖矿脚本
  
  参考：https://www.secpulse.com/archives/99420.html