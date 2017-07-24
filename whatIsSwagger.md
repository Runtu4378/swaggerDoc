# 什么是 Swagger？

Swagger 允许您对 API 的结构进行描述，以便机器可以读取它们。

让 API 能描述它们自身的结构是 Swagger 最吸引人的地方。为什么这么说呢？因为 Swagger 通过读取您编写的 API 结构文档去生成美观和可互动（即时调试）的 API 文档。我们还可以为您的 API 生成多种语言的客户端库，或者例如自动测试等更为宽阔的可能性。

Swagger 通过要求您的 API 返回包含整个 API 的详细描述的 YAML 或者 JSON 文件来实现上述功能。该文件实质上是您的 API 的资源列表，它遵循 [OpenAPI](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) 规范。该规范要求您在文件中提供以下信息：

- 您的 API 支持的所有操作是什么？
- 您的 API 的参数是什么，它返回什么？
- 您的 API 需要哪些授权信息吗？
- 其他有趣的小东西如条款，联系信息和使用 API 的许可证。

您可以手动为您的 API 编写 Swagger 规格文档，也可以由源代码中的注释自动生成。查看 [Swagger 开源工具列表](https://swagger.io/open-source-integrations/) 来寻找你可以用来从源代码生成 Swagger 的工具。

# 所以我现在已经有一份 Swagger 规格文档了，我该怎么进行下一步？

Swagger 有几种方法可以帮助您进行更进一步的推进 API 开发：

- 设计先行的用户：可使用 [Swagger Codegen](https://swagger.io/swagger-codegen/) 来为您的 API 生成**服务器存根**。唯一剩下的就是实现服务器逻辑——因为您的 API 已经准备好了。
- 使用 [Swagger Codegen](https://swagger.io/swagger-codegen/) 来生成支持多达 40 种语言的**客户端库**。
- 使用 [Swagger UI](https://swagger.io/swagger-ui/) 来生成**交互式 API 文档**，这样您的开发者可以通过浏览器的直接调用来测试您的 API。
- 使用 API 相关工具来调用您的 API 规格文档。例如在 [SoapUI](https://soapui.org/) 中引用规格文档来为您的 API 创建自动测试。
- 更多使用方法可以查看 [开源工具](https://swagger.io/open-source-integrations/) 和 [商业工具](https://swagger.io/commercial-tools/) 来查找可以整合到 Swagger 的工具。