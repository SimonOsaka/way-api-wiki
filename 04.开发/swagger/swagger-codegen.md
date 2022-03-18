##swagger-codegen

swagger-codegen的github：**https://github.com/swagger-api/swagger-codegen**

需要的环境：jdk > 1.7  maven > 3.3.3

安装比较简单：

下载：

```shell
git clone git@github.com:swagger-api/swagger-codegen.git
```

下载完成后进入下载的文件夹里

```shell
mvn package
```

等着就可以了，我是用了两个小时完成编译的。

swagger-codegen目前还没太用明白，目前只用到了swagger-codegen-cli.jar包。这个jar包的位置在<download_package>/modules/swagger-codegen-cli/target/swagger-codegen-cli.jar。把它拷出来就行了。已经是一个功能完善的完整的jar包了。

查看用法：

```shell
$ java -jar swagger-codegen-cli.jar help
usage: swagger-codegen-cli <command> [<args>]
The most commonly used swagger-codegen-cli commands are:
    　　config-help 　　Config help for chosen lang
    　　generate 　　　　Generate code with chosen lang
    　　help 　　　　　　 Display help information
    　　langs 　　　　          Shows available langs
    　　meta            MetaGenerator. Generator for creating a new template set and configuration for Codegen.  The output will be based on the language you specify, and includes default templates to include.
    　　version         Show version information
See 'swagger-codegen-cli help <command>' for more information on a specific
command.
```

主要介绍和spring-boot有关的用法，平常做spring-boot项目在服务间相互调用时一般都是RestTemplate，有了这个就不用了，可以生成client端的jar包，直接调用jar包中的方法就会进行服务调用。

首先需要会写yaml文件，还需要一个.json的配置文件，json文件可以有也可以没有，如果不用的话所有的参数就需要在命令行指定。

