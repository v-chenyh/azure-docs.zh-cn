---
title: "使用 Azure Functions 创建无服务器 API | Microsoft Docs"
description: "如何使用 Azure Functions 创建无服务器 API"
services: functions
author: mattchenderson
manager: erikre
ms.service: functions
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 05/04/2017
ms.author: mahender
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 2b11d08b95d658ae2b0303107367549e6d0b5dff
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017

---

# <a name="create-a-serverless-api-using-azure-functions"></a>使用 Azure Functions 创建无服务器 API

本教程介绍如何使用 Azure Functions 构建高度可缩放的 API。 Azure Functions 附带了一系列内置 HTTP 触发器和绑定，方便你使用各种语言（包括 Node.JS、C# 等等）创建终结点。 在本教程中，你将自定义一个 HTTP 触发器来处理 API 设计中的特定操作。 此外，还要通过将你的 API 与 Azure Functions 代理集成并设置模拟 API，来准备扩展你的 API。 所有这些操作将在 Functions 无服务器计算环境的顶层完成，因此，你不需要考虑如何缩放资源 - 只需专注于自己的 API 逻辑。

## <a name="prerequisites"></a>先决条件 

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

本教程的余下内容将使用生成的函数。

### <a name="sign-in-to-azure"></a>登录 Azure

