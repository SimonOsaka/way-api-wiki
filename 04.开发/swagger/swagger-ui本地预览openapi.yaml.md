## swagger-ui本地预览openapi.yaml

> 双击swagger-ui.html可以本地预览

### 文件目录

* mp
  * way_article_post.yaml
* sp
  * way_article_post.yaml
* swagger-ui.html
* server.js
* server_config.js
* swagger-ui-data.js
* swagger-ui-web.sh

---

1. 创建swagger-ui.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>JICU API文档</title>
    <link rel="stylesheet" type="text/css" href="https://unpkg.com/swagger-ui-dist@3/swagger-ui.css">
</head>
<body>
<div id="swagger-ui"></div>

<script src="https://unpkg.com/swagger-ui-dist@3.25.0/swagger-ui-bundle.js"></script>
<script src="https://unpkg.com/swagger-ui-dist@3.25.0/swagger-ui-standalone-preset.js"></script>
<script src="http://localhost:9413/swagger-ui-data.js"></script>
<script type="text/javascript">
window.onload = function() {
    const ui = SwaggerUIBundle({
        urls: urls,
        dom_id: '#swagger-ui',
        presets: [
          SwaggerUIBundle.presets.apis,
          SwaggerUIStandalonePreset
        ],
        layout: "StandaloneLayout"
      });
    window.ui = ui
}

</script>
</body>
</html>
```

2. 创建swagger-ui-data.js

```js
const urls = [
    {url: "http://localhost:9413/mp/way_article_post.yaml", name: 'MP ARTICLE API 1.2'},
    {url: "http://localhost:9413/sp/way_article_post.yaml", name: 'SP ARTICLE API 1.2'}
]
```

3. 创建server_config.js

```js
module.exports = {
    root:  process.cwd(),
    hostname: 'localhost',
    port: 9413
}
```

4. 创建server.js

```js
const http = require('http');
const chalk = require('chalk');
const path = require('path');
const fs = require('fs');
const opn = require('opn');
const config = require('./server_config.js');

var server = http.createServer(function(req,res){
    const filePath = path.join(config.root,req.url)
    console.info("path",`${chalk.green(filePath)}`)
    fs.stat(filePath,(err,stats)=>{//fs.stat(path,callback),读取文件的状态；
        if(err){//说明这个文件不存在
            console.log(err)
            res.statusCode = 404;
            res.setHeader('Content-Type','text/javascript;charset=UTF-8');//utf8编码，防止中文乱码
            res.end(`${filePath} is not a directory or file.`)
            return;
        }
        var extname = path.extname(filePath)
        console.log('extname', extname);
        if(stats.isFile()){//如果是文件
            res.statusCode = 200;
            if (extname === '.html') {
                res.setHeader('Content-Type','text/html;charset=UTF-8');
            } else {
                res.setHeader('Content-Type','text/javascript;charset=UTF-8');
            }
            res.setHeader('Access-Control-Allow-Origin', '*');
            fs.createReadStream(filePath).pipe(res);//以流的方式来读取文件
        }else if (stats.isDirectory()) {//如果是文件夹，拿到文件列表
            fs.readdir(filePath,(err,files)=>{//files是个数组
                res.statusCode = 200;
                res.setHeader('Content-Type','text/plain');
                res.end(files.join(','));//返回所有的文件名
            })
        }
    })
});

server.listen(config.port,config.hostname,()=>{
    var addr = `http://${config.hostname}:${config.port}`;
    console.info(`listenning in:${chalk.green(addr)}`);
    console.log('浏览器会自动打开swagger-ui.html', '同时也可以访问'+`http://${config.hostname}:${config.port}`+'/swagger-ui.html')
    opn('./swagger-ui.html')
})
```

5. 创建swagger-ui-web.sh

```js
#!/bin/bash
echo 安装chalk
npm install chalk

echo 安装opn
npm install opn

echo 启动swagger-ui，[CTRL + C]关闭
node server.js
```

6. 执行命令

```shell
> chmod +x swagger-ui-web.sh
> ./swagger-ui-web.sh
```

**双击swagger-ui.html即可查看openapi接口定义文档**

**如果要让对方查看，浏览器输入`http://localhost:9413/swagger-ui.html`**