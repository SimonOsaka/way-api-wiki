## nginx使用ssl

## 使用certbot-auto配置ssl

### 首次生成证书

使用免费的Let's encrypt获取证书并让nginx使用，具体如下

1. 安装certbot-auto

   ```shell
   wget https://dl.eff.org/certbot-auto
   chmod a+x ./certbot-auto
   ./certbot-auto --help
   ```

2. 生成证书

   ```shell
   sudo ./certbot-auto certonly --preferred-challenges dns --manual --email geniusmickymouse@qq.com --server https://acme-v02.api.letsencrypt.org/directory -d jicu.vip -d *.jicu.vip
   ```

   生成证书过程中需要对指定的域名添加DNS解析信息（一共两条）

   ```
   _acme-challenge TXT 默认 记录值（填入一长串字母加数字）
   ```

   添加DNS完成后，再继续，直到提示完成。
---

### nginx配置证书

   1. 在nginx的conf文件夹中，创建文件`ssl.conf`

   ```nginx
   ssl_certificate       /etc/letsencrypt/live/jicu.vip/fullchain.pem;
   ssl_certificate_key   /etc/letsencrypt/live/jicu.vip/privkey.pem;
   ```

   将`ssl.conf`引入到其它域名配置文件www.conf中

   ```nginx
   server{
     listen       80;
     server_name  www.jicu.vip;
     return       301 https://$server_name$request_uri;
   }
   server {
   	listen 443 ssl;
   	server_name www.jicu.vip;
   	include /usr/local/nginx/conf/ssl.conf;
   	location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|ico)$ {
   		root            /apps/web/assets;
   		expires         30d;
     }
   
   	location / {
   		root   /apps/web/www;
   		index  index.html index.htm;
   		expires 30d;
     }
   }
   ```

   检查`nginx -t`配置是否准确，然后重启`nginx -s reload`。==检查服务器443端口是否开启。==

3. 访问域名www.jicu.vip是否正常，如果不正常问问百度。

### 配置域名自动续期

1. 先配置两个环境变量，最好写在/etc/profile，或者.bashrc里面，然后执行source生效

   ```shell
   # 阿里云的access key和secret,这个可以是ram子账户授权
   export CERTBOT_ALI_KEY=""
   export CERTBOT_ALI_SECRET=""
   ```

2. 下载certbot-alidns放到/usr/bin/，并改名为`certbot-alidns`

   ```
   编译好的下载，Linux版本,其他版本请自行编译；
   链接: https://pan.baidu.com/s/1zk3iM-KTbJr941frtFYJkw 提取码: uxe5
   ```

3. 加入定时任务重新生成证书，先编辑一个运行脚本`cert-renew.sh`

   ```bash
   #!/bin/bash
   # 停止nginx
   #nginx -s stop

   # 续签
   sudo /apps/software/certbot-auto renew --force-renew --cert-name jicu.vip --manual-auth-hook /usr/bin/certbot-alidns && /usr/local/nginx/sbin/nginx -s reload

   # 重启nginx
   #nginx -s reload
   ```

   写入定时任务

   ```shell
   # 编辑定时任务
   crontab -e

   # 写入定时任务。每周日，凌晨3点执行重新生成证书脚本
   * 3 * * 0 /apps/script/cert-renew.sh >> /apps/log/crontab/cert-renew-$(date +"\%Y-\%m-\%d").log 2>&1

   # 查看定时任务列表
   crontab -l
   ```

4. 手动重新生成证书，==如果有紧急情况==

   ```shell
   sudo /apps/software/certbot-auto renew --force-renew --cert-name jicu.vip --manual-auth-hook /usr/bin/certbot-alidns && /usr/local/nginx/sbin/nginx -s reload
   ```

5. 访问http://s.tool.chinaz.com/https/检查`SSL`过期时间是否更新

## 使用acme.sh配置ssl

1. 安装。查看[官方说明](https://github.com/acmesh-official/acme.sh/wiki/说明)。*如果是第一次使用，最好阅读一遍*
2. 安装完成后，重启一个命令行窗口`alias acme.sh`才起作用。
3. 配置对应域名运营商的api key。不知道如何配置，查看[官方说明dnsapi](https://github.com/acmesh-official/acme.sh/wiki/dnsapi)
4. 申请证书。执行命令`acme.sh --issue --dns dns_ali -d jicu.vip -d *.jicu.vip`。

   - 泛域名必须使用`--dns`来申请，否则会提示`The supported validation types are dns-01,but you specified http-01`，不同的服务提供商参数值会不同，这里是`dns_ali`阿里的。
   - 如果出现错误并看不到信息，在命令后面加入`--log`、`--debug`或`--debug 2`，后一个比前一个日志详细度更高。
5. 安装证书。执行命令`acme.sh --install-cert -d jicu.vip \
   --key-file       /etc/letsencrypt/live/jicu.vip/privkey.pem  \
   --fullchain-file /etc/letsencrypt/live/jicu.vip/fullchain.pem \
   --reloadcmd     "/usr/local/nginx/sbin/nginx -s reload"`
6. 打开浏览器查看自己的域名证书是否正常。
7. 由于let's encrypt会不定时修改策略，acme.sh也会进行跟进更新，为了防止再次使用acme.sh发生未知问题，使用之前最好进行升级，执行命令`acme.sh --upgrade`

提示：打开`https://crt.sh`，可以查看证书**申请次数**和**起止时间**是否==正常==。