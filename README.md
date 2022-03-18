### 开发与部署

| 项目               | 功能                    | 开发运行执行命令                                                                                                            | 产品打包执行命令                                                                                                                         |
| ---------------- | --------------------- | ------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| way-api          | 服务接口                  | IDE直接运行WayApplication                                                                                               | ./gradlew bootjar                                                                                                                |
| way-admin-shop   | 商家平台                  | npm run dev                                                                                                         | npm run build                                                                                                                    |
| way-admin-manage | 管理平台                  | npm run dev                                                                                                         | npm run build:prod                                                                                                               |
| way              | 移动端web                | npm run dev                                                                                                         | npm run pack:web                                                                                                                 |
| way-app-ios      | 移动端app（包括iOS、Android） | android: 根路径./android.dev（**如果dist不提交**），然后在Android Studio运行模拟器或打包<br><br>iOS: 根路径npm run copy:ios，然后在xcode内执行运行模拟器 | android: 根路径./android.prod（**如果dist不提交**），然后执行./gradlew assembleDevelopRelease<br><br/>iOS: 根路径npm run copy:ios，然后在xcode内执行运行模拟器 |

---

### Springboot

#### 启动脚本

线上设置`--spring.profiles.active=production`代表生产环境，日志最低级别info  
执行启动  
最小环境资源

```shell
java -Dspring.profiles.active=production -jar on_the_way-1.0.1-SNAPSHOT.jar -server -Xmx512M -Xms128M -Xmn512M -XX:MetaspaceSize=64M -XX:MaxMetaspaceSize=512M -Xss256K -XX:+DisableExplicitGC -XX:SurvivorRatio=1 -XX:+UseConcMarkSweepGC -XX:+UseParNewGC -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:CMSFullGCsBeforeCompaction=0 -XX:+CMSClassUnloadingEnabled -XX:LargePageSizeInBytes=128M -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70 -XX:SoftRefLRUPolicyMSPerMB=0 -XX:+PrintClassHistogram -XX:+PrintGCDetails -XX:+PrintGCTimeStamps -XX:+PrintHeapAtGC -Xloggc:/apps/log/gc.log &
```

---

### Nginx

#### Let's encrypt证书

有一个定时任务`crontab -l`查看，为了防止证书过期（或者一些不知道的原因）**每月22日凌晨3点**执行获取一次新证书。

如果**修改定时任务**（有权限），先`chattr -i /var/spool/cron/root`，修改`crontab -e`，再``chattr +i /var/spool/cron/root``

---

### MySQL

#### 线上my.cnf配置

```properties
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
innodb_buffer_pool_size = 64M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

sql-mode=ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```
