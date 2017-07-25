[返回首页](https://github.com/Runtu4378/swaggerDoc)

# MIMI Types(MIME 类型)

API 可以接受和返回不同格式的数据，最常见的格式是 JSON 和 XML，你可以使用 `consumes` 和 `produces` 关键字来为你的 API 指定其所能处理的 MIME 类型。该两个关键字的值是 MIME 类型数组。

在 API 规格文档根级别定义的全局 MIME 类型可以被所有 API 所继承使用，下面是使用 JSON 和 XML 两种类型的定义。

```YAML
consumes:
  - application/json
  - application/xml
produces:
  - application/json
  - application/xml
```

需要注意的是 `consumes` 只会影响请求体，即 POST、PUT、PATCH 等方法，会被对 GET 的请求体的处理所忽略。

当在每个 API 操作的层级里面定义 `consumes` 和 `produces` 的时候，它将会覆盖全局定义（而非增量延伸）。在下面的例子中，GET /logo 的操作被重定义为了返回图片：

```YAML
paths:
  /logo:
    get:
      summary: Returns the logo image
      produces:
        - image/png
        - image/gif
        - image/jpeg
      responses:
        200:
          description: OK
          schema:
            type: file
```

添加到 `consumes` 和 `produces` 里面的 MIME 类型需要和 [RFC 6838](http://tools.ietf.org/html/rfc6838) 兼容，举个例子，你可以使用的标准 MIME 类型为：

```
application/json
application/xml
application/x-www-form-urlencoded
multipart/form-data
text/plain; charset=utf-8
text/html
application/pdf
image/png
```

以及由供应商所提供的特定的 MIME 类型（由 `vnd.` 表示）

```
application/vnd.mycompany.myapp.v2+json
application/vnd.ms-excel
application/vnd.openstreetmap.data+xml
application/vnd.github-issue.text+json
application/vnd.github.v3.diff
image/vnd.djvu
```
