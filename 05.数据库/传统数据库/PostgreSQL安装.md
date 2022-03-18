## 安装

官方文档 [PostgreSQL: Linux downloads (Red Hat family)](https://www.postgresql.org/download/linux/redhat/)

```shell
sudo dnf install -y postgresql14-server
```

## 初始化数据库

```shell
postgresql-14-setup initdb
########################################
## 数据生成在目录 /var/lib/pgsql/14/data
## 默认数据库 postgres
## 默认用户 postgres
## 密码为空
########################################
```

## 自定义配置

打开`/var/lib/pgsql/14/data/postgresql.conf`，例如修改`地址`和`端口`，修改后重启

```properties
#listen_addresses = 'localhost'        # what IP address(es) to listen on;
                    # comma-separated list of addresses;
                    # defaults to 'localhost'; use '*' for all
                    # (change requires restart)
#port = 5432                # (change requires restart)
```

## 适配客户端连接

打开`/var/lib/pgsql/14/data/pg_hba.conf`，修改内容如下

```properties
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            trust
host    all             all             0.0.0.0/32                trust
# IPv6 local connections:
host    all             all             ::1/128                 trust
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     trust
host    replication     all             127.0.0.1/32            trust
host    replication     all             ::1/128                 trust
```

复原

```properties
# TYPE  DATABASE        USER            ADDRESS                 METHOD

# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            scram-sha-256
# IPv6 local connections:
host    all             all             ::1/128                 scram-sha-256
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            scram-sha-256
host    replication     all             ::1/128                 scram-sha-256
```

## 修改默认密码

*必须先将`pg_hba.conf`文件的`METHOD`修改为`trust`，密码修改完成后，再将`METHOD`复原。*

```shell
# 在postgresql服务端
sudo -u postgres psql
alter user postgres with password 'postgres';

# 或者客户端连接后，直接执行
alter user postgres with password 'postgres';
```

## unit file

### 创建

```shell
sudo systemctl enable postgresql-14
```

### 启动

```shell
sudo systemctl start postgresql-14
```

### 重启

```shell
sudo systemctl restart postgresql-14
```

### 停止

```shell
sudo systemctl stop postgresql-14
```

### 删除

```shell
sudo systemctl disable postgresql-14
```
