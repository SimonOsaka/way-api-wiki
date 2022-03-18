mac抓包

#mitmproxy

> 使用mitmproxy，免费

### 安装&运行

1. 在mac终端执行命令`brew install mitmproxy`进行安装

2. 执行命令`mitmdump —version`看到如下输出，代表安装成功
   
   ```shell
   Mitmproxy: 4.0.4
   Python:    3.7.3
   OpenSSL:   OpenSSL 1.0.2r  26 Feb 2019
   Platform:  Darwin-18.2.0-x86_64-i386-64bit
   ```

3. 执行命令`mitmproxy`启动，默认端口为8080，如果要自定义端口`-p 端口号`

4. 启动完成，在要抓包的设备中设置网关内容
   
   ```shell
   ip: 127.0.0.1 # 改为启动mitmproxy的机器ip
   port: 8080 # 默认。如果自定义就改为自定义端口
   ```

### 可视化页面

> 可以通过webui直观查看抓包结果

1. 终端执行命令`mitmweb`，默认端口8081，修改默认端口`--web-port 端口号`
2. 运行成功后，浏览器弹出`127.0.0.1:8081`网页，**默认的proxy端口为8080**

### https

> 抓取https配置方式

1. 首先启动mitmproxy
2. 打开浏览器输入mitm.it，下载证书（在对应的设备上），关于证书的配置在页面有描述
3. 证书配置完成后，重启mitmproxy即可抓取https包