.yaml文件(这个文件的url是http://localhost:8888/{name} name是String的 返回值是SwaggerResponse{data:String})生成 application/json

```yaml
swagger: '2.0'
info:
  title: spring-cloud API
  description: Definition for test swagger-codegen
  version: 1.0.0
host: localhost:8888
schemes:
- http
basePath: /
produces:
- application/json
paths:
  /{name}:
    get:
      summary: test
      operationId: swaggerCodegen
      description: test
      produces:
      - application/json
      parameters:
      - name: name
        in: path
        description: 名
        type: string
        required: true
      responses:
        200:
          description: 返回结果
          schema:
            $ref: '#/definitions/SwaggerResponse'
definitions:
  SwaggerResponse:
    type: object
    properties:
      data:
        type: string
        description: 返回值
```

client端配置文件 spring-cloud.json：

```json
{
    "groupId" : "cn.fzk",　　　　　　　　　　　　　　# 这三个就是 maven或gradle中下载jar包需要的三个参数
    "artifactId" : "spring-cloud-client",
    "artifactVersion" : "1.0.0",

    "library" : "feign",　　　　　　　　　　　　　　  # library template to use (官网说的还没太懂，和http请求有关，用了这个就需要在项目配置相关jar包)
    "invokerPackage" : "cn.com.client",　　　　　　# 相当于源文件的真正代码的位置
    "modelPackage" : "cn.com.client.model",　　　　# model存放的位置definitions下定义的东西
    "apiPackage" : "cn.com.client.api"　　　　　　 # API最终在DefaultApi中，这个文件的位置
}
```

client端依赖的jar包：(gradle dependencies)

```groovy
dependencyManagement { imports { mavenBom 'org.springframework.cloud:spring-cloud-netflix:1.2.0.M1' } }

dependencies  {
  compile("com.netflix.feign:feign-core:8.17.0")
  compile("com.netflix.feign:feign-jackson:8.17.0")
  compile("com.netflix.feign:feign-slf4j:8.17.0")  
  compile "com.fasterxml.jackson.core:jackson-core:2.7.5"
  compile "com.fasterxml.jackson.core:jackson-annotations:2.7.5"
  compile "com.fasterxml.jackson.core:jackson-databind:2.7.5"
  compile "com.fasterxml.jackson.datatype:jackson-datatype-joda:2.7.5"
  compile group: 'com.fasterxml.jackson.datatype', name: 'jackson-datatype-joda', version: '2.0.4'
  compile("io.github.openfeign.form:feign-form:2.1.0")
  compile("io.github.openfeign.form:feign-form-spring:2.1.0")
}
```

server端配置文件 spring-cloud-server.json：

```json
{
    "groupId" : "cn.fzk",
    "artifactId" : "spring-cloud-server",
    "artifactVersion" : "1.0.0",

    "interfaceOnly" : "true",
    "library" : "spring-boot",
    "invokerPackage" : "cn.com.server",
    "modelPackage" : "cn.com.server.model",
    "apiPackage" : "cn.com.server.api"
}
```

生成jar包的方法。
client端： -i 指定生成API文件位置，-c 指定配置文件位置， -l 指定语言(下面会说)， -o 指定生成的文件存放的位置

```shell
java -jar swagger-codegen-cli.jar generate -i spring-cloud.yaml -c spring-cloud.json -l java -o client
cd client
mvn package
```

最终的jar包在target下有jar包，源码包，javadoc，test.jar。要的是spring-cloud-client-1.0.0.jar放到项目中。
项目中的使用方法：

```java
import cn.com.client.ApiClient;
import cn.com.client.api.DefaultApi;
import cn.com.client.model.SwaggerResponse;
import cn.com.fzk.resource.Base;
ApiClient apiClient = new ApiClient();
apiClient.setBasePath("http://localhost:8888");　　　　　　　　　　　　//需要指定服务器的地址
DefaultApi defaultApi = apiClient.buildClient(DefaultApi.class);
SwaggerResponse res = defaultApi.swaggerCodegen(name);
```

server端： (如果server端已经写好其实可以不用server端)
spring-boot项目指定的语言： -l spring

```java
java -jar swagger-codegen-cli.jar generate -i spring-cloud.yaml -c spring-cloud-server.json -l spring -o server
cd server
mvn package
```

生成的jar包同样在target下。
server端使用方法：

```java
@RestController
public class SwaggerController implements NameApi {
  @RequestMapping(value = "/{name}", method = RequestMethod.GET)
  public ResponseEntity<SwaggerResponse> swaggerCodegen(@PathVariable("name") String name) {
    System.out.println(name);
    SwaggerResponse res = new SwaggerResponse();
    res.setData("i am " + name);

    return new ResponseEntity<SwaggerResponse>(res, HttpStatus.OK);
  }
}
```

需要说的一下是server端的返回值一直封装在ResponseEntity中。但是对前台及client端调用都不影响，最终接受到的仍然是真正的response，如果真的实在看不过去可以在最开始的编译前修改下源码：
spring server端源码的位置：swagger-codegen/modules/swagger-codegen/src/main/resources/JavaSpring下面的api.mustache 和 apiController.mustache文件。
打开文件就能看到ResponseEntity，但是修改的时候一定要**注意**别改错了。
最后自己写的小程序调用一下就可以了。说简单有点简单说难挺难的，yaml文件特别难写，建议在swagger-editor上编写，可以在网页上弄，最好还是下载到本地。

下面的东西就没用了，仅仅记录一下。

```shell
java -java swagger-codegen-cli.jar generate <options>
OPTIONS
        -a <authorization>, --auth <authorization>
            adds authorization headers when fetching the swagger definitions
            remotely. Pass in a URL-encoded string of name:header with a comma
            separating multiple values

        --additional-properties <additional properties>
            sets additional properties that can be referenced by the mustache
            templates in the format of name=value,name=value

        --api-package <api package>
            package for generated api classes

        --artifact-id <artifact id>
            artifactId in generated pom.xml

        --artifact-version <artifact version>
            artifact version in generated pom.xml

        -c <configuration file>, --config <configuration file>
            Path to json configuration file. File content should be in a json
            format {"optionKey":"optionValue", "optionKey1":"optionValue1"...}
            Supported options can be different for each language. Run
            config-help -l {lang} command for language specific config options.

        -D <system properties>
            sets specified system properties in the format of
            name=value,name=value

        --group-id <group id>
            groupId in generated pom.xml

        -i <spec file>, --input-spec <spec file>
            location of the swagger spec, as URL or file (required)


        --import-mappings <import mappings>
            specifies mappings between a given class and the import that should
            be used for that class in the format of type=import,type=import

        --instantiation-types <instantiation types>
            sets instantiation type mappings in the format of
            type=instantiatedType,type=instantiatedType.For example (in Java):
            array=ArrayList,map=HashMap. In other words array types will get
            instantiated as ArrayList in generated code.

        --invoker-package <invoker package>
            root package for generated code

        -l <language>, --lang <language>
            client language to generate (maybe class name in classpath,
            required)

        --language-specific-primitives <language specific primitives>
            specifies additional language specific primitive types in the format
            of type1,type2,type3,type3. For example:
            String,boolean,Boolean,Double

        --library <library>
            library template (sub-template)

        --model-package <model package>
            package for generated models

        -o <output directory>, --output <output directory>
            where to write the generated files (current dir by default)

        -s, --skip-overwrite
            specifies if the existing files should be overwritten during the
            generation.

        -t <template directory>, --template-dir <template directory>
            folder containing the template files

        --type-mappings <type mappings>
            sets mappings between swagger spec types and generated code types in
            the format of swaggerType=generatedType,swaggerType=generatedType.
            For example: array=List,map=Map,string=String

        --reserved-words-mappings <import mappings>
            specifies how a reserved name should be escaped to. Otherwise, the
            default _<name> is used. For example id=identifier

        -v, --verbose
            verbose mode
```

config.json文件中可以生命的参数：

```shell
CONFIG OPTIONS
    modelPackage
        package for generated models

    apiPackage
        package for generated api classes

    sortParamsByRequiredFlag
        Sort method arguments to place required parameters before optional parameters. Default: true

    invokerPackage
        root package for generated code

    groupId
        groupId in generated pom.xml

    artifactId
        artifactId in generated pom.xml

    artifactVersion
        artifact version in generated pom.xml

    sourceFolder
        source folder for generated code

    localVariablePrefix
        prefix for generated code members and local variables

    serializableModel
        boolean - toggle "implements Serializable" for generated models

    library
        library template (sub-template) to use:
        jersey1 - HTTP client: Jersey client 1.18. JSON processing: Jackson 2.4.2
        jersey2 - HTTP client: Jersey client 2.6
        feign - HTTP client: Netflix Feign 8.1.1.  JSON processing: Jackson 2.6.3
        okhttp-gson (default) - HTTP client: OkHttp 2.4.0. JSON processing: Gson 2.3.1
        retrofit - HTTP client: OkHttp 2.4.0. JSON processing: Gson 2.3.1 (Retrofit 1.9.0)
        retrofit2 - HTTP client: OkHttp 2.5.0. JSON processing: Gson 2.4 (Retrofit 2.0.0-beta2
```

server端的说明：https://github.com/swagger-api/swagger-codegen/wiki/Server-stub-generator-HOWTO

本文来源：https://www.cnblogs.com/badboyf/p/6519997.html