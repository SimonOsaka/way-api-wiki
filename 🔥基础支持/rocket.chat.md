[官网](https://rocket.chat)
## 安装
* 使用官方github.com的`docker-compose.yml`进行安装。
* hubot-rocketchat需要创建一个用户，否则status显示restart，用户、密码都在`docker-compost.yml`中。
* **OTR会话功能，必须使用https，无法继续校验此功能。**

## 使用
* public channel，公开频道，所有人可以加入并可见，可对话，可以只读。可以作为公司新闻发布频道，技术讨论频道等等。
* private channel，私有组，必须由创建人进行邀请才可以加入，可以只读。可以作为team、具体业务组等等。
* broadcast channel，广播频道，可以作为广播消息，授权人才可以发布消息，未授权可以回复消息。
* discussion，讨论组，基于一个channel而创建的讨论，讨论当前channel的某一个事务。比如某个技术频道出现一个技术问题。
* direct message，直发消息，1-1进行消息沟通。