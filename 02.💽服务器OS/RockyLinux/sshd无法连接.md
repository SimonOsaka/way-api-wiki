rockylinux9 无法连接ssh
1. 需要修改`/etc/ssh/sshd_config`的`permitRootLog=yes`
2. 重启sshd服务`systemctl restart sshd`