[返回首页](https://github.com/Runtu4378/swaggerDoc)

# Describing Responses(描述应答体)

API 规格文档需要指定所有 API 操作的 `responses`。每个操作必须拥有至少一个应答定义，通常是成功回调。一个响应一般包括它的 HTTP 状态码和在应答体或者是头部的返回数据。

下面是一个最简单的例子：

```YAML
paths:
  /ping:
    get:
      produces:
        - application/json
      responses:
        200:
          description: OK
```

## Response Media Types(响应媒体类型)

API 可以通过各种媒体类型进行返回。JSON 是数据交互中最常见的格式，不过也不是唯一一种可能。

在文档跟级别或者操作级级别使用 `produces` 关键字来定义应答的媒体类型。全局设定可以被操作级设定所覆盖。

```
produces:
  - application/json

paths:
  # This operation returns JSON - as defined globally above
  /users:
    get:
      summary: Gets a list of users.
      responses:
        200:
          description: OK
  # Here, we override the "produces" array to specify other media types
  /logo:
    get:
      summary: Gets the logo image.
      produces:
        - image/png
        - image/gif
        - image/jpeg
      responses:
        200:
          description: OK
```

*更多内容*:[MIME 类型]

## HTTP Status Codes(HTTP 状态码)

在 `responses` 配置项下，配置由状态码开始，例如 200 或者 404。操作一般返回的会有一个成功状态和若干个失败状态。

每个响应状态需要一个 `description`。举个例子，你可以对失败状态进行描述，[GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/) 可以用来进行富文本的展示。

```YAML
      responses:
        200:
          description: OK
        400:
          description: Bad request. User ID must be an integer and bigger than 0.
        401:
          description: Authorization information is missing or invalid.
        404:
          description: A user with the specified ID was not found.
```

需要注意的是 API 规格文档未必需要对所有可能出现的 HTTP 状态都进行覆盖性的描述，因为有些状态是不可能提前被预知的。即便如此，你应该列出所有可能出现的和已知的错误状态情况。“已知的错误”的意思是，举个例子，根据 ID 获取资源出现的 404 状态，或者在传入无效参数的时候出现的 400 状态。

## Response Body(响应体)

`schema` 是用于描述响应体的，该属性可以指定：

- `object` 或者 `array` —— 通常和 JSON 和 XML API 一起使用。
- 原始数据类型例如 string 或者 number —— 用于纯文本响应。
- `file` （下面进行说明）

Schema 可以在操作中内联定义：

```YAML
      responses:
        200:
          description: A User object
          schema:
            type: object
            properties:
              id:
                type: integer
                description: The user ID.
              username:
                type: string
                description: The user name.
```

或者在根级别下定义，然后通过 `$ref` 进行引用。这在多个响应都用到同一种数据结构的时候十分有用。

```YAML
      responses:
        200:
          description: A User object
          schema:
            $ref: "#/definitions/User"
definitions:
  User:
    type: object
    properties:
      id:
        type: integer
        description: The user ID.
      username:
        type: string
        description: The user name.
```

## Response That Returns a File(响应返回文件)

API 操作可以返回文件，例如 image 或者 PDF。在这种情况下，定义 `schema` 为 `type:file` 和在 `produces` 中指定合适的 MIME 类型。

```YAML
paths:
  /report:
    get:
      summary: Returns the report in the PDF format.
      produces:
        - application/pdf
      responses:
        200:
          description: A PDF file.
          schema:
            type: file
```

## Empty Response Body(空响应体)

有时候响应是没有响应体的，例如：

- 204 No Content
- 201 Created(创建的资源在 URL 的头部返回而不是在正文中返回)

只要不定义响应的 `schema` 属性，Swagger 就会将这个响应状态处理为无响应体的响应。

```YAML
      responses:
        204:
          description: The resource was deleted successfully.
```

## Response Headers(响应头部)

API 请求可以自定义头部以通过这样来返回额外的信息。举个例子，一个限速的 API 请求可能会像这样在响应头部返回速率限制的信息：

```
HTTP 1/1 200 OK
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 2016-10-12T11:00:00Z

{ ... }
```

你可以像这样在每个响应中定义自定义头部：

```YAML
paths:
  /ping:
    get:
      summary: Checks if the server is alive.
      responses:
        200:
          description: OK
          headers:
            X-RateLimit-Limit:
              type: integer
              description: Request limit per hour.
            X-RateLimit-Remaining:
              type: integer
              description: The number of requests left for the time window.
            X-RateLimit-Reset:
              type: string
              format: date-time
              description: The UTC date/time at which the current rate limit window resets.
```

需要注意的是，目前 Swagger 还无法为不同的响应码和不同的 API 操作定义通用的响应头部。你需要为每个响应单独去定义它们的自定义头部。

## Default Response(默认响应)

有时候，一个操作可能会返回多种不同的 HTTP 状态码，但是它们可能会拥有相同的响应结构：

```YAML
      responses:
        200:
          description: Success
          schema:
            $ref: "#/definitions/User"
        400:
          description: Bad request
          schema:
            $ref: "#/definitions/Error"    # <-----
        404:
          description: Not found
          schema:
            $ref: "#/definitions/Error"    # <----- 
```

你可以用 `default` 响应来统一定义这些响应状态而不是各个去单独定义。"Default" 意思是该响应结构被用于这个操作所未单独定义的其他所有状态码。

```YAML
      responses:
        200:
          description: Success
          schema:
            $ref: '#/definitions/User'
        # Definition of all error statuses
        default:
          description: Unexpected error
          schema:
            $ref: '#/definitions/Error'
```

## Reusing Responses(重用响应)

如果多个操作都会返回相同的应答（状态码相同而且数据相同），你可以在全局的 `responses` 字段中进行定义，在每个操作的内联定义中通过 `$ref` 来进行引用。这对于拥有相同状态码和相同返回内容的响应来说十分有用。

```YAML
paths:
  /users:
    get:
      summary: Gets a list of users.
      response:
        200:
          description: OK
          schema:
            $ref: "#/definitions/ArrayOfUsers"
        401:
          $ref: "#/responses/Unauthorized"   # <-----
  /users/{id}:
    get:
      summary: Gets a user by ID.
      response:
        200:
          description: OK
          schema:
            $ref: "#/definitions/User"
        401:
          $ref: "#/responses/Unauthorized"   # <-----
        404:
          $ref: "#/responses/NotFound"       # <-----
# Descriptions of common responses
responses:
  NotFound:
    description: The specified resource was not found
    schema:
      $ref: "#/definitions/Error"
  Unauthorized:
    description: Unauthorized
    schema:
      $ref: "#/definitions/Error"
definitions:
  # Schema for error response body
  Error:
    type: object
    properties:
      code:
        type: string
      message:
        type: string
    required:
      - code
      - message
```

需要注意的是在根级别定义的响应并不会自动应用到所有响应中，它只是一个可以被多个操作重用的定义集合。

## FAQ

### Can I have different responses based on a request parameter? Such as:(我可以根据操作的传参不同而返回不同的内容吗)

```
GET /something -> {200, schema_1}
GET /something?foo=bar -> {200, schema_2}
```

不，这不支持。

## Reference(参考)

Swager 的相应规范：

[https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#responsesDefinitionsObject](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#responsesDefinitionsObject)

[https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#responsesObject](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#responsesObject)

[https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#responseObject](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#responseObject)