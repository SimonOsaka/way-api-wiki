> git平台有多个的时候，sshkeys使用同一个不是很安全

### 多Sshkeys设置

1. 生成gitlab sshkeys

```shell
ssh-keygen -t rsa -C "你的邮箱" -f ~/.ssh/id_rsa_gitlab

# 一路回车
```

2. 生成github sshkeys

```shell
ssh-keygen -t rsa -C "你的邮箱" -f ~/.ssh/id_rsa_github

# 一路回车
```

3. 进入`.ssh`目录

```shell
cd ~/.ssh

# 目录内会有四个文件
id_rsa_gitlab id_rsa_gitlab.pub id_rsa_github id_rsa_github.pub
```

4. 执行agent

```shell
ssh-agent -s 
```

5. 添加私钥

```shell
ssh-add id_rsa_gitlab
ssh-add id_rsa_github

# 添加成功，提示Identity added: ./id_rsa_xxxx (xxxx@xxx.com)
```

6. 查看是否添加成功

```shell
ssh-add -l

# 会有两条内容，代表成功添加
```

7. 修改~/.ssh/config文件，没有就创建

```shell
# gitlab
Host gitlab.com
HostName gitlab.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_gitlab

# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_github
```

8. 将对应的id_rsa_xxxx.pub内容添加到xxxx.com的Ssh keys设置中

9. 验证设置是否准确

```shell
# 检查github
ssh -T git@github.com 
# 成功提示
# Hi xxx! You've successfully authenticated, but GitHub does not provide shell access. 

# 检查gitlab
ssh -T git@gitlab.com 
# 成功提示
# Welcome to GitLab, @xxx! 
```

现在就可以使用ssh进行git clone了~~



### 可能会用到的命令

```shell
# 删除所有已经添加的id_rsa_xxxx
ssh-add -D
```



### 参考文献

* [Git高级之配置多个SSH key](https://www.cnblogs.com/godfeer/p/12214301.html)

* [解决git@github.com: Permission denied (publickey). Could not read from remote repository.](https://www.jianshu.com/p/7d57ce4147d3)