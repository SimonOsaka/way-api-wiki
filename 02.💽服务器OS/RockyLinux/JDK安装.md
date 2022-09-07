## JDK安装
### Amazon Corretto Jdk 17安装
```shell
 sudo rpm --import https://yum.corretto.aws/corretto.key 
 sudo curl -L -o /etc/yum.repos.d/corretto.repo https://yum.corretto.aws/corretto.repo

 sudo yum install -y java-17-amazon-corretto-devel

 java -version
 ```
 如果系统已安装多于一种JDK，需要切换指定JDK版本
 ```shell
 sudo alternatives --config java

 sudo alternatives --config javac
 ```

 参考
 - [Amazon Corretto 17 Installation Instructions for Debian-Based, RPM-Based and Alpine Linux Distributions](https://docs.aws.amazon.com/corretto/latest/corretto-17-ug/generic-linux-install.html#rpm-linux-install-instruct)