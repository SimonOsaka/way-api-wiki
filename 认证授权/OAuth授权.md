## 背景

上一篇我们介绍了单点登录（SSO），它能够实现多个系统的统一认证。今天我们来谈一谈近几年来非常流行的，大名鼎鼎的OAuth。它也能完成统一认证，而且还能做更多的事情。至于OAuth与SSO的区别，将在文章最后总结。

![20180907145738](uploads/e3ca51ba31afeea57e953fd4dc11c198/20180907145738.png)

如上图所示，用户通过浏览器（Browser）访问app1，他想用微信的账号直接登录，这样就免去了在app1系统的注册流程。这样的流程完全符合单点登录（SSO），但我们今天要看看OAuth是怎么做的。

## 具体流程

![20180907155348](uploads/6e21eae234f3826c6b0d4add8143d25b/20180907155348.png)

流程比单点登录（SSO）复杂了很多，但是它比SSO更强大。接下来我们好好捋一捋这个流程：

1. 用户访问app1系统，app1返回登录页，让用户登录。
2. 用户点击微信登录，这里的微信就是OAuth Server，跳转到微信登录页，带上参数appid和回调地址（backUrl）。关于appid我们要详细说一下，我们在与OAuth Server做对接的时候，先要在OAuth Server上注册自己的系统（app1），需要填写应用的名称、回调地址等， OAuth Server会生成appid和appSecret，这两个变量是非常关键的。
3. 微信后台（OAuth Server）根据appid查找到app1的注册信息，校验参数backUrl和注册的回调地址是否一致（这里可以只校验域名或者一级域名，具体要看OAuth Server怎么设计），如果校验不通过则返回错误，校验成功则返回授权页。
4. 微信弹出授权页，如果微信没有登录则弹出登录并授权页。这个过程是微信询问用户，是否同意app1系统访问微信的资源。用户授权后， 微信后台（OAuth Server）会生成这个用户对应的code，并通过app1的backUrl返回app1系统。
5. app1系统拿到code后，再带上appid和appSecret从后台访问微信后台（OAuth Server），换取用户的token。
6. app1拿到token后，再去微信后台（OAuth Server）获取用户的信息。
7. app1设置session为登录状态，并将用户信息（昵称、头像等）返回给Browser。

到这里，OAuth的授权流程就结束了。**有的同学可能很快就会问到：用户授权后，为什么不直接返回token，而是要用code换取token？这是因为如果直接返回token，token会先到浏览器（Browser），然后再到app1系统，到了浏览器，这个token就是不安全的了，有可能被窃取，token被窃取后，微信后台（OAuth Server）中，这个用户的信息就不安全了。然而在用code换取token时，是带上了appid和appSecret的，微信后台（OAuth Server）可以判断appid和appSecret的合法性，确认无误后，再将token返回给app1。这里是直接返回app1，没有经过浏览器，token是安全的。**

## 静默登录

上面的流程是用户先访问app1，点击微信登录后，跳到微信。如果我们先打开了微信，在微信里边再打开app1，这个流程就好像我们在
微信里打开了京东，这里微信就是OAuth Server，京东就是app1。在这个流程中，我们可以省略掉询问用户是否授权的过程，也就是在微信里打开京东（app1）的时候，京东（app1）带着appid和backUrl访问微信（OAuth Server），微信（OAuth Server）验证appid和backUrl，并且微信（OAuth Server）已经是登录的，这里并没有询问用户是否授权京东访问自己在微信的资源，直接将code返回给了京东。从而使京东登录，并且在京东里显示的用户在微信中的昵称和头像等信息。

使用静默登录一般都是你的系统做的比较牛了，被资源方看中了，要把你的系统嵌入到资源方去。比如：微信中嵌入了京东。

上面的例子中，我们只做了获取用户信息，其实还可以开放很多信息，例如：用户的账户余额等。要开放哪些资源就看OAuth Server的了。
这也就是我们常说的open api。要做open api，上面的OAuth流程是必不可少的。

## 与单点登录（SSO）的对比

单点登录（SSO）是保障客户端（app1）的用户资源的安全 。

OAuth则是保障服务端（OAuth）的用户资源的安全 。

单点登录（SSO）的客户端（app1）要获取的最终信息是，这个用户到底有没有权限访问我（app1）的资源。

OAuth获取的最终信息是，我（OAuth Server）的用户的资源到底能不能让你（app1）访问。

单点登录（SSO），资源都在客户端（app1）这边，不在SSO Server那一方。 用户在给SSO Server提供了用户名密码后，作为客户端app1并不知道这件事。 随便给客户端（app1）个ST，那么客户端（app1）是不能确定这个ST是否有效，所以要拿着这个ST去SSO Server再问一下，这个ST是否有效，是有效的我（app1）才能让这个用户访问。

OAuth认证，资源都在OAuth Server那一方，客户端（app1）想获取OAuth Server的用户资源。 所以在最安全的模式下，用户授权之后，
服务端（OAuth Server）并不能通过重定向将token发给客户端（app1），因为这个token有可能被黑客截获，如果黑客截获了这个token，那么用户的资源（OAuth Server）也就暴露在这个黑客之下了。 于是OAuth Server通过重定向发送了一个认证code给客户端（app1），客户端（app1）在后台，通过https的方式，用这个code，以及客户端和服务端预先商量好的密码（appid和appSecret），才能获取到token，这个过程是非常安全的。 如果黑客截获了code，他没有那串预先商量好的密码（appid和appSecret），他也是无法获取token的。这样OAuth就能保证请求资源这件事，是用户同意的，客户端（app1）也是被认可的，可以放心的把资源发给这个客户端（app1）了。

**总结：所以单点登录（SSO）和OAuth在流程上的最大区别就是，通过ST或者code去认证的时候，需不需要预先商量好的密码（appid和appSecret）。**

## 总结

OAuth和SSO都可以做统一认证登录，但是OAuth的流程比SSO复杂。SSO只能做用户的认证登录，OAuth不仅能做用户的认证登录，开可以做open api开放更多的用户资源。