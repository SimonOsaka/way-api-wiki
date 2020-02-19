## 如何正确使用

> 请先将另外三篇文件学习完成，再看此篇。

按照如下顺序执行开发：

1. 打开swagger-editor，编辑一个新api的yaml文件。
2. 编辑yaml完成后，执行导出为yaml或json文件到本地。
3. 使用swagger-ui的docker命令行，打开将yaml或json文件。

```shell
docker run -p 8080:8080 -e URLS="[{ name: \"one\", url: \"/foo/swagger_1.json\"}, { name: \"two\", url: \"/foo/swagger_2.json\"}]" -v /Volumes/code/temp/foo:/usr/share/nginx/html/foo swaggerapi/swagger-ui

# 来源：https://github.com/swagger-api/swagger-ui/issues/4820
```

4. 用swagger-codegen将yaml转换成server和client源代码。

5. 将正常运行的**swagger-ui**和**client源代码**发给调用端开发人员进行同步开发。
6. **如果开发过程中有api改动，那么重新走一遍第1到第5步。**

7. server端引入controller相关源代码进行开发。
8. 开发完成后，测试人员可以使用swagger-ui直接对url测试。



还有swagger mock？？？

> 网络上有几种mock方式，大概总结为以下三种：
>
> 1. 将yaml转成带有mock数据的json格式spec。`https://github.com/whq731/swagger-mock-file-generator`
> 2. mock服务，使用开源mock提供的功能，编程式将mock数据和请求逻辑写入mock服务代码中。`https://github.com/APIDevTools/swagger-express-middleware`
> 3. mock服务，解析api请求地址，更改response结果，请求任意yaml的api，都随机返回mock数据。`https://github.com/whq731/swagger-express-middleware-with-chance`
>
> 下文使用第1种方式进行mock api。但是，本人更倾向于2、3联合，启动mock服务，直接mock response数据，简单直接

1. 将swagger-editor编辑好的api导出yaml到本地。
2. 用`https://github.com/whq731/swagger-mock-file-generator`将yaml转成有mock数据的json格式文件。
3. 用`https://github.com/APIDevTools/swagger-express-middleware`将json格式文件用`node xxx.js`启动即可mock。
4. 浏览器访问api内的任意接口，json格式文件内的mock数据都将response。
5. mock结束

*postman支持导入swagger api的yaml进行测试。*



##### 参考文献

* https://www.jianshu.com/p/879baf1cff07





