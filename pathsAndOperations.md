[返回首页](https://github.com/Runtu4378/swaggerDoc)

# Path and Operations(API 路径和操作)

在 Swagger 的术语中，**paths** 是你的 API 对外暴露的端点（或资源），如 `/users`、`/reports.summary` 等。**operations** 则是调用这些端点所使用的 HTTP 方法，如 `GET`、`POST`、`DELETE`等。

## Paths(路径)

在 API 规格文档的 `paths` 部分定义 API 路径和操作。

```YAML
paths:
  /ping:
    ...
  /users:
    ...
  /users/{id}:
    ...
```

所有路径都是以 basePath 为根目录的相对路径（更多查看[主机名和根目录设置](https://github.com/Runtu4378/swaggerDoc/blob/master/apiHostAndBaseURL.md)）。完整的请求 URL 是 `scheme://host/basePath/path`。

## Path Templating(路径模板)

Swagger 支持路径模板，这意味着你可以使用大括号去标记 URL 的某部分作为路径传参。

```
/users/{id}
/organizations/{orgId}/members/{memberId}
/report.{format}
```

API 客户端在调用 API 的时候需要提供合适的参数，例如 `/users/5` 或者 `/users/12`。

## Operations(操作)

你需要为每个 API 路径去定义能够被访问的操作（HTTP 方法）。Swagger 2.0 支持 `GET`、`POST`、`PUT`、`PATCH`、`DELETE`、`HEAD` 和 `OPTIONS`。

相同的路径可以接受复数的操作类型。例如 `GET /users` 是获取用户列表而 `POST /users` 是添加一个新用户。Swagger 定义的每一个唯一的操作是由路径和操作方法结合而成的。这意味着不可以为同一个路径添加重复的操作方法——即使它们的参数并不一样（参数的异同并不影响到它独特性的判断）。

下面是一个最简单的例子：

```YAML
paths:
  /ping:
    get:
      responses:
        200:
          description: OK
```

更详细的参数和响应模式示例：

```YAML
paths:
  /users/{id}:
    get:
      summary: Gets a user by ID.
      description: >
        A detailed description of the operation.
        GitHub Flavored Markdown can be used for rich text representation,
        such as **bold**, *italic* and [links](http://swagger.io).
      operationId: getUsers
      tags:
        - users
      produces:
        - application/json
        - application/xml
      parameters:
        - name: id
          in: path
          description: User ID
          type: integer
          required: true
      responses:
        200:
          description: OK
          schema:
            $ref: '#/definitions/User'
      externalDocs:
        url: http://api.example.com/docs/user-operations/
        description: Learn more about User operations provided by this API.
definitions:
  User:
    type: object
    properties:
      id:
        type: integer
      name:
        type: string
    required:
      - id
      - name
```

出于文档方面的目的，操作定义支持一些可选参数：

- 简短的 `summary` 和 更详细的 `description` 都是用于描述这个端口要做什么的。`description` 支持 [多行](http://stackoverflow.com/questions/3790454/in-yaml-how-do-i-break-a-string-over-multiple-lines) 和支持 [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/) 来进行富文本内容呈现。
- `tags` 是用于在 Swagger UI 中为操作分组的。
- `externalDocs` 允许引用包含其他文档的外部资源。

## Operation Parameters(操作参数)

Swagger 允许通过路径传参、查询字符串、请求头部和请求体，更多内容查看 [描述参数]()

## operationId(操作id)

每个操作可以指定一个唯一的 `operationId`。有些代码生成器使用这个值来命名代码中的相应方法。

```YAML
/users:
     get:
       operationId: getUsers
       summary: Gets all users.
       ...
     post:
       operationId: addUser
       summary: Adds a new user.
       ...
   /user/{id}:
     get:
       operationId: getUserById
       summary: Gets a user by user ID.
       ...
```

## Query String in Paths(路径中的查询字符串)

查询字符串不能够被定义在路径中，它们应该在 `query parameters` 中被定义。

不正确示例：

```YAML
paths:
  /users?role={role}:
```

正确示例：

```YAML
paths:
  /users:
    get:
      parameters:
        - in: query
          name: role
          type: string
          enum: [user, poweruser, admin]
          required: false
```

这意味着不可能有两个只因为查询字符串不同而不同的路径，例如：

```
GET /users?firstName=value&lastName=value
GET /users?role=value
```

这是因为 Swagger 是从唯一的 HTTP 操作方法和唯一的路径来考虑唯一性的，额外的参数并不能使操作唯一。反而，你应该像这样子来定义两个各唯一的路径：

```
GET /users/findByName?firstName=value&lastName=value
GET /users/findByRole?role=value
```

## Marking as Deprecated(标记为弃用)

你可以标记个别的操作为 `deprecated` 来表明他们在使用中应该被转换为新的：

```YAML
/pet/findByTags:
    get:
      deprecated: true
```

工具会以特定方式处理已被弃用的操作。例如 Swagger UI 会用不同的样式来显示它们：

![显示示意图](http://2434zd29misd3e4a4f1e73ki-wpengine.netdna-ssl.com/wp-content/uploads/2017/02/deprecated-operation.png)
