[返回首页](https://github.com/Runtu4378/swaggerDoc)

# API Host and Base URL(主机名和根目录设置)

REST API 是以端点路由依附在根路由上面进行管理的。在 Swagger 中，根路由是在规格文档的根层级中的 `schema`，`host` 和 `basePath` 中被定义的。

```YAML
host: petstore.swagger.io
basePath: /v2
schemes:
  - https
```

所有 API 都是相对于根路由的，如 `/users` 实际上意味着 `<schema>://<host>/<basePath>/users`。

![路由示意图](http://2434zd29misd3e4a4f1e73ki-wpengine.netdna-ssl.com/wp-content/uploads/2017/02/url-structure.png)

## schemes(传输方案)

`schemes` 是用来定义 API 所使用的传输协议的。Swagger 支持 `http`,`https`和 `WebSocket` 方式—— ws 和 wss。在 YAML 中可以传入任意 list，即是可以使用以下的句法：

```YAML
schemes:
  - http
  - https
```

或者是数组语法：

```YAML
schemes: [http, https]
```

如果没有指定传输方案，则会使用调用 API 规格文档的方案来调用 API。

## host（主机）

`host` 是用来定义提供 API 的主机的域名或者是 IP 地址（IPv4）。如果所使用的端口与传输方案的默认端口（HTTP 是 80 端口，HTTPS 是 443 端口）不一样的话，host 定义中需要加上所使用的端口。需要注意的是本定义支定义主机名，不需要提供 `http(s)://` 或者是子路由。

合法的主机名：

```
api.example.com
example.com:8089
93.184.216.34
93.184.216.34:8089
```

错误的主机名：

```
http://api.example.com
example.com/api/v1
```

如果  `host`没有被定义的话，系统会自动使用提供 API 规格文档的主机名来作为默认值。

## basePath(主路径)

`basePath` 是所有 API 所使用的 URL 前缀，以主机路由作为根目录。它必须以反斜杠 `/` 开头，如果 `basePath` 没有被定义的话，它默认为 `/` ，意味着所有 API 的路径以主机名为开头。

合法的路径前缀：

```
/v2
/api/v2
/
```

不合法的路径前缀：

```
v2
```

## Omitting host and scheme(主机名定义和传输方案定义的省略)

在某些动态关联中 `host` 和 `scheme` 可以被省略，在这种情况下，提供 API 规格文档的主机名和传输方案会被用来 API 的调用。例如基于 UI 的 API 规格文档被托管在 [https://api.example.com/apidocs/index.html](https://api.example.com/apidocs/index.html) 上，在 UI 界面上调用 API 会被引导到 [https://api.example.com](https://api.example.com)

## FAQ

### 我可以指定多个主机，例如开发环境、测试环境和生产环境？

Swagger 2.0 在每一个 API 规格文档都只支持定义一个 `host`（或者说两个，如果你把 HTTP 和 HTTPS 视为不一样的两种接口的话）。

一种实现多主机名的方法可能是忽略掉 `host` 和 `schema` 的定义，将 API 规格文档放在不同主机上面去进行调用，这样的话每份拷贝都会被引导至不同的主机名底下。

多主机名将会在 3.0 版本中被支持。

### Host 和 basePath 支持模板语法么？

例如这样的：

```
https://{customer_id}.saas-app.com/api/v1
https://api.saas-app.com/v1/{customer_id}/apis
```

Swagger 2.0 不支持这样，不过在 3.0 中可能会被支持。

有关域名模板语法的解决方法可以参考上一个问题。

### 我可以为 HTTP 和 HTTPS 定义不同的端口吗？

例如这样的：

```
http://example.com:8080
https://example.com:8443
```

Swagger 2.0 不支持这样，不过在 3.0 中可能会被支持。

关于这种需求的解决方法同样请参考第一个问题。

## 参考
[https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#swagger-object](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#swagger-object)