## swagger-editor

> api的swagger编辑器

**接口开发，使用swagger-editor来写api描述语言，然后使用swagger-codegen生成server和client两端代码。**

- 访问GitHub，进入swagger-api
- 下载swagger-editor，最好使用docker

```shell
docker pull swaggerapi/swagger-editor
docker run -d -p 80:8080 swaggerapi/swagger-editor
```

- 然后部署，运行swagger-editor即可。
- 编辑完成api，可以导出yaml或json，最后让swagger-codegen调用生成对应语言的代码。

需要注意，可能有**墙**的原因，有些swagger.io打开很慢，或者干脆打不开。

------

==这篇没什么可以多写的==。

