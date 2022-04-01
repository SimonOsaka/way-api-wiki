## 使用git初始化已经存在的项目

首先修改本地全局配置文件，设置默认分支为`main`

```shell
git config --global init.defaultBranch main
```

主干名称`main`

```shell
git init

git add .

git commit -m initial

git remote add origin https://xxxx.xxx.xxx/xxxxxx.git

git pull --rebase origin main

git push -u origin main
```
