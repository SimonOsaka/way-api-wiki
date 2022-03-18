> 开发时，当safari地址栏输入**http**://localhost，却跳转到**https**://localhost的问题，百思不得其解。搜索baidu毛都没有。

解决方案如下：

1. 打开safari
2. 快捷键`command + ,`
3. 隐私 -> 管理网站数据
4. 搜索`localhost`
5. 点击`移除`

#### 参考

* [Safari Redirecting http to (non-existent) https](https://apple.stackexchange.com/questions/215077/safari-redirecting-http-to-non-existent-https)

#### 学习

## 什么是HSTS？

### 1. Strict-Transport-Security

[HTTP Strict Transport Security](http://tools.ietf.org/html/rfc6797)，简称为HSTS。它允许一个HTTPS网站，要求浏览器总是通过HTTPS来访问它。现阶段，除了Chrome浏览器，Firefox4+，以及Firefox的NoScript扩展都支持这个响应头。

我们知道HTTPS相对于HTTP有更好的安全性，而很多HTTPS网站，也可以通过HTTP来访问。开发人员的失误或者用户主动输入地址，都有可能导致用户以HTTP访问网站，降低了安全性。一般，我们会通过Web Server发送301/302重定向来解决这个问题。现在有了HSTS，可以让浏览器帮你做这个跳转，省一次HTTP请求。

要使用HSTS，只需要在你的**HTTPS**网站响应头中，加入下面这行：

```http
strict-transport-security: max-age=16070400; includeSubDomains
```

includeSubDomains是可选的，用来指定是否作用于子域名。支持HSTS的浏览器遇到这个响应头，会把当前网站加入HSTS列表，然后在max-age指定的秒数内，当前网站所有请求都会被重定向为https。即使用户主动输入http://或者不输入协议部分，都将重定向到https://地址。

Chrome内置了一个HSTS列表，默认包含Google、Paypal、Twitter、Linode等等服务。我们也可以在Chrome输入chrome://net-internals/#hsts，进入HSTS管理界面。在这个页面，你可以增加/删除/查询HSTS记录。例如，你想一直以https访问某网址，通过“add Domain”加上去就好了。查看Chrome内置的全部HSTS列表，或者想把自己的网站加入这个列表，请[点这里](http://www.chromium.org/sts)。

### 2. X-Frame-Options

[X-Frame-Options](http://tools.ietf.org/html/draft-ietf-websec-x-frame-options-01)，已经转正为[Frame-Options](http://tools.ietf.org/html/draft-ietf-websec-frame-options-00)，但现阶段使用最好还是带上X-。Chrome4+、Firefox3.6.9+、IE8+均支持，详细的浏览器支持情况[看这里](https://developer.mozilla.org/en-US/docs/HTTP/X-Frame-Options?redirectlocale=en-US&redirectslug=The_X-FRAME-OPTIONS_response_header#Browser_compatibility)。使用方式如下：

```http
x-frame-options: SAMEORIGIN
```

这个响应头支持三种配置：

- DENY：不允许被任何页面嵌入；
- SAMEORIGIN：不允许被本域以外的页面嵌入；
- ALLOW-FROM uri：不允许被指定的域名以外的页面嵌入（Chrome现阶段不支持）；

如果某页面被不被允许的页面以<iframe>或<frame>的形式嵌入，IE会显示类似于“此内容无法在框架中显示”的提示信息，Chrome和Firefox都会在控制台打印信息。由于嵌入的页面不会加载，这就减少了点击劫持（Clickjacking）的发生。

### 3. X-XSS-Protection

顾名思义，这个响应头是用来防范XSS的。较早我是在介绍IE8的文章里看到这个，现在主流浏览器都支持，并且默认都开启了XSS保护，用这个header可以关闭它。它有几种配置：

- 0：禁用XSS保护；
- 1：启用XSS保护；
- 1; mode=block：启用XSS保护，并在检查到XSS攻击时，停止渲染页面（例如IE8中，检查到攻击时，整个页面会被一个#替换）；

浏览器提供的XSS保护机制并不完美，但是开启后仍然可以提升攻击难度，总之没有特别的理由，不要关闭它。

### 4. X-Content-Type-Options

互联网上的资源有各种类型，通常浏览器会根据响应头的Content-Type字段来分辨它们的类型。例如："text/html"代表html文档，"image/png"是PNG图片，"text/css"是CSS样式文档。然而，有些资源的Content-Type是错的或者未定义。这时，某些浏览器会启用MIME-sniffing来猜测该资源的类型，解析内容并执行。

例如，我们即使给一个html文档指定Content-Type为"text/plain"，在IE8-中这个文档依然会被当做html来解析。利用浏览器的这个特性，攻击者甚至可以让原本应该解析为图片的请求被解析为JavaScript。通过下面这个响应头可以禁用浏览器的类型猜测行为：

```http
X-Content-Type-Options: nosniff
```

这个响应头的值只能是nosniff，可用于IE8+和Chrome。另外，它还被Chrome用于扩展下载，[见这里](https://developer.chrome.com/extensions/hosting.html)。

### 5. X-Content-Security-Policy

这个响应头主要是用来定义页面可以加载哪些资源，减少XSS的发生。之前单独介绍过，请点击继续浏览：Content Security Policy介绍。

### 别人怎么用

最后，我们来看看几个实际案例：

Google+，使用了这几个本文提到的响应头：

```http
BASHx-content-type-options: nosniff  x-frame-options: SAMEORIGIN  x-xss-protection: 1; mode=block
```

Twitter使用了这些：

```http
BASHstrict-transport-security: max-age=631138519  x-frame-options: SAMEORIGIN  x-xss-protection: 1; mode=block
```

PayPal的：

```http
BASHX-Frame-Options: SAMEORIGIN  Strict-Transport-Security: max-age=14400
```

Facebook则使用了这些（配置了详细的CSP，关闭了XSS保护）：

```http
BASHstrict-transport-security: max-age=60  x-content-type-options: nosniff  x-frame-options: DENY  x-xss-protection: 0  content-security-policy: default-src *;script-src https://*.facebook.com http://*.facebook.com https://*.fbcdn.net http://*.fbcdn.net *.facebook.net *.google-analytics.com *.virtualearth.net *.google.com 127.0.0.1:* *.spotilocal.com:* chrome-extension://lifbcibllhkdhoafpjfnlhfpfgnpldfl 'unsafe-inline' 'unsafe-eval' https://*.akamaihd.net http://*.akamaihd.net;style-src * 'unsafe-inline';connect-src https://*.facebook.com http://*.facebook.com https://*.fbcdn.net http://*.fbcdn.net *.facebook.net *.spotilocal.com:* https://*.akamaihd.net ws://*.facebook.com:* http://*.akamaihd.net https://fb.scanandcleanlocal.com:*;
```