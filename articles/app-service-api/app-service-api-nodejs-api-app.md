---
title: "Azure 应用服务中的 Node.js API 应用 | Microsoft Docs"
description: "了解如何创建 Node.js RESTful API 并将其部署到 Azure 应用服务中的 API 应用。"
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 06/13/2017
ms.author: rachelap
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f68f90c3aea337d7b61b43e637bcfda3c98f3ea
ms.openlocfilehash: 19d6d0e1df185746a8ea289db9ac18e5614c09c7
ms.contentlocale: zh-cn
ms.lasthandoff: 06/20/2017


---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>构建 Node.js RESTful API 并将它部署到 Azure 中的 API 应用
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

此快速入门教程演示如何使用 [Swagger](http://swagger.io/) 定义创建 [Express](http://expressjs.com/) 框架 Node.js REST API，并在 Azure 上将其部署为 [API 应用](app-service-api-apps-why-best-platform.md)。 使用命令行工具创建应用，使用 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) 配置资源，并使用 Git 部署该应用。  完成后，即可拥有一个在 Azure 上运行的有效示例 REST API。

## <a name="prerequisites"></a>先决条件

* [Git](https://git-scm.com/)
* [Node.js 和 NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本主题要求运行 Azure CLI 2.0 版或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。 

## <a name="prepare-your-environment"></a>准备环境

1. 在终端窗口中，运行以下命令，将示例克隆到本地计算机。

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. 切换到包含示例代码的目录。

    ```bash
    cd app-service-api-node-contact-list
    ```

3. 在本地计算机上安装 [Swaggerize](https://www.npmjs.com/package/swaggerize-express)。 Swaggerize 是一种工具，用于从 Swagger 定义生成用于 REST API 的 Node.js 代码。

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>生成 Node.js 代码 

本教程部分为 API 开发工作流建模，将在其中先创建 Swagger 元数据，然后以此创建（自动生成）API 服务器代码基架。 

将目录更改为 start 文件夹，然后运行 `yo swaggerize`。 Swaggerize 从 api.json 中的 Swagger 定义创建用于 API 的 Node.js 项目。

    ```bash
    cd start
    yo swaggerize --apiPath api.json --framework express
    ```

     When Swaggerize asks for a project name, use *ContactList*.
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>自定义项目代码

1. 将 lib 文件夹复制到 `yo swaggerize` 创建的 ContactList 文件夹，然后将目录更改为 ContactList。

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. 安装 `jsonpath` 和 `swaggerize-ui` NPM 模块。 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. 将 handlers/contacts.js 中的代码替换为以下代码： 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    此代码使用 lib/contactRepository.js 提供的 lib/contacts.json 中存储的 JSON 数据。 新的 contacts.js 代码将存储库中的所有联系人返回为 JSON 有效负载形式。 

4. 将 handlers/contacts/{id}.js 文件中的代码替换为以下代码：

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    此代码允许使用路径变量仅返回具有给定 ID 的联系人。

5. 将 server.js 中的代码替换为以下代码：

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    此代码进行了一些小的更改，可与 Azure App Service 配合使用，并公开一个用于 API 的交互式 Web 界面。

### <a name="test-the-api-locally"></a>在本地测试 API

1. 启动 Node.js 应用
    ```bash
    npm start
    ```
    
2. 浏览到 http://localhost:8000/contacts，查看整个联系人列表的 JSON。
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. 浏览到 http://localhost:8000/contacts/2，查看具有两个 `id` 中其中一个的联系人。
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. 在 http://localhost:8000/docs 使用 Swagger Web 界面测试 API。
   
    ![Swagger Web 界面](media/app-service-api-nodejs-api-app/swagger-ui.png)

## <a id="createapiapp"></a>创建 API 应用

本部分将使用 Azure CLI 2.0 创建在 Azure App Service 上托管 API 的资源。 

1.  使用 [az login](/cli/azure/#login) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

    ```azurecli-interactive
    az login
    ```

2. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

3. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

4. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>使用 Git 部署 API

通过将提交内容从本地 Git 存储库推送到 Azure App Service，将代码部署到 API 应用。

1. [!INCLUDE [Configure your deployment credentials](../../includes/app-service-api-configure-local-git.md)] 

2. 初始化 ContactList 目录中的一个新存储库。 

    ```bash
    git init .
    ```

3. 从 Git 中排除在教程前面步骤中由 npm 创建的 node_modules 目录。 在当前目录中创建新的 `.gitignore` 文件，并在文件的任何位置添加新的以下文本行。

    ```
    node_modules/
    ```
    确认已通过 `git status` 忽略 `node_modules` 文件夹。

4. 提交存储库更改。
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>在 Azure 中测试 API

1. 打开浏览器并导航到 http://app_name.azurewebsites.net/contacts。 返回的 JSON 与本教程之前在本地提发出请求时返回的内容相同。

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. 在浏览器中转到 `http://app_name.azurewebsites.net/docs` 终结点，试用在 Azure 中运行的 Swagger UI。

    ![Swagger UI](media/app-service-api-nodejs-api-app/swagger-azure-ui.png)

    现在可通过将提交内容推送到 Azure Git 存储库，将示例 API 的更新部署到 Azure。

## <a name="clean-up"></a>清理

若要清除此快速入门中创建的资源，请运行以下 Azure CLI 命令：

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>后续步骤 
> [!div class="nextstepaction"]
> [借助 CORS 从 JavaScript 客户端使用 API 应用](app-service-api-cors-consume-javascript.md)


