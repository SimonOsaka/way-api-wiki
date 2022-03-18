# 读写分离

```properties
# 配置真实数据源
sharding.jdbc.datasource.names=master1,slave0
# 主数据库
sharding.jdbc.datasource.master1.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.master1.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
sharding.jdbc.datasource.master1.jdbc-url=jdbc:mysql://192.168.0.3:3306/ds0?characterEncoding=utf-8&autoReconnect=true&serverTimezone=Asia/Shanghai
sharding.jdbc.datasource.master1.username=test
sharding.jdbc.datasource.master1.password=12root
# 从数据库
sharding.jdbc.datasource.slave0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.slave0.hikari.driver-class-name=com.mysql.cj.jdbc.Driver
sharding.jdbc.datasource.slave0.jdbc-url=jdbc:mysql://192.168.0.3:3306/ds0?characterEncoding=utf-8&autoReconnect=true&serverTimezone=Asia/Shanghai
sharding.jdbc.datasource.slave0.username=test
sharding.jdbc.datasource.slave0.password=12root
# 配置读写分离
# 配置从库选择策略，提供轮询与随机，这里选择用轮询
sharding.jdbc.config.masterslave.load-balance-algorithm-type=round_robin
sharding.jdbc.config.masterslave.name=ms
sharding.jdbc.config.masterslave.master-data-source-name=master1
sharding.jdbc.config.masterslave.slave-data-source-names=slave0
# 开启SQL显示，默认值: false，注意：仅配置读写分离时不会打印日志
sharding.jdbc.config.props.sql.show=true
spring.main.allow-bean-definition-overriding=true
```

# 读写分离&水平分表&Druid

```properties
# ++++++++++++++++++ shardingsphere【START】 ++++++++++++++++++

# 配置数据源 分别是 主数据库1个 从数据库1个
sharding.jdbc.datasource.names=master0,master0slave0

# 主第一个数据库
sharding.jdbc.datasource.master0.type=com.alibaba.druid.pool.DruidDataSource
sharding.jdbc.datasource.master0.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.master0.url=jdbc:mysql://192.168.2.156:3306/sdkcms?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false&allowMultiQueries=true
sharding.jdbc.datasource.master0.username=root
sharding.jdbc.datasource.master0.password=XXXX

# 从第一个数据库
sharding.jdbc.datasource.master0slave0.type=com.alibaba.druid.pool.DruidDataSource
sharding.jdbc.datasource.master0slave0.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.master0slave0.url=jdbc:mysql://192.168.2.157:3306/sdkcms?useUnicode=true&characterEncoding=utf8&zeroDateTimeBehavior=convertToNull&useSSL=false&allowMultiQueries=true
sharding.jdbc.datasource.master0slave0.username=root
sharding.jdbc.datasource.master0slave0.password=XXXX


# 读写分离配置

# 从库的读取规则为round_robin（轮询策略），除了轮询策略，还有支持random（随机策略）
sharding.jdbc.config.masterslave.load-balance-algorithm-type=round_robin
# 逻辑主从库名和实际主从库映射关系
# 主数据库0
sharding.jdbc.config.sharding.master-slave-rules.sdkcms.master-data-source-name=master0
# 从数据库0
sharding.jdbc.config.sharding.master-slave-rules.sdkcms.slave-data-source-names=master0slave0

# 水平分表配置--自定义【START】

# 白名单用户
# 分表策略 其中uvip_user_class为逻辑表 分表主要取决于org_id行
sharding.jdbc.config.sharding.tables.vip_user_class.actual-data-nodes=sdkcms.vip_user_class_$->{0..2}
sharding.jdbc.config.sharding.tables.vip_user_class.table-strategy.inline.sharding-column=org_id
# 分片算法表达式
sharding.jdbc.config.sharding.tables.vip_user_class.table-strategy.inline.algorithm-expression=vip_user_class_$->{org_id %3}




# 水平分表配置--自定义【END】

# 打印操作的sql以及库表数据等
# 开启SQL显示，默认值: false，注意：仅配置读写分离时不会打印日志
sharding.jdbc.config.props.sql.show=true
```

# 分库分表

```properties
# 数据源 ds0,ds1
sharding.jdbc.datasource.names=ds0,ds1
# 第一个数据库
sharding.jdbc.datasource.ds0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.ds0.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds0.jdbc-url=jdbc:mysql://localhost:3306/ds0?characterEncoding=utf-8
sharding.jdbc.datasource.ds0.username=root
sharding.jdbc.datasource.ds0.password=root

# 第二个数据库
sharding.jdbc.datasource.ds1.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.ds1.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds1.jdbc-url=jdbc:mysql://localhost:3306/ds1?characterEncoding=utf-8
sharding.jdbc.datasource.ds1.username=root
sharding.jdbc.datasource.ds1.password=root

# 水平拆分的数据库（表） 配置分库 + 分表策略 行表达式分片策略
# 分库策略
sharding.jdbc.config.sharding.default-database-strategy.inline.sharding-column=id
sharding.jdbc.config.sharding.default-database-strategy.inline.algorithm-expression=ds$->{id % 2}

# 分表策略 其中user为逻辑表 分表主要取决于age行
sharding.jdbc.config.sharding.tables.user.actual-data-nodes=ds$->{0..1}.user_$->{0..1}
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.sharding-column=age
# 分片算法表达式
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.algorithm-expression=user_$->{age % 2}

# 主键 UUID 18位数 如果是分布式还要进行一个设置 防止主键重复
#sharding.jdbc.config.sharding.tables.user.key-generator-column-name=id

# 打印执行的数据库以及语句
sharding.jdbc.config.props..sql.show=true
spring.main.allow-bean-definition-overriding=true
```

