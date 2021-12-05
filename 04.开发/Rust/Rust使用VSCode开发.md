## vscode
1. 下载[Download Visual Studio Code - Mac, Linux, Windows](https://code.visualstudio.com/Download)

2. 打开vscode，安装插件
   - matklad.rust-analyzer
   - serayuzgur.crates
   - bungcip.better-toml
   - vadimcn.vscode-lldb
   - gsppvo.vscode-commandbar
> 详细配置，参考[SimonOsaka/my_adventures_api: 奇，api (github.com)](https://github.com/SimonOsaka/my_adventures_api)的文件夹`.vscode`
3. 打开rust工程，运行`cargo build`

## vscode plugins
### crates
设置为正在使用的registry目录，目录位置`/Users/用户名/.cargo/registry/index`
```json
"crates.localCargoIndexHash": "rsproxy.cn-8f6827c7555bfaf8"
```
