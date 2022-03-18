## 扩容

> 停止虚拟机

* 打开virtualbox 管理》虚拟介质管理》可以直接调。
* 执行命令`VBoxManage modifyhd [虚拟机vdi文件位置] --resize 10240 `

## 命令

### 列表

`VBoxManage list vms`

```
"manjaro" {59e701ab-c653-433a-b06a-4b655b76f000}
"Rockylinux8.4" {033dbb7b-7375-4dbc-ad98-bafd3dda2961}
"k8s-mirror" {ab16589e-e716-42c8-a423-fed1aa4134b9}
"k8s-linkerd2" {105cd1be-845e-4bb4-ab33-11170b507b31}
```

### 启动

`VBoxManage startvm "Rockylinux8.4" --type=headless # 分离式后台启动`  

### 正在运行

`VBoxManage list runningvms`

### 正常关机

`VBoxManage controlvm "Rockylinux8.4" acpipowerbutton `

### 强制关机（拔电源）

`VBoxManage controlvm "Rockylinux8.4" poweroff`
