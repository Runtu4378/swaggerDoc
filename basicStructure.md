[返回首页](https://github.com/Runtu4378/swaggerDoc)

# 基本结构

Swagger 的规格文档可以使用 JSON 和 YAML 两种语法进行书写。在这篇指南中，我们只显示 YAML 的例子，不过 JSON 工作效果也同样优秀。

使用 YAML 编写的 Swagger 规格文档的简单示例如下：

```YAML
swagger: "2.0"
info:
  title: Sample API
  description: API description in Markdown.
  version: 1.0.0

host: api.example.com
basePath: /v1
schemes:
  - https

paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in Markdown.
      produces:
        - application/json
      responses:
        200:
          description: OK
```

## Metadata(元数据)

所有 Swagger 规格文档都要以 Swagger 版本号开头，2.0 是最新的版本。Swagger 版本定义明确了整个规格文档的结构——你可以编写什么和你如何编写它。

```YAML
swagger: "2.0"
```

然后，你需要定义 API 的相关信息：title、description（可选）、version（API 的版本而非该规格文档的版本或是 Swagger 的版本）

```YAML
info:
  title: Sample API
  description: API description in Markdown.
  version: 1.0.0
```

`version`: 你可以使用随意的数字，例如 `主要版本.次要版本.补丁版本`([语义化版本定义](http://semver.org/))，或者像 `1.0-beta or 2016.11.15`这样的随意格式。

`description`: 可以是[多行](http://stackoverflow.com/a/21699210)的，并且支持 [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/) 格式的富文本表示。

`info`: 还支持其他领域的联系信息如许可证或其他详情。

> 参考：[info 对象](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#infoObject)

## Base URL(基本 URL 信息)

所有通过 base URL 调用的 API 都会使用 schema、host 和 basePath 所定义的内容。

```YAML
host: api.example.com
basePath: /v1
schemes:
  - https
```

所有 API 路径都是以 base URL 为根的相对路径，如 `/users` 实际上是 `https://api.example.com/v1/users`。

> 更多内容：[API 主机和 Base URL](https://swagger.io/docs/specification/api-host-and-base-path/)

## Consumes, Produces(请求内容格式，应答内容格式)

`consumes` 和 `produces` 部分定义的是 API 所支持的 MIME 类型。在规格文档根级别的定义可以被内部定义所覆盖。

```YAML
consumes:
  - application/json
  - application/xml
produces:
  - application/json
  - application/xml
```

> 更多内容：[MIME 类型](https://swagger.io/docs/specification/mime-types/)

## Paths(路径)

`paths` 部分所定义的是您的 API 的个别端点（路径），和这些端点所支持的 HTTP 方法（操作），如 `GET /users` 可以这样定义：

```YAML
paths:
  /users:
    get:
      summary: Returns a list of users.
      description: Optional extended description in Markdown.
      produces:
        - application/json
      responses:
        200:
          description: OK
```

> 更多内容：[路径和操作](https://swagger.io/docs/specification/paths-and-operations/)

## Responses(应答)

对每个网络请求操作您都可以定义可能出现的网络状态码，例如 `200 OK` 或者 `404 Not found`，和应答体的 `schema（模式）`。模式可以直接定义或者通过 `$ref` 引用外部定义。你还可以为不同的应答内容类型定义不同的示例响应。

```YAML
paths:
  /users/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - in: path
          name: userId
          required: true
          type: integer
          minimum: 1
          description: The ID of the user to return.
      responses:
        200:
          description: A User object.
          schema:
            type: object
            properties:
              id:
                type: integer
                example: 4
              name:
                type: string
                example: Arthur Dent
        400:
          description: The specified user ID is invalid (e.g. not a number).
        404:
          description: A user with the specified ID was not found.
        default:
          description: Unexpected error
```

> 更多内容：[应答描述](https://swagger.io/docs/specification/describing-responses/)

## Input and Output Models(输入模型和输出模型)

你可以通过 `definitions` 定义你 API 中使用到的常用数据结构。当 `schema` 需要的时候你可以通过 `$ref` 来引用这些数据结构——无论是请求体定义还是应答体定义。

例如下面的 JSON 对象：

```JSON
{
  "id": 4,
  "name": "Arthur Dent"
}
```

可以被这样子呈现：

```YAML
definitions:
  User:
    properties:
      id:
        type: integer
      name:
        type: string
    # Both properties are required
    required:  
      - id
      - name
```

然后你像这样子可以这样在请求体和应答体中应用：

``` YAML
paths:
  /users/{userId}:
    get:
      summary: Returns a user by ID.
      parameters:
        - in: path
          name: userId
          required: true
          type: integer
      responses:
        200:
          description: OK
          schema:
            $ref: '#/definitions/User'
  /users:
    post:
      summary: Creates a new user.
      parameters:
        - in: body
          name: user
          schema:
            $ref: '#/definitions/User'
      responses:
        200:
          description: OK
```

## Authentication(认证)

`securityDefinitions` 和 `security` 关键字是用来描述你的 API 所使用的认证方法。

```YAML
securityDefinitions:
  BasicAuth:
    type: basic

security:
  - BasicAuth: []
```

支持的认证方法有：

- [基本认证](https://swagger.io/docs/specification/authentication/basic-authentication/)
- [API key](https://swagger.io/docs/specification/authentication/api-keys/)(作为请求头或者是查询参数)
- [OAuth 2](https://swagger.io/docs/specification/authentication/oauth2/)公共流（隐式、密码、应用和访问代码）

> 更多内容：[认证](https://swagger.io/docs/specification/authentication/)
