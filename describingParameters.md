[返回首页](https://github.com/Runtu4378/swaggerDoc)

# Describing Parameters(描述参数)

在 Swagger 中，API 的操作的参数是在操作定义里面的 `parameters` 字段里面定义的。每个参数都拥有 `name`、参数的值的数据类型 `type`（给那些是原始数据类型的参数所定义的）或者是 `schema`（为请求体定义的）和可选的 `description`。下面是一个例子：

```YAML
paths:
  /users/{userId}:
    get:
      summary: Gets a user by ID.
      parameters:
        - in: path
          name: userId
          type: integer
          required: true
          description: Numeric ID of the user to get.
```

需要注意的是 `parameters` 是一个数组，所以，在 YAML 语法中，所有参数的定义前面都需要以短横线（-）作为分割。

## Parameter Types(参数类型)

Swagger 根据参数所在的位置区分参数是以下哪种类型的参数。参数的位置由参数的 `in` 键值的值所决定，比如 `in: query` 或者 `in: path`。

- [query parameters] 例如 `/users?role=admin`
- [path parameters] 例如 `/users/{id}`
- [header parameters] 例如 `X-MyHeader: Value`
- 描述 POST、PUT 和 PATCH 请求的请求体的参数（更多参考[描述请求体]()）
- [form parameters] `Content-Type` 的值为 `application/x-www-form-urlencoded` 或 `multipart/form-data` （后者一般用于[文件上传](https://swagger.io/docs/specification/file-upload)）的一种请求体参数

## Query Parameters(查询参数)

查询参数是最常见的参数类型。它们出现在请求 URL 的结尾的问号（？）后面，以不同的 `name=value` 键值对作为组合，键值对之间以 `&` 分隔。查询参数可以是必填的，也可以是可选的。

```
GET /pets/findByStatus?status=available
GET /notes?offset=100&limit=50
```

使用 `in: query` 来表示查询参数：

```YAML
     parameters:
        - in: query
          name: offset
          type: integer
          description: The number of items to skip before starting to collect the result set.
        - in: query
          name: limit
          type: integer
          description: The numbers of items to return.
```

查询参数只支持原始数据类型，你可以有一个数组，不过它的元素 `items` 必须是原始数据类型的，查询参数不支持对象。

**注意**：要描述作为查询参数传递的 API 键，请改用安全定义，具体查看 [API 键](https://swagger.io/docs/specification/authentication/api-keys/)

## Path Parameters(路径参数)

路径参数是 URL 路径当中可变化的那一部分。它们通常用于指定集合中的某个资源，比如由 ID 识别出某个用户。一个 URL 可以有多个路径参数，每个路径参数周围被大括号包裹着（{ }）。

```
GET /users/{id}
GET /cars/{carId}/drivers/{driverId}
```

当 API 客户端发起请求时，每个路径参数的位置都必须填充上实际的值，

在 Swagger 中，使用 `in: path` 来定义路径参数，路径参数的其他属性同样也是必须的。参数的 `name` 属性必须和路径定义中所规定的一致。同样的，记得要加上 `required: true` 因为路径参数总是必须的。

下面是 `GET /users/{id}` 的例子：

```YAML
paths:
  /users/{id}:
    get:
      parameters:
        - in: path
          name: id   # Note the name is the same as in the path
          required: true
          type: integer
          minimum: 1
          description: The user ID.
       responses:
         200:
           description: OK
```

路径参数可以是 `multi-valued` 的，例如 `GET /users/12,34,56`。这是通过定义该参数的类型为 `array` 来实现的。具体可以查看下面的 [Array and Multi-Value Parameters ]

## Header Parameters(头部参数)

一个 API 请求可能会使用自定义请求头来传输。Swagger 允许你通过设置 `in: header` 的头部参数来设定自定义请求头。

举个例子，假设，使用 `X-Request-ID` 头部来调用 `GET /ping`:

```YAML
GET /ping HTTP/1.1
Host: example.com
X-Request-ID: 77e1c83b-7bb0-437b-bc50-a7a58e5660ac
```

在 Swagger 中，你可能需要像下面这样来定义上述操作：

```YAML
paths:
  /ping:
    get:
      summary: Checks if the server is alive.
      parameters:
        - in: header
          name: X-Request-ID
          type: string
          required: true
```

使用类似的方法，你可以设置 [自定义响应头部

**注意**：Swagger 对一些头部字段使用了特殊的关键字来设定：

头部字段 | Swagger 关键字 | 更多知识
--- | --- | ---
Content-Type | 	consumes, produces | [MIME 类型](https://github.com/Runtu4378/swaggerDoc/blob/master/mimeTypes.md)
Authorization | 	securityDefinitions, security | [认证]

## Form Parameters(表单参数)

表单参数是使用以下 `Content-Type` 来描述的请求体参数：

- `application/x-www-form-urlencoded`（用于传递原始数据类型的数据或者是由原始数据类型构成的数组）
- `multipart/form-data`（用于上传文件或者是原始数据类型数据和文件的集合）

所以，`consumes` 需要设置为上述值的其中一种。

表单参数由 `in: formData` 来定义，它们的数据类型只能是原始类型（strings、numbers、booleans）或者是原始类型数据的数组（意味着你不能用 `$ref` 来作为 `items` 的值）。同样的，表单参数无法和 `in: body` 共存，因为 `formData` 是描述请求体参数的一个具体子集。

为了说明表单参数，我们定义了一个 HTML 上传表单：

```HTML
<form action="http://example.com/survey" method="post">
  <input type="text"   name="name" />
  <input type="number" name="fav_number" />
  <input type="submit"/>
</form>
```

该表单将数据 POST 到表单的端点：

```
POST /survey HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

name=Amy+Smith&fav_number=321
```

在 Swagger，你可以这样子来编辑规格文档：

```YAML
paths:
  /survey:
    post:
      summary: A sample survey.
      consumes:
        - application/x-www-form-urlencoded
      parameters:
        - in: formData
          name: name
          type: string
          description: A person's name.
        - in: formData
          name: fav_number
          type: number
          description: A person's favorite number.
      responses:
        200:
          description: OK
```

想要了解如何通过表单参数进行文件上传，查看 [文件上传]

## Required and Optional Parameters(必填参数和可选参数)

在默认状态下，Swagger 将所有参数处理为可选参数。你可以添加 `required: true` 来标记那些必填参数。需要注意的是路径参数必须拥有 `required: true` 属性，因为路径参数总是必须的。

```
      parameters:
        - in: path
          name: userId
          type: integer
          required: true    # <----------
          description: Numeric ID of the user to get.
```

## Default Parameter Values(参数默认值)

你可以为可选参数设置 `default` 值来指定它的默认值。默认值是当 API 客户端没有对某个参数进行传入时后台所使用的值，所设定的值的数据类型必须和该参数设定的数据类型一致。

一个典型的例子是分页类参数中的 offset 和 limit：

```
GET /users
GET /users?offset=30&limit=10
```

假设 offset 默认为 0，limit 默认为 20，ranges 范围为 0 到 100，你可以这样定义这些参数：

```YAML
      parameters:
        - in: query
          name: offset
          type: integer
          required: false
          default: 0
          minimum: 0
          description: The number of items to skip before starting to collect the result set.
        - in: query
          name: limit
          type: integer
          required: false
          default: 20
          minimum: 1
          maximum: 100
          description: The numbers of items to return.
```

## Common Mistakes(常见错误)

在使用 `default` 关键字的时候有两种常见错误：

- 对必填参数设定 `default` 属性，例如路径参数。这没有意义——因为如果某个值是必填的话，客户端必须要传入该值，这样的话默认值永远都不会被用上。
- 使用 `default` 来指定样本数据。这不是默认的用法，可能会导致一些工具触发意外的行为。规范的使用是使用 `example` 或者 `examples` 参数来实现这个目的。

## Enum Parameters(枚举参数)

`enum` 参数允许你将某个参数限制为固定集合内的某个值。枚举参数的值需要符合其所设定的 `type`。

```YAML
        - in: query
          name: status
          type: string
          enum: [available, pending, sold]
```

更多内容：[定义枚举参数]

## Array and Multi-Value Parameters(数组和多值参数)

路径参数、查询参数、头部参数和表单参数可以接受列表形式的值，例如：

```
GET /users/12,34,56,78
GET /resource?param=value1,value2,value3
GET /resource?param=value1&param=value2&param=value3

POST /resource
param=value1&param=value2
```

多值参数需要定义 `type: array` 和适当的 `collectionFormat`：

```YAML
      # color=red,black,white
      parameters:
        - in: query
          name: color
          type: array
          collectionFormat: csv
          items:
            type: string
```

`collectionFormat` 所指定的是数组的格式（具有多个参数的单个参数或具有相同名称的多个参数）和数组元素的分隔符。

collectionFormat值 | 描述 | 例子
--- | --- | ---
csv(默认值) | 以逗号分隔值 | 	foo,bar,baz
ssv | 以空格分隔值 | 	foo bar baz
tsv	| 反斜杠分隔值	| "foo\tbar\tbaz"
pipes	| 以竖线分隔值 | foo|bar|baz
multi | 多参数实例而不是多值。该选项只支持 `in: query` 和 `in: formData` 的参数 | 	foo=value&foo=another_value

另外，你可以：

- 使用 `minItems` 和 `maxItems` 来控制数组的尺寸。
- 实行限制 `uniqueItems`。
- 限制数据元素为枚举方式 `enum` 值。

举个例子：

```YAML
        - in: query
          name: color
          required: false
          type: array
          minItems: 1
          maxItems: 5
          uniqueItems: true
          items:
            type: string
            enum: [black, white, gray, red, pink, orange, yellow, green, blue, purple, brown]
```

你同样可以设置默认数组，这样在该值被忽略时可使用默认值：

```YAML
        - in: query
          name: sort
          required: false
          type: array
          items:
            type: string
          default: ["-modified", "+id"]
```

## Constant Parameters(常数参数)

你可以定义某个必须参数为常数参数，常数参数只有唯一可能的值：

```YAML
- required: true
  enum: [value]
```

枚举参数是指定可能出现的值的。在这个例子中，只有一个例子可以被使用，所以在 Swagger UI 中只会有一个值可以被用户选择。

**注意**：常数参数和 [参数默认值] 是不一样的概念，常数参数只是总是被客户端发送为这样一个值而已，而参数默认值是当客户端不发送该值时才会生效的值。

## Parameters Without a Value(没有值的参数)

查询字符串和表单数据参数可能会只有参数名而没有值：

```
GET /foo?metadata

POST /something
foo&bar&baz
```

使用 `allowEmptyValue` 来描述这种参数：

```YAML
        - in: query
          name: metadata
          required: true
          type: boolean
          allowEmptyValue: true  # <-----
```

## Common Parameters(共同参数

### Common Parameters for All Methods of a Path(路径所有方法的常用参数)

参数可以在一个路径下被单独定义，在这种情况下，该参数存在于定义在本路径下的所有操作方法中。典型的例子是 GET/PUT/PATCH/DELETE 操作都需要访问同一个资源如路径参数：

```YAML
paths:
  /user/{id}:
    parameters:
      - in: path
        name: id
        type: integer
        required: true
        description: The user ID.

    get:
      summary: Gets a user by ID.
      ...
    patch:
      summary: Updates an existing user with the specified ID.
      ...
    delete:
      summary: Deletes the user with the specified ID.
      ...
```

在操作级别定义的任何额外参数与路径级参数一起使用：

```YAML
paths:
  /users/{id}:
    parameters:
      - in: path
        name: id
        type: integer
        required: true
        description: The user ID.

    # GET/users/{id}?metadata=true
    get:
      summary: Gets a user by ID.
      # Note we only define the query parameter, because the {id} is defined at the path level.
      parameters:
        - in: query
          name: metadata
          type: boolean
          required: false
          description: If true, the endpoint returns only the user metadata.
      responses:
        200:
          description: OK
```

可以在操作级别覆盖特定的路径级别所定义的参数，但无法将其移除。

```YAML
paths:
  /users/{id}:
    parameters:
      - in: path
        name: id
        type: integer
        required: true
        description: The user ID.

    # DELETE /users/{id} - uses a single ID.
    # Reuses the {id} parameter definition from the path level.
    delete:
      summary: Deletes the user with the specified ID.
      responses:
        204:
          description: User was deleted.

    # GET /users/id1,id2,id3 - uses one or more user IDs.
    # Overrides the path-level {id} parameter.
    get:
      summary: Gets one or more users by ID.
      parameters:
        - in: path
          name: id
          required: true
          description: A comma-separated list of user IDs.
          type: array
          items:
            type: integer
          collectionFormat: csv
          minItems: 1
      responses:
        200:
          description: OK
```

### Common Parameters in Different Paths(不同路径中的公共参数)

不同的API路径可能具有一些常见参数，如分页参数。您可以在全局参数部分中定义公共参数 `parameters`，并通过 `$ref` 在各个操作中引用它们。

```YAML
parameters:
  offsetParam:  # <-- Arbitrary name for the definition that will be used to refer to it.
                # Not necessarily the same as the parameter name.
    in: query
    name: offset
    required: false
    type: integer
    minimum: 0
    description: The number of items to skip before starting to collect the result set.
  limitParam:
    in: query
    name: limit
    required: false
    type: integer
    minimum: 1
    maximum: 50
    default: 20
    description: The numbers of items to return.
paths:
  /users:
    get:
      summary: Gets a list of users.
      parameters:
        - $ref: '#/parameters/offsetParam'
        - $ref: '#/parameters/limitParam'
      responses:
        200:
          description: OK
  /teams:
    get:
      summary: Gets a list of teams.
      parameters:
        - $ref: '#/parameters/offsetParam'
        - $ref: '#/parameters/limitParam'
      responses:
        200:
          description: OK
```

需要注意的是全局定义的参数 `parameters` 并非应用到全部操作的参数——它们只是全局定义的方便于重复引用参数定义。

## Parameter Dependencies(参数依赖性)

Swagger 不支持参数依赖和互斥参数。有一个开放的功能需求在 [https://github.com/OAI/OpenAPI-Specification/issues/256](https://github.com/OAI/OpenAPI-Specification/issues/256)。

您可以做的是记录参数描述中的限制，并定义400错误请求响应中的逻辑。

举个例子，`/report` 接受相对日期范围（rdate）或精确范围的日期范围参数（start_date + end_date）：

```
GET /report?rdate=Today
GET /report?start_date=2016-11-15&end_date=2016-11-20
```

你可以这样子描述这个端点：

```YAML
paths:
  /report:
    get:
      parameters:
        - name: rdate
          in: query
          type: string
          description: >
             A relative date range for the report, such as `Today` or `LastWeek`.
             For an exact range, use `start_date` and `end_date` instead.
        - name: start_date
          in: query
          type: string
          format: date
          description: >
            The start date for the report. Must be used together with `end_date`.
            This parameter is incompatible with `rdate`.
        - name: end_date
          in: query
          type: string
          format: date
          description: >
            The end date for the report. Must be used together with `start_date`.
            This parameter is incompatible with `rdate`.
      responses:
        400:
          description: Either `rdate` or `start_date`+`end_date` are required.
```

## FAQ

### When should I use “type” vs “schema”?(什么时候应该使用 type 或者 schema?)

schema 只能被用在 `in: body` 的参数。其他类型的参数都只接受原始数据类型的数据，例如 `type: string`，或者原始数据对象。

### Can I have an object as a query parameter?(我可以有一个对象作为查询对象吗？)

不可以。查询参数只支持原始数据类型或是原始数据类型的数组。