# 读写分离&分库分表

```properties
# 可以看到配置四个数据源 分别是 主数据库两个 从数据库两个
sharding.jdbc.datasource.names=master0,master1,master0slave0,master1slave0

# 主第一个数据库
sharding.jdbc.datasource.master0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.master0.hikari.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.master0.jdbc-url=jdbc:mysql://192.168.0.4:3306/ds0?characterEncoding=utf-8&serverTimezone=Asia/Shanghai
sharding.jdbc.datasource.master0.username=test
sharding.jdbc.datasource.master0.password=12root

# 主第二个数据库
sharding.jdbc.datasource.master1.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.master1.hikari.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.master1.jdbc-url=jdbc:mysql://192.168.0.4:3306/ds1?characterEncoding=utf-8&serverTimezone=Asia/Shanghai
sharding.jdbc.datasource.master1.username=test
sharding.jdbc.datasource.master1.password=12root

# 从第一个数据库
sharding.jdbc.datasource.master0slave0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.master0slave0.hikari.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.master0slave0.jdbc-url=jdbc:mysql://192.168.0.3:3306/ds0?characterEncoding=utf-8&serverTimezone=Asia/Shanghai
sharding.jdbc.datasource.master0slave0.username=test
sharding.jdbc.datasource.master0slave0.password=12root

# 从第一个数据库
sharding.jdbc.datasource.master1slave0.type=com.zaxxer.hikari.HikariDataSource
sharding.jdbc.datasource.master1slave0.hikari.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.master1slave0.jdbc-url=jdbc:mysql://192.168.0.3:3306/ds1?characterEncoding=utf-8&serverTimezone=Asia/Shanghai
sharding.jdbc.datasource.master1slave0.username=test
sharding.jdbc.datasource.master1slave0.password=12root



# 读写分离配置

# 从库的读取规则为round_robin（轮询策略），除了轮询策略，还有支持random（随机策略）
sharding.jdbc.config.masterslave.load-balance-algorithm-type=round_robin

# 逻辑主从库名和实际主从库映射关系

# 主数据库0
sharding.jdbc.config.sharding.master-slave-rules.ds0.master-data-source-name=master0

# 从数据库0
sharding.jdbc.config.sharding.master-slave-rules.ds0.slave-data-source-names=master0slave0

# 主数据库1
sharding.jdbc.config.sharding.master-slave-rules.ds1.master-data-source-name=master1

# 从数据库1
sharding.jdbc.config.sharding.master-slave-rules.ds1.slave-data-source-names=master1slave0





# 分库分表配置

# 水平拆分的数据库（表） 配置分库 + 分表策略 行表达式分片策略

# 分库策略

sharding.jdbc.config.sharding.default-database-strategy.inline.sharding-column=id
sharding.jdbc.config.sharding.default-database-strategy.inline.algorithm-expression=ds$->{id %2}

# 分表策略 其中user为逻辑表 分表主要取决于age行
sharding.jdbc.config.sharding.tables.user.actual-data-nodes=ds$->{0..1}.user_$->{0..1}
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.sharding-column=age

# 分片算法表达式
sharding.jdbc.config.sharding.tables.user.table-strategy.inline.algorithm-expression=user_$->{age %2}

# 主键 UUID 18位数 如果是分布式还要进行一个设置 防止主键重复
#sharding.jdbc.config.sharding.tables.user.key-generator-column-name=id

# 打印操作的sql以及库表数据等
sharding.jdbc.config.props.sql.show=true
spring.main.allow-bean-definition-overriding=true
```

# 分库分表

```properties
sharding.jdbc.datasource.names=ds0,ds1

sharding.jdbc.datasource.ds0.type=org.apache.commons.dbcp.BasicDataSource
sharding.jdbc.datasource.ds0.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds0.url=jdbc:mysql://localhost:3306/test1
sharding.jdbc.datasource.ds0.username=root
sharding.jdbc.datasource.ds0.password=123456

sharding.jdbc.datasource.ds1.type=org.apache.commons.dbcp.BasicDataSource
sharding.jdbc.datasource.ds1.driver-class-name=com.mysql.jdbc.Driver
sharding.jdbc.datasource.ds1.url=jdbc:mysql://localhost:3306/test2
sharding.jdbc.datasource.ds1.username=root
sharding.jdbc.datasource.ds1.password=123456

#分库策略--对那个字段进行分库
sharding.jdbc.config.sharding.default-database-strategy.inline.sharding-column=user_id
#分库对user_id % 2进行分库选择
sharding.jdbc.config.sharding.default-database-strategy.inline.algorithm-expression=ds$->{user_id % 2} 

#分表策略--ds$->{0..1}.t_order_$->{0..1}代表是那个库下面的那个表
sharding.jdbc.config.sharding.tables.t_order.actual-data-nodes=ds$->{0..1}.t_order_$->{0..1}
#分表策略--对那个字段进行分表
sharding.jdbc.config.sharding.tables.t_order.table-strategy.inline.sharding-column=order_id
#分表的表达式
sharding.jdbc.config.sharding.tables.t_order.table-strategy.inline.algorithm-expression=t_order_$->{order_id % 2}
#分表对应的字段名
sharding.jdbc.config.sharding.tables.t_order.key-generator-column-name=order_id
```