## nginx本地开发加入ssl

1. 安装`openssl`

2. 执行命令

```shell
# 用openssl生成key和crt文件
> openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout /apps/cert/nginx.key -out /apps/cert/nginx.crt
```

3. 填写注册内容

```shell
Country Name (2 letter code) [XX]: CN
State or Province Name (full name) []: beijing    
Locality Name (eg, city) [Default City]: beijing
Organization Name (eg, company) [Default Company Ltd]: 回车
Organizational Unit Name (eg, section) []: 回车
Common Name (eg, your name or your server's hostname) []: *.jicu.vip
Email Address []: geniusmickymouse@qq.com
```

注：hostname使用**`*.jicu.vip`**代表泛域名。也可以使用**localhost**。

4. 配置nginx

```nginx
server {
  listen 443 ssl;
  server_name  mp.jicu.vip;

  ssl_certificate /apps/cert/nginx.crt;
  ssl_certificate_key /apps/cert/nginx.key;

  location / {
    root /apps/web/mp;
    index index.html;
  }
}
```

5. 校验nginx配置，重启

```shell
# 校验
> nginx -t
nginx: the configuration file /usr/local/etc/nginx/nginx.conf syntax is ok
nginx: configuration file /usr/local/etc/nginx/nginx.conf test is successful

# 重启
> nginx -s reload
```

6. 浏览器访问`https://mp.jicu.vip`，会有提示（不同浏览器不同），继续（强制）访问。
7. 如果代码中调用`https://api.jicu.vip`等其它的服务，需要按照==步骤6==在浏览器地址栏中访问一次。



本文结束！