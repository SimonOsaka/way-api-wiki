## MySQL备份（全量、增量）

> 每日进行增量备份，每三（或七）日进行全量备份，将备份完成的文件通过rsync传输到备份的服务器内保存

### 1. 备份脚本

#### 全量备份脚本

mysql_backup_all.sh

```shell
#!/bin/bash
#获取当前时间
date_now=$(date "+%Y%m%d-%H%M%S")
backUpFolder=/home/db/backup/mysql
username="root"
password="123456"
db_name="zone"
#定义备份文件名
fileName="${db_name}_${date_now}.sql"
#定义备份文件目录
backUpFileName="${backUpFolder}/${fileName}"
echo "starting backup mysql ${db_name} at ${date_now}."
/usr/bin/mysqldump -u${username} -p${password}  --lock-all-tables --flush-logs ${db_name} > ${backUpFileName}
#进入到备份文件目录
cd ${backUpFolder}
#压缩备份文件
tar zcvf ${fileName}.tar.gz ${fileName}

date_end=$(date "+%Y%m%d-%H%M%S")
echo "finish backup mysql database ${db_name} at ${date_end}."
```

#### 增量备份脚本

mysql_backup_increment.sh

```shell
#!/bin/bash
#在使用之前，请提前创建以下各个目录
backupDir=/usr/local/work/backup/daily
#增量备份时复制mysql-bin.00000*的目标目录，提前手动创建这个目录
mysqlDir=/var/lib/mysql
#mysql的数据目录
logFile=/usr/local/work/backup/bak.log
BinFile=/var/lib/mysql/mysql-bin.index
#mysql的index文件路径，放在数据目录下的

mysqladmin -uroot -p123456 flush-logs
#这个是用于产生新的mysql-bin.00000*文件
# wc -l 统计行数
# awk 简单来说awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。
Counter=`wc -l $BinFile |awk '{print $1}'`
NextNum=0
#这个for循环用于比对$Counter,$NextNum这两个值来确定文件是不是存在或最新的
for file in `cat $BinFile`
do
    base=`basename $file`
    echo $base
    #basename用于截取mysql-bin.00000*文件名，去掉./mysql-bin.000005前面的./
    NextNum=`expr $NextNum + 1`
    if [ $NextNum -eq $Counter ]
    then
        echo $base skip! >> $logFile
    else
        dest=$backupDir/$base
        if(test -e $dest)
        #test -e用于检测目标文件是否存在，存在就写exist!到$logFile去
        then
            echo $base exist! >> $logFile
        else
            cp $mysqlDir/$base $backupDir
            echo $base copying >> $logFile
         fi
     fi
done
echo `date +"%Y年%m月%d日 %H:%M:%S"` $Next Bakup succ! >> $logFile
```

==脚本未经过实际验证==

#### 定时任务

执行命令`crontab -e`，编辑定时任务脚本

```shell
# 增量备份脚本
* 2 * * 0-6 /apps/script/mysql_backup_increment.sh
# 全量备份脚本
* 3 * * 0   /apps/script/mysql_backup_all.sh
```

---

### 2. MySQL配置文件

> 配置文件和脚本对应的内容保持一致

/etc/my.cnf

```mysql
# Copyright (c) 2014, 2016, Oracle and/or its affiliates. All rights reserved.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; version 2 of the License.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301 USA

#
# The MySQL  Server configuration file.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

[mysqld]
pid-file    = /var/run/mysqld/mysqld.pid
socket        = /var/run/mysqld/mysqld.sock
datadir        = /var/lib/mysql
#log-error    = /var/log/mysql/error.log
# By default we only accept connections from localhost
#bind-address    = 127.0.0.1
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#binlog setting
log-bin=/var/lib/mysql/mysql-bin
server-id=123454
```
