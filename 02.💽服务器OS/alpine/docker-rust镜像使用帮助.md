> alpine docker

## 请求rust镜像

```shell
> docker pull rust:alpine
```

## 加入国内cargo镜像源

```shell
> vi cargo_temp_config
# 写入
[source.crates-io]
replace-with='ustc'

[source.ustc]
registry="https://mirrors.ustc.edu.cn/crates.io-index"

> docker run -it -v ./cargo_temp_config:/.cargo/config rust:alpine 
# ⚠️此处如果使用$HOME/.cargo/config将不起作用
```

### dockerfile

```dockerfile
# 重新制作一个新的docker image
FROM rust:alpine
# 修改alpine源为ustc
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
# 避免cargo编译时出现问题
ENV CARGO_HTTP_MULTIPLEXING false
# 加入cargo国内源的配置文件
RUN mkdir /.cargo
ADD ./cargo_temp_config /.cargo/config
```

执行命令`docker build -t rust_alpine .`构建镜像

### 多阶段构建镜像

```dockerfile
FROM rust:alpine as builder
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories
ENV CARGO_HTTP_MULTIPLEXING false
RUN mkdir /.cargo
ADD ./cargo_temp_config /.cargo/config
RUN apk add git
RUN git clone https://xxx.xxx.xxx/xxx/xxx # 改为自己的仓库地址
RUN cd xxx
RUN cargo build --release

FROM rust:alpine
COPY --from=builder /build_source/target/release/xxx .
CMD ["./xxx"]
```

执行命令`docker build -t rust_alpine .`构建镜像

## cargo install编译中出现的问题

| 问题                                                                                                                                                                                                      | 解决                             |
|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ |
| = note: /usr/lib/gcc/x86_64-alpine-linux-musl/9.3.0/../../../../x86_64-alpine-linux-musl/bin/ld: cannot find crti.o: No such file or directory<br/>          collect2: error: ld returned 1 exit status | apk add --no-cache -U musl-dev |

## 克隆源代码

```shell
> apk add git
# Git安装完成

> git clone https://xxx...
```
