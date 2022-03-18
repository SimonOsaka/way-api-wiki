## 幂等（Idempotent）

一个HTTP方法是**幂等**的，指的是同样的请求被执行一次与连续执行多次的效果是一样的，服务器的状态也是一样的。换句话说就是，幂等方法不应该具有副作用（统计用途除外）。在正确实现的条件下，[`GET`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)，[`HEAD`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/HEAD)，[`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT)和[`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 等方法都是**幂等**的，而 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 方法不是。所有的 [safe](https://developer.mozilla.org/en-US/docs/Glossary/safe) 方法也都是幂等的。

幂等性只与后端服务器的实际状态有关，而每一次请求接收到的状态码不一定相同。例如，第一次调用[`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 方法有可能返回 [`200`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/200)，但是后续的请求可能会返回[`404`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status/404)。==[`DELETE`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/DELETE) 的言外之意是，开发者不应该使用`DELETE`方法实现具有删除最后条目功能的 RESTful API。==

需要注意的是，服务器不一定会确保请求方法的幂等性，有些应用可能会错误地打破幂等性约束。

`GET /pageX HTTP/1.1`是幂等的。连续调用多次，客户端接收到的结果都是一样的：

```html
GET /pageX HTTP/1.1   
GET /pageX HTTP/1.1   
GET /pageX HTTP/1.1   
GET /pageX HTTP/1.1   
```

`POST /add_row HTTP/1.1`不是幂等的。如果调用多次，就会增加多行记录：

```html
POST /add_row HTTP/1.1
POST /add_row HTTP/1.1   -> Adds a 2nd row
POST /add_row HTTP/1.1   -> Adds a 3rd row
```

`DELETE /idX/delete HTTP/1.1`是幂等的，即便是不同请求之间接收到的状态码不一样：

```html
DELETE /idX/delete HTTP/1.1   -> Returns 200 if idX exists
DELETE /idX/delete HTTP/1.1   -> Returns 404 as it just got deleted
DELETE /idX/delete HTTP/1.1   -> Returns 404
```

---

**对上面黄色高亮文字的解释**

Please keep in mind if some systems may have DELETE APIs like this:

```
DELETE /item/last
```

In the above case, calling operation N times will delete N resources – hence `DELETE` is not idempotent in this case. In this case, a good suggestion might be to change above API to POST – because POST is not idempotent.

```
POST /item/last
```

## 参考

* [https://developer.mozilla.org/zh-CN/docs/Glossary/幂等](https://developer.mozilla.org/zh-CN/docs/Glossary/幂等)

* https://restfulapi.net/idempotent-rest-apis/

* https://blog.cdemi.io/misconception-on-idempotency-in-rest-apis/

* https://stackoverflow.com/questions/4088350/is-rest-delete-really-idempotent