# Caddy

## Offical site

[Caddy](https://caddyserver.com/)

## 安装

### MacOS

```shell
brew install caddy
```

other [downloads](https://caddyserver.com/download)

## 配置

```shell
# file server feature
file-server

# Optional: port to listen 
# Default: :80
# --listen
--listen :2022 

# Optional: file root dir
# Default:  the root directory of your site and run
# --root
--root /apps/web

# access log
--access-log
```

## 使用

```shell
caddy file-server --listen :2022 --access-log --root /apps/web
```

输出:

```shell
2022/04/22 00:18:44.325    WARN    admin    admin endpoint disabled
2022/04/22 00:18:44.326    INFO    tls.cache.maintenance    started background certificate maintenance    {"cache": "0xc0001d20e0"}
2022/04/22 00:18:44.326    INFO    tls    cleaning storage unit    {"description": "FileStorage:/Users/***/Library/Application Support/Caddy"}
2022/04/22 00:18:44.326    INFO    tls    finished cleaning storage units
2022/04/22 00:18:44.327    INFO    autosaved config (load with --resume flag)    {"file": "/Users/***/Library/Application Support/Caddy/autosave.json"}
2022/04/22 08:18:44 Caddy 2 serving static files on :2022
```

成功!!!