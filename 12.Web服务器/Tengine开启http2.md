## 为什么要用Http2

提升性能。自行baidu获取答案。

## Tengine追加使用Http2

1. 获取Tengine源码，`git clone https://github.com/alibaba/tengine.git`

2. 执行编译
   
   ```shell
   cd tengine
   
   ./configure --with-http_ssl_module --with-http_v2_module # 如果还有其他参数也加上
   
   make
   ```

3. 检验结果
   
   ```shell
   cd objs
   
   ./nginx -v
   Tengine version: Tengine/x.x.x (nginx/x.x.x) # 提示：x.x.x为版本号
   
   ./nginx -V
   结果列表有 ngx_http_v2_module (static) 代表 HTTP/2 编译模块通过
   ```

4. 替换旧nginx
   
   ```shell
   # 当前还在objs目录
   sudo cp -f nginx /usr/local/nginx/sbin
   ```

5. 进入nginx配置文件，启用http2
   
   ```shell
   # listen 443 ssl;
   # 改为
   listen 443 http2 ssl;
   ```

6. 保存配置文件，检查是否正确`nginx -t`，然后重启`nginx -s reload`

7. 右键打开safari浏览器的`检查元素`，访问或刷新要启用http2的域名，在`网络`一栏里，对应的域名的有一列`协议`（如果没有就右键添加进去）显示h2证明启用成功。***如果还是http/1.1，说明没有启用成功，看看nginx是否重启正确。***
