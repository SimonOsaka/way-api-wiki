## 使用官方tutorial学习dgraph

dgraph官方GitHub：`https://github.com/dgraph-io/dgraph`

dgraph官方学习教程GitHub：`https://github.com/dgraph-io/tutorial`

1. 将教程clone到本地

2. 运行
   
   ```shell
   cd tutorial
   ./script/local.sh
   ```
   
   *命令行会提示本地访问链接*

3. 问题来了，加载速度非常慢，检查过后发现是jquery的链接无法访问导致。
   
   ```shell
   vi theme/hugo-tutorials/layouts/partials/header.html
   # 查找code.jquery.com，找到引用js的位置，复制文件名，去bootcdn找到一样版本号的js文件链接，然后替换保存。
   ```

4. 重启tutorial，访问正常。

5. 先安装dgraph的docker，然后再根据教程操作，非常顺利。
