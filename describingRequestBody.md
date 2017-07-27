[返回首页](https://github.com/Runtu4378/swaggerDoc)

# Describing Request Body(描述请求体)

POST，PUT 和 PATCH 方法可以拥有请求体，例如 JSON 或者 XML 数据。在 Swagger 术语中，请求体称为 **请求体参数**。可以只有一个请求体参数，即使操作可能拥有其他参数（查询字符串，请求头参数，路径参数）。

**注意**：`application/x-www-form-urlencoded` 和 `multipart/form-data` 类型的请求的参数应该定义为 [表单参数]，而不是请求体参数。

请求体参数应该定义在操作的参数章节而且应该包含以下设置：

- `in: body`
- `schema` 用于描述请求体参数的数据类型和结构。请求体参数通常是对象类型，不过也可以是原始数据类型或者是数组。
- 可选的 `description`
- 参数的名字，是必须的不过会被忽略（因为只被用作文档化的目的）

## Object Payload(对象形式的参数)

许多 API 将数组作为对象传输，例如 JSON 格式。对象形式参数的 `schema` 属性必须定义 `type: object` 和对象的属性。

举个例子：`POST /users` 操作的参数是 JSON 对象：

```JSON
{
  "userName":  "Trillian",
  "firstName": "Tricia",
  "lastName":  "McMillan"
}
```

可以这样来定义：

```YAML
paths:
  /users:
    post:
      summary: Creates a new user.
      consumes:
        - application/json
      parameters:
        - in: body
          name: user
          description: The user to create.
          schema:
            type: object
            required:
              - userName
            properties:
              userName:
                type: string
              firstName:
                type: string
              lastName:
                type: string
      responses:
        201:
          description: Created
```

**注意**：请求体参数的 `name` 属性不会被显示，但是需要定义，原因是要用作生成文档。

更加模块化的方式是你将 schema 的定义转移到全局的 `definitions` 配置项里面，然后通过关键字 `$ref` 来进行引用。

```YAML
paths:
  /users:
    post:
      summary: Creates a new user.
      consumes:
        - application/json
      parameters:
        - in: body
          name: user
          description: The user to create.
          schema:
            $ref: "#/definitions/User"     # <----------
     responses:
         200:
           description: OK
definitions:
  User:           # <----------
    type: object
    required:
      - userName
    properties:
      userName:
        type: string
      firstName:
        type: string
      lastName:
        type: string
```

## Primitive Body(原始类型请求体参数)

希望通过 POST/PUT 只传递一个参数？你需要将请求体参数的类型定义为原始数据类型，比如 string 或者 number。

原始请求：

```
POST /status HTTP/1.1
Host: api.example.com
Content-Type: text/plain
Content-Length: 42

Time is an illusion. Lunchtime doubly so.
```

Swagger 定义：

```YAML
paths:
  /status:
    post:
      summary: Updates the status message.
      consumes:
        - text/plain      # <----------
    parameters:
      - in: body
        name: status
        required: true
        schema:
          type: string  # <----------
    responses:
      200:
        description: Success!
```
