## crontab: error renaming解决办法

> 因为挖矿病毒，某些文件加入了chattr +i权限

执行 crontab -e 命令，系统显示类似如下。

```shell
[root@iZkZ cron]# crontab -e
crontab: installing new crontab
crontab: error renaming /var/spool/cron/#tmp.xx.XXXX3tTwiC to /var/spool/cron/root
rename: Operation not permitted
crontab: edits left in /tmp/crontab.BRY7dw
```

解决方法：进入`cd /var/spool/cron`，执行命令`chattr -i root`，修改/var/spool/cron/root权限。
然后执行`crontab -e`命令，系统显示类似如下，表示恢复正常。
crontab: installing new crontab

执行完，再将权限改回来`chattr +i root`

原文链接：https://blog.csdn.net/qq_29485643/java/article/details/89072025