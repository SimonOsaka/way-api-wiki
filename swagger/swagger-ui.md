## swagger-ui

Swagger UI是一个API在线文档生成和测试的框架。
页面简单直接，方便调试，是Swagger的一个常用工具
配合Swagger-Editor或在线编辑swagger.yml / swagger.json

1 Download github代码

```shell
> git clone https://github.com/swagger-api/swagger-ui.git
```

2 安装 express

```shell
> npm install express --save
```

3 创建一个空文件夹node_app

```shell
> mkdir node_app
```

4 初始化 node ，根据提示创建package.json文件

```shell
> cd node_ap
> npm init
name: (node_app) node_app 
version: (1.0.0) 
description: 
entry point: (index.js)
```

5 安装 express

```shell
> npm install express --save
```

6 创建public文件夹，放static资源

```shell
> mkdir public
> cd public
```

7 创建index.js，启动文件

```js
var express = require('express'); var app = express();
app.use('/static', express.static('public')); 
app.get('/', function (req, res) { res.send('Hello World!'); }); 
app.listen(3000, function () { 
    console.log('Example app listening on port 3000!'); 
});
```

8 把Swagger UI项目中dist 目录下的文件全部复制到 public 文件夹下
如果要自定义UI界面，可以在public下修改css

```shell
> cp ../../dist/* .
```

9 启动node

```shell
> node index.js
```

访问 http://localhost:3000/static/index.html

---

现在页面显示的是官网的例子，替换为自己的swagger

编辑好swagger文件并切导出 swagger.json 文档，把 swagger.json 放到 node_app/public 目录下
在浏览器上方URL中改为/static/test.json，点击Explore刷新



或修改public/index.html

```js
// url = "http://petstore.swagger.io/v2/swagger.json" ,
url = "/static/swagger.json",
// 一般同一个项目会有多个版本的swagger.json接口定义文件，这时需要改写，核心内容为如下（也可以放到json文件，然后引入）
{urls: [{url:"",name:""},{url:"",name:""}]}
```

~~重启 node 服务即可~~

---

**以上只是一个通俗易懂的例子，看懂就行。**

==最后建议使用官方提供的docker==

