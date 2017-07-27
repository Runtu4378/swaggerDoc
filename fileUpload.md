[返回首页](https://github.com/Runtu4378/swaggerDoc)

# File Upload(文件上传)

Swagger 2.0 支持 `Content-Type: multipart/form-data` 形式的文件上传。意味着你的 API 服务端需要对此操作使用 `multipart/form-data` 类型的处理。

```YAML
consumes:
   - multipart/form-data
```

操作参数使用 `formData` 格式进行描述（而非请求体参数的格式）。进行文件上传的那个参数需要被定义为 `type: file`。

```YAML
paths:
   /upload:
     post:
       summary: Uploads a file.
       consumes:
         - multipart/form-data
       parameters:
         - in: formData
           name: upfile
           type: file
           description: The file to upload.
```

上述定义对应的是以下的 HTTP 请求：

```
POST /upload
Host: example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Length: 204

------WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Disposition: form-data; name="upfile"; filename="example.txt"
Content-Type: text/plain

File contents go here.

------WebKitFormBoundaryqzByvokjOTfF9UwD--
```

Swagger UI 为文件类型的参数显示的是文件输入控件，允许用户浏览本地文件进行文件上传。

![示意图](https://2434zd29misd3e4a4f1e73ki-wpengine.netdna-ssl.com/wp-content/uploads/2017/02/swagger-ui-file-upload.png)

## Upload a File + Other Data(上传文件 + 其他数据)

文件类型的参数可以和其他表单参数一起进行提交：

```YAML
      parameters:
        - in: formData
          name: upfile
          type: file
          required: true
          description: The file to upload.
        - in: formData
          name: note
          type: string
          required: false
          description: Description of file contents.
```

对应的 HTTP 请求参数会包含多个部分：

```
POST /upload
Host: example.com
Content-Type: multipart/form-data; boundary=----WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Length: 332

------WebKitFormBoundaryqzByvokjOTfF9UwD
Content-Disposition: form-data; name="upfile"; filename="example.txt"
Content-Type: text/plain

File contents go here.

------WebKitFormBoundarysKk4Z8KcYfU3u6Cs
Content-Disposition: form-data; name="note"

Uploading a file named "example.txt"

------WebKitFormBoundaryqzByvokjOTfF9UwD--
```

## Multiple Upload(多重上传)

你可以逐一定义多个文件类型的参数：

```YAML
      parameters:
        - in: formData
          name: upfile1
          type: file
          required: true
        - in: formData
          name: upfile2
          type: file
          required: false
        - in: formData
          name: upfile3
          type: file
          required: false
```

但是不支持上传任意数量的文件（文件数组）。有一个开放性需求分支是关于解决这个问题的[https://github.com/OAI/OpenAPI-Specification/issues/254](https://github.com/OAI/OpenAPI-Specification/issues/254)。现在你可以使用二进制字符串数组作为上传任意数量文件的解决方法:

```YAML
type: array
items:
  type: string
  format: binary
```

需要注意 Swagger UI 不会为这样定义的参数显示文件上传控件。

## FAQ

### Can I upload files via PUT?(我能通过 GET 上传文件吗)

Swagger 支持通过 `Content-Type: multipart/form-data` 来上传文件，但是不关心它使用的是什么 HTTP 方法。你可以使用 POST，GET 或任意其他方法，为那个操作提供 `multipart/form-data` 描述就可以了。

不支持参数只是原始文件内容的上传，因为它们不是 `multipart/form-data` ，也就是说，Swagger 不支持下面这样的操作：

```
curl --upload-file archive.zip http://example.com/upload
```

同样需要注意的是在 Swagger UI 中只支持 POST 请求的文件上传操作，因为浏览器中 HTML 表单只支持 GET 和 POST 操作。

### Can I define the Content-Type for uploaded files?(我可以为上传的文件定义 content-type 吗)

目前不支持，你可以说某个操作接受文件类型参数，但你不能说这个文件是制定的某种结构。为此我们开了一个开放的需求：[https://github.com/OAI/OpenAPI-Specification/issues/222](https://github.com/OAI/OpenAPI-Specification/issues/222)

目前，你可以使用 [供应商扩展](https://swagger.io/docs/specification/swagger-extensions/) 来实现这个功能，例子如下：

```YAML
- in: formData
  name: zipfile
  type: file
  description: Contents of the ZIP file.
  x-mimetype: application/zip
```