打开 Azure 门户。 为此，请使用你的 Azure 帐户登录到 [https://portal.azure.com](https://portal.azure.com)。

## <a name="customize-your-http-function"></a>自定义 HTTP 函数

默认情况下，HTTP 触发的函数已配置为接受任何 HTTP 方法。 此外，还有一个采用 `http://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>` 格式的默认 URL。 如果你已完成快速入门教程，`<funcname>` 可能类似于“HttpTriggerJS1”。 在本部分，你将修改该函数，以便只响应针对 `/api/hello` 路由发出的 GET 请求。 

在 Azure 门户中导航到该函数。 在左侧导航栏中选择“集成”。

![自定义 HTTP 函数](./media/functions-create-serverless-api/customizing-http.png)

使用表中指定的 HTTP 触发器设置。

| 字段 | 示例值 | 说明 |
|---|---|---|
| 允许的 HTTP 方法 | 选定的方法 | 确定可以使用哪些 HTTP 方法来调用此函数 |
| 选定的 HTTP 方法 | GET | 只允许使用选定的 HTTP 方法来调用此函数 |
| 路由模板 | /hello | 确定可以使用哪个路由来调用此函数 |

请注意，你并未在路由模板中包含 `/api` 基路径前缀，因为此操作由某个全局设置处理。

单击“保存” 。

可以在 [Azure Functions HTTP 和 Webhook 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#customizing-the-http-endpoint)中详细了解如何自定义 HTTP 函数。

### <a name="test-your-api"></a>测试 API

接下来，请测试你的函数，确定它是否适用于新的 API 图面。

在左侧导航栏中单击该函数的名称，导航回到开发页。

单击“获取函数 URL”并复制该 URL。 应会看到它现在使用了 `/api/hello` 路由。

将 URL 复制到新的浏览器标签页或偏好的 REST 客户端中。 浏览器默认使用 GET。

运行该函数并确认它是否正常工作。 可能需要提供“name”参数作为查询字符串来配合快速启动代码。

你也可以尝试使用其他 HTTP 方法调用终结点，确认是否未执行该函数。 为此，需要使用 REST 客户端，例如 cURL、Postman 或 Fiddler。

## <a name="proxies-overview"></a>代理概述

在下一部分，你将通过代理呈现 API。 Azure Functions 代理是一个预览功能，可将请求转发到其他资源。 定义 HTTP 终结点的过程与定义 HTTP 触发器类似，但调用终结点时不能写入要执行的代码，而要提供远程实现的 URL。 这样，便可以将多个 API 源组合到可方便客户端使用的单个 API 图面中。 如果你要以微服务的形式构建 API，这种做法特别有效。

代理可以指向任何 HTTP 资源，例如：
- Azure Functions 
- [Azure 应用服务](https://docs.microsoft.com/azure/app-service/app-service-value-prop-what-is)中的 API 应用
- [Linux 上的应用服务](https://docs.microsoft.com/azure/app-service/app-service-linux-readme)中的 Docker 容器
- 其他任何托管 API

若要了解有关代理的详细信息，请参阅[使用 Azure Functions 代理（预览版）]。

## <a name="create-your-first-proxy"></a>创建第一个代理

在本部分，你将创建充当整个 API 的前端的新代理。 

### <a name="setting-up-the-frontend-environment"></a>设置前端环境

重复[创建 Function App](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function#create-a-function-app) 中的步骤，创建要在其中创建代理的新 Function App。 此新应用将充当 API 的前端，之前编辑的 Function App 将充当后端。

在门户中导航到新的前端 Function App。

选择“设置”。 然后，将“启用 Azure Functions 代理(预览版)”切换为“打开”。

选择“平台设置”，然后选择“应用程序设置”。

向下滚动到“应用设置”，并创建一个包含“HELLO_HOST”键的新设置。 将其值设置为后端 Function App 的主机，例如 `<YourApp>.azurewebsites.net`。 这是前面在测试 HTTP 函数时复制的 URL 的一部分。 稍后将在配置中引用此设置。

> [!NOTE] 
> 建议在主机配置中使用应用设置，以防止对代理的环境依赖关系进行硬编码。 使用应用设置意味着可以在环境之间移动代理配置，并应用特定于环境的应用设置。

单击“保存” 。

### <a name="creating-a-proxy-on-the-frontend"></a>在前端上创建代理

在门户中导航回到前端 Function App。

在左侧导航栏中，单击“代理(预览版)”旁边的加号“+”。

![创建代理](./media/functions-create-serverless-api/creating-proxy.png)

使用表中指定的代理设置。

| 字段 | 示例值 | 说明 |
|---|---|---|
| 名称 | HelloProxy | 仅用于管理的友好名称 |
| 路由模板 | /api/hello | 确定可以使用哪个路由来调用此代理 |
| 后端 URL | https://%HELLO_HOST%/api/hello | 指定请求应代理的终结点 |

请注意，代理不提供 `/api` 基路径前缀，必须在路由模板中包含此前缀。

`%HELLO_HOST%` 语法将引用前面创建的应用设置。 解析的 URL 将指向原始函数。

单击“创建” 。

可以通过复制代理 URL 或使用偏好的 HTTP 客户端在浏览器中对其进行测试来试验新代理。

## <a name="create-a-mock-api"></a>创建模拟 API

接下来，使用代理来为解决方案创建模拟 API。 这样，客户端开发便可以继续进行，而无需完全实现后端。 以后在开发时，可以创建新的 Function App 来支持此逻辑并将代理重定向到此逻辑。

为了创建此模拟 API，我们将创建一个新代理，但这一次我们使用的是[应用服务编辑器](https://github.com/projectkudu/kudu/wiki/App-Service-Editor)。 若要开始，请在门户中导航到你的 Function App。 选择“平台功能”并找到“应用服务编辑器”。 单击该按钮会在新选项卡中打开应用服务编辑器。

在左侧导航栏中选择 `proxies.json`。 这是用于存储所有代理的配置的文件。 如果使用某种 [Functions 部署方法](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment)，则此文件是在源代码管理中维护的文件。 若要详细了解此文件，请参阅[代理高级配置](https://docs.microsoft.com/azure/azure-functions/functions-proxies#advanced-configuration)。

到目前为止，proxies.json 应如下所示：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        }
    }
}
```

接下来，请添加模拟 API。 将 proxies.json 文件替换为以下内容：

```json
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "HelloProxy": {
            "matchCondition": {
                "route": "/api/hello"
            },
            "backendUri": "https://%HELLO_HOST%/api/hello"
        },
        "GetUserByName" : {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/users/{username}"
            },
            "responseOverrides": {
                "response.statusCode": "200",
                "response.headers.Content-Type" : "application/json",
                "response.body": {
                    "name": "{username}",
                    "description": "Awesome developer and master of serverless APIs",
                    "skills": [
                        "Serverless",
                        "APIs",
                        "Azure",
                        "Cloud"
                    ]
                }
            }
        }
    }
}
```

这会添加一个不带 backendUri 属性的新代理“GetUserByName”。 此代理不会调用另一个资源，而是使用响应重写来修改代理的默认响应。 也可以将请求和响应重写与后端 URL 结合使用。 在代理需要修改标头、查询参数等元素的旧式系统时，这种做法特别有效。若要详细了解请求和响应重写，请参阅[修改代理中的请求和响应](https://docs.microsoft.com/azure/azure-functions/functions-proxies#a-namemodify-requests-responsesamodifying-requests-and-responses)。

通过使用浏览器或偏好的 REST 客户端调用 `/api/users/{username}` 终结点来测试模拟 API。 请务必将 _{username}_ 替换为表示用户名的字符串值。

## <a name="next-steps"></a>后续步骤

本教程已介绍如何在 Azure Functions 中构建和自定义 API。 此外，还介绍了如何将多个 API（包括模拟 API）合并成统一的 API 图面。 可以使用这些方法构建具有不同复杂性的 API，所有这些 API 都可在 Azure Functions 提供的无服务器计算模型上运行。

以下参考文档可以帮助你进一步开发 API：

- [Azure Functions HTTP 和 Webhook 绑定](https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook)
- [使用 Azure Functions 代理（预览版）]
- [记录 Azure Functions API（预览版）](https://docs.microsoft.com/azure/azure-functions/functions-api-definition-getting-started)


[Create your first function]: https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function
[使用 Azure Functions 代理（预览版）]: https://docs.microsoft.com/azure/azure-functions/functions-proxies
