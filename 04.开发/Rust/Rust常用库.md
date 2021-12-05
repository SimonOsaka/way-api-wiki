> 展示Rust常用的库。如果会使用java，那么将对标java的库，方便理解。

## Rust库

### 第三方库

| 类型          | Rust第三方github库                                           | Java第三方库                |
| ------------- | ------------------------------------------------------------ | --------------------------- |
| 序列化Json    | [serde](https://github.com/serde-rs/serde)                   | `fastjson`,`jackson`,`gson` |
| HTTP客户端    | [reqwest](https://github.com/seanmonstar/reqwest)            | `HttpClient`,`OkHttp`       |
| Web应用框架   | [warp](https://github.com/seanmonstar/warp)                  | `SpringMvc`,`Struts2`       |
| 日期时间      | [chrono](https://github.com/chronotope/chrono)               | `Joda-Time`                 |
| SQL数据库操作 | [sqlx](https://github.com/launchbadge/sqlx)                  | `Spring JDBC`               |
| ORM数据库操作 | [diesel](https://github.com/diesel-rs/diesel)                | `Hibernate`                 |
| 日志框架      | [flexi_logger](https://github.com/emabee/flexi_logger)       | `Log4j`,`Slf4j`,`Logback`   |
| JWT框架       | [jsonwebtoken](https://github.com/Keats/jsonwebtoken)        | `JJWT`,`java-jwt`           |
| 加密          | [rust-crypto](https://github.com/DaGenix/rust-crypto/)       | `Commons Codec`             |
| 表单参数验证  | [validator](https://github.com/Keats/validator)              | `Hibernate Validator`       |
| 邮箱发送      | [lettre](https://github.com/lettre/lettre)                   | `Java Mail Sender`          |
| 验证码        | [captcha](https://github.com/daniel-e/captcha)               | `kaptcha`,`easy-captcha`    |
| 授权框架      | [casbin](https://github.com/casbin/casbin-rs) + [sqlx-adapter](https://github.com/casbin-rs/sqlx-adapter) | `Shiro`,`Spring Security`   |
| Shell Script  | [run_script](https://github.com/sagiegurari/run_script)      | `javax.script`              |

### 客户端

| 类型             | Rust第三方github库                                           | Java第三方库                       |
| ---------------- | ------------------------------------------------------------ | ---------------------------------- |
| Redis客户端      | [redis](https://github.com/mitsuhiko/redis-rs)               | `Redis官网`,`jedis`,`Redisson`,... |
| ES客户端         | [elasticsearch](https://github.com/elastic/elasticsearch-rs) | `Elastic Search官网`               |
| MongoDB客户端    | [mongodb](https://github.com/mongodb/mongo-rust-driver)      | `MongoDB官网`                      |
| RabbitMQ客户端   | [amiquip](https://github.com/jgallagher/amiquip)             | `RabbitMQ官网`                     |
| OAuth2客户端     | [oauth2](https://github.com/ramosbugs/oauth2-rs)             | `OAuth2官网`                       |
| Kafka客户端      | [rdkafka](https://github.com/fede1024/rust-rdkafka)          | `Kafka官网`                        |
| etcd客户端       | [etcd_client](https://github.com/etcdv3/etcd-client)         | `Etcd官网`                         |
| ZooKeeper客户端  | [zookeeper](https://github.com/bonifaido/rust-zookeeper)     | `ZooKeeper官网`                    |
| Prometheus客户端 | [prometheus](https://github.com/tikv/rust-prometheus)        | `Prometheus官网`                   |
| Pulsar客户端     | [pulsar](https://github.com/wyyerd/pulsar-rs)                | `Pulsar官网`                       |

