```shell
#之前安装过其他版本，可以先全部移除开发工具
sudo rm -rf /Library/Developer/CommandLineTools
#安装
xcode-select --install
#tip:切换版本
sudo xcode-select -switch /Library/Developer/CommandLineTools/
#如果后来你安装了Xcode，可以切回
# Change the path if you installed Xcode somewhere else.
sudo xcode-select -s /Applications/Xcode.app/Contents/Developer
```
