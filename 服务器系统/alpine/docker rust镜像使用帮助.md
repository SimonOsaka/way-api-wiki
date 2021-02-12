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



## cargo install编译中出现的问题

| 问题                                                         | 解决                           |
| :----------------------------------------------------------- | ------------------------------ |
| = note: /usr/lib/gcc/x86_64-alpine-linux-musl/9.3.0/../../../../x86_64-alpine-linux-musl/bin/ld: cannot find crti.o: No such file or directory<br/>          collect2: error: ld returned 1 exit status | apk add --no-cache -U musl-dev |



## 克隆源代码

```shell
> apk add git
# Git安装完成

> git clone https://xxx...
```

