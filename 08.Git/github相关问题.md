## 解决GitHub的raw.githubusercontent.com无法连接问题

1. 访问https://site.ip138.com/raw.githubusercontent.com/
2. 输入raw.githubusercontent.com
3. 复制一个地理位置近的ip
4. 粘贴到hosts中，例如：`151.101.76.133 raw.githubusercontent.com`

## git使用https提交提示服务过期，不能使用'用户名+密码'提交
1. 创建token [https://github.com/settings/tokens](https://github.com/settings/tokens)
    - repo
    - read:org
    - workflow
2. 复制token
    - 替换
    ```shell
    git remote set-url origin https://粘贴token到这里@github.com/用户名/仓库名称.git
    ```
    - 先删除，再添加
    ```shell
    git remote remove origin

    git remote add origin https://粘贴token到这里@github.com/用户名/仓库名称.git
    ```