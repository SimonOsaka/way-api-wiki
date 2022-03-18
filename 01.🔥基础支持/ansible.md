> 常用技术网站打开慢，使用几个网络命令解决

## 使用nslookup

此命令使用当前系统的DNS（114.114.114.114）作为域名解析基础

```shell
# nslookup
> raw.githubusercontent.com #这里输入速度慢的域名
Server:        114.114.114.114
Address:    114.114.114.114#53

Non-authoritative answer:
raw.githubusercontent.com    canonical name = github.map.fastly.net.
Name:    github.map.fastly.net
Address: 151.101.76.133
```

**如上输出，将Address结果151.101.76.133写入到hosts里。**

## 使用dig

此命令传递一个指定的DNS地址作为解析域名。

```shell
# dig @114.114.114.114 raw.githubusercontent.com
; <<>> DiG 9.10.6 <<>> @114.114.114.114 raw.githubusercontent.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 17141
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;raw.githubusercontent.com.    IN    A

;; ANSWER SECTION:
raw.githubusercontent.com. 33    IN    CNAME    github.map.fastly.net.
github.map.fastly.net.    33    IN    A    151.101.76.133

;; Query time: 59 msec
;; SERVER: 114.114.114.114#53(114.114.114.114)
;; WHEN: Wed Feb 10 10:42:40 CST 2021
;; MSG SIZE  rcvd: 105
```

**如上在*ANSWER SECTION*中，将解析得到的IP 151.101.76.133写入到hosts中。**

## 知名的DNS地址

### 一、114 DNS

高速 电信联通移动全国通用DNS，能引导您到最快的网站，手机和计算机都可用
稳定 DNS解析成功率超高，与ISP的DNS相比，能访问更多的国内外网站

可靠 3000万个家庭和企业DNS的后端技术支持，多次为电信运营商提供DNS灾备

纯净 无劫持 无需再忍受被强扭去看广告或粗俗网站之痛苦
服务ip为：`114.114.114.114` 和 `114.114.115.115`

拦截 钓鱼病毒木马网站 增强网银、证券、购物、游戏、隐私信息安全
服务ip为：`114.114.114.119` 和 `114.114.115.119`

学校或家长可选拦截 色情网站 保护少年儿童免受网络色情内容的毒害
服务ip为：`114.114.114.110` 和 `114.114.115.110`

### 二、阿里DNS

服务ip为：`223.5.5.5`和`223.6.6.6` 阿里巴巴集团众多优秀工程师开发维护的公共DNS---AliDNS

作为国内最大的互联网基础服务提供商，阿里巴巴在继承多年优秀技术的基础上，通过提供性能优异的公共DNS服务，为广大互联网用户提供最可靠的递归解决方案.

阿里公共DNS是阿里巴巴集团推出的DNS递归解析系统，目标是成为国内互联网基础设施的组成部分，面向互联网用户提供“快速”、“稳定”、“智能”的免费DNS递归解析服务。

当然阿里dns也于2019年支持ipv6dns了，IPv6：`2400:3200::1`和`2400:3200:baba::1`

### 三、百度DNS

服务IP为:`180.76.76.76` 百度公共DNS是百度系统部推出的递归DNS解析服务。

云防护，从此上网无患

病毒、木马、钓鱼网站一网拦截，百度云防护实时守护用户的访问安全。

无劫持，从此上网无阻

无恶意跳转，无强制广告，百度公共DNS让用户访问更加畅通无阻。

更精准，从此上网无忧

遍布全国的CDN网络、智能解析、edns-client-subnet… 所有的努力只为让定位更精准，让用户的每一次访问都更高效。

### 四、360 DNS

服务ip为：电信：首选：`101.226.4.6` 联通：首选：`123.125.81.6` 移动：首选：`101.226.4.6` 铁通：首选：`101.226.4.6`

使用 DNS派 的公共DNS解析服务后,让网上冲浪更加稳定、快速、安全; 为家庭拦截钓鱼网站，过滤非法网站，建立一个绿色健康的网上环境; 为域名拼写自动纠错, 让上网更方便。

### 五、Google DNS

服务ip为：`8.8.8.8`和`8.8.4.4`

而Google表示推出免费DNS服务的主要目的就是为了改进网络浏览速度、改善网络用户的浏览体验，为此Google并不使用BIND等广为使用的DNS程序，而是以自行开发的软件对DNS服务器技术进行了改进，在两层计算机簇上，缓存DNS服务器平衡负载以提升性能，同时保证了DNS服务的安全性和准确性。