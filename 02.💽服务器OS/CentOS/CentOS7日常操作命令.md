## CentOS7 修改密码，不需要重启

#### 修改密码

```shell
# 输入passwd，再输入两次新密码后，立即生效
> passwd
```

## CentOS7定时任务

cron服务是Linux的内置服务，但它不会开机自动启动，可以每分钟执行任务。可以用以下命令启动和停止服务：

```shell
systemctl start crond
systemctl stop crond
systemctl restart crond
systemctl reload crond
systemctl status crond
```

添加开机启动

```shell
vi /etc/rc.local

/bin/systemctl start crond.service
```

**crontab操作**

```shell
crontab -u //设定某个用户的cron服务 
crontab -l //列出某个用户cron服务的详细内容 
crontab -r //删除某个用户的cron服务 
crontab -e //编辑某个用户的cron服务
crontab -i //打印提示，输入yes等确认信息

/var/spool/cron/root (以用户命名的文件) 是所有默认存放定时任务的文件
/etc/cron.deny 该文件中所列出用户不允许使用crontab命令
/etc/cron.allow 该文件中所列出用户允许使用crontab命令，且优先级高于/etc/cron.deny

/var/log/cron    该文件存放cron服务的日志
```

**基本格式**

```shell
# For details see man 4 crontabs
# Example of job definition:
# .---------------- minute (0 - 59)
# | .------------- hour (0 - 23)
# | | .---------- day of month (1 - 31)
# | | | .------- month (1 - 12) OR jan,feb,mar,apr ...
# | | | | .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# | | | | |
# * * * * * user-name command to be executed
定时任务的每段为：分，时，日，月，周，用户，命令
第1列表示分钟1～59 每分钟用*或者 */1表示
第2列表示小时1～23（0表示0点）
第3列表示日期1～31
第4列表示月份1～12
第5列标识号星期0～6（0表示星期天）
第6列要运行的命令

*：表示任意时间都，实际上就是“每”的意思。可以代表00-23小时或者00-12每月或者00-59分
-：表示区间，是一个范围，00 17-19 * * * cmd，就是每天17,18,19点的整点执行命令
,：是分割时段，30 3,19,21 * * * cmd，就是每天凌晨3和晚上19,21点的半点时刻执行命令
/n：表示分割，可以看成除法，*/5 * * * * cmd，每隔五分钟执行一次
```

https://www.cnblogs.com/p0st/p/9482167.html
