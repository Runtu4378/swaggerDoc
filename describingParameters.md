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
- [form parameters] 其中