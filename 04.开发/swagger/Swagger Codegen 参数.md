## Swagger Codegen 参数

```shell
swagger-codegen-cli generate
                [(-a <authorization> | --auth <authorization>)]
                [--additional-properties <additional properties>...]
                [--api-package <api package>] [--artifact-id <artifact id>]
                [--artifact-version <artifact version>]
                [(-c <configuration file> | --config <configuration file>)]
                [-D <system properties>...] [--git-repo-id <git repo id>]
                [--git-user-id <git user id>] [--group-id <group id>]
                [--http-user-agent <http user agent>]
                (-i <spec file> | --input-spec <spec file>)
                [--ignore-file-override <ignore file override location>]
                [--import-mappings <import mappings>...]
                [--instantiation-types <instantiation types>...]
                [--invoker-package <invoker package>]
                (-l <language> | --lang <language>)
                [--language-specific-primitives <language specific primitives>...]
                [--library <library>] [--model-name-prefix <model name prefix>]
                [--model-name-suffix <model name suffix>]
                [--model-package <model package>]
                [(-o <output directory> | --output <output directory>)]
                [--release-note <release note>] [--remove-operation-id-prefix]
                [--reserved-words-mappings <reserved word mappings>...]
                [(-s | --skip-overwrite)]
                [(-t <template directory> | --template-dir <template directory>)]
                [--type-mappings <type mappings>...] [(-v | --verbose)]

OPTIONS
        -a <authorization>, --auth <authorization>
            adds authorization headers when fetching the swagger definitions
            remotely. Pass in a URL-encoded string of name:header with a comma
            separating multiple values

        --additional-properties <additional properties>
            sets additional properties that can be referenced by the mustache
            templates in the format of name=value,name=value. You can also have
            multiple occurrences of this option.

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
            name=value,name=value (or multiple options, each with name=value)

        --git-repo-id <git repo id>
            Git repo ID, e.g. swagger-codegen.

        --git-user-id <git user id>
            Git user ID, e.g. swagger-api.

        --group-id <group id>
            groupId in generated pom.xml

        --http-user-agent <http user agent>
            HTTP user agent, e.g. codegen_csharp_api_client, default to
            'Swagger-Codegen/{packageVersion}}/{language}'

        -i <spec file>, --input-spec <spec file>
            location of the swagger spec, as URL or file (required)

        --ignore-file-override <ignore file override location>
            Specifies an override location for the .swagger-codegen-ignore file.
            Most useful on initial generation.

        --import-mappings <import mappings>
            specifies mappings between a given class and the import that should
            be used for that class in the format of type=import,type=import. You
            can also have multiple occurrences of this option.

        --instantiation-types <instantiation types>
            sets instantiation type mappings in the format of
            type=instantiatedType,type=instantiatedType.For example (in Java):
            array=ArrayList,map=HashMap. In other words array types will get
            instantiated as ArrayList in generated code. You can also have
            multiple occurrences of this option.

        --invoker-package <invoker package>
            root package for generated code

        -l <language>, --lang <language>
            client language to generate (maybe class name in classpath,
            required)

        --language-specific-primitives <language specific primitives>
            specifies additional language specific primitive types in the format
            of type1,type2,type3,type3. For example:
            String,boolean,Boolean,Double. You can also have multiple
            occurrences of this option.

        --library <library>
            library template (sub-template)

        --model-name-prefix <model name prefix>
            Prefix that will be prepended to all model names. Default is the
            empty string.

        --model-name-suffix <model name suffix>
            Suffix that will be appended to all model names. Default is the
            empty string.

        --model-package <model package>
            package for generated models

        -o <output directory>, --output <output directory>
            where to write the generated files (current dir by default)

        --release-note <release note>
            Release note, default to 'Minor update'.

        --remove-operation-id-prefix
            Remove prefix of operationId, e.g. config_getId => getId

        --reserved-words-mappings <reserved word mappings>
            specifies how a reserved name should be escaped to. Otherwise, the
            default _<name> is used. For example id=identifier. You can also
            have multiple occurrences of this option.

        -s, --skip-overwrite
            specifies if the existing files should be overwritten during the
            generation.

        -t <template directory>, --template-dir <template directory>
            folder containing the template files

        --type-mappings <type mappings>
            sets mappings between swagger spec types and generated code types in
            the format of swaggerType=generatedType,swaggerType=generatedType.
            For example: array=List,map=Map,string=String. You can also have
            multiple occurrences of this option.

        -v, --verbose
            verbose mode
```

config

```shell
CONFIG OPTIONS

  sortParamsByRequiredFlag

    Sort method arguments to place required parameters before optional parameters. (Default: true)

  ensureUniqueParams

    Whether to ensure parameter names are unique in an operation (rename parameters that are not). (Default: true)

  modelPackage

 package for generated models

  apiPackage

    package for generated api classes

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

    boolean - toggle "implements Serializable" for generated models (Default: false)

  bigDecimalAsString

    Treat BigDecimal values as Strings to avoid precision loss. (Default: false)

  fullJavaUtil

 whether to use fully qualified name for classes under java.util.

    This option only works for Java API client (Default: false)

  hideGenerationTimestamp

    hides the timestamp when files were generated

  dateLibrary

 Option. Date library to use

      joda - Joda

      legacy - Legacy java.util.Date

      java8-localdatetime - Java 8 using LocalDateTime (for legacy app only)

      java8 - Java 8 native

  useRxJava

 Whether to use the RxJava adapter with the retrofit2 library. (Default: false)

  library

 library template (sub-template) to use (Default: okhttp-gson)

      jersey1 - HTTP client: Jersey client 1.19.1. JSON processing: Jackson 2.7.0

      feign - HTTP client: Netflix Feign 8.16.0. JSON processing: Jackson 2.7.0

      jersey2 - HTTP client: Jersey client 2.22.2. JSON processing: Jackson 2.7.0

      okhttp-gson - HTTP client: OkHttp 2.7.5. JSON processing: Gson 2.6.2

      retrofit - HTTP client: OkHttp 2.7.5. JSON processing: Gson 2.3.1 (Retrofit 1.9.0).

        IMPORTANT NOTE: retrofit1.x is no longer actively maintained so please upgrade to 'retrofit2' instead.

      retrofit2 - HTTP client: OkHttp 3.2.0. JSON processing: Gson 2.6.1 (Retrofit 2.0.2). 

        Enable the RxJava adapter using '-DuseRxJava=true'. (RxJava 1.1.3)
```
