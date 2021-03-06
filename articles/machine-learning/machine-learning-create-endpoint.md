---
title: "在机器学习中创建 Web 服务终结点 | Microsoft Docs"
description: "在 Azure 机器学习中创建 Web 服务终结点"
services: machine-learning
documentationcenter: 
author: hiteshmadan
manager: padou
editor: cgronlun
ms.assetid: 4657fc1b-5228-4950-a29e-bc709259f728
ms.service: machine-learning
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/04/2016
ms.author: himad
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 700761e24565310df0792a209ce6e41699f3d0e8
ms.lasthandoff: 03/31/2017


---
# <a name="creating-endpoints"></a>创建终结点
> [!NOTE]
>  本主题介绍适用于**经典**机器学习 Web 服务的技术。
> 
> 

创建销售给客户的 Web 服务时，需要为每位客户提供训练的模型，这些模型链接到从中创建 Web 服务的实验。 此外，实验的任何更新都要有选择地应用到终结点，而不会覆盖自定义项。

若要实现此目的，使用 Azure 机器学习可为已部署的 Web 服务创建多个终结点。 Web 服务中的每个终结点都是独立处理、限制和托管的。 每个终结点唯一 URL 和身份验证密钥，可以将其分发给客户。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="adding-endpoints-to-a-web-service"></a>将终结点添加到 Web 服务
将终结点添加到 Web 服务有三种方法。

* 以编程方式
* 通过 Azure 机器学习 Web 服务
* 通过 Azure 经典门户

创建终结点后，可以通过同步 API、Batch API 和 Excel 工作表来使用它。 除了通过此 UI 来添加终结点，还可以使用终结点管理 API 以编程方式添加终结点。

> [!NOTE]
> 如果 Web 服务已添加其他终结点，则无法删除默认终结点。
> 
> 

## <a name="adding-an-endpoint-programmatically"></a>以编程方式添加终结点
可以使用 [AddEndpoint](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs) 示例代码以编程方式将终结点添加到 Web 服务。

## <a name="adding-an-endpoint-using-the-azure-machine-learning-web-services-portal"></a>使用 Azure 机器学习 Web 服务门户添加终结点
1. 在机器学习工作室的左侧导航栏中，单击“Web 服务”。
2. 在“Web 服务”仪表板的底部，单击“管理终结点”。 Azure 机器学习 Web 服务门户可打开 Web 服务的终结点页。
3. 单击“新建” 。
4. 键入新终结点的名称及说明。 终结点名称的长度必须少于或等于 24 个字符，并且必须由小写字母或数字组成。 选择日志记录级别以及是否启用示例数据。 有关日志记录的详细信息，请参阅[为机器学习 Web 服务启用日志记录](machine-learning-web-services-logging.md)。

## <a name="adding-an-endpoint-using-the-azure-classic-portal"></a>使用 Azure 经典门户添加终结点
1. 登录到 [Azure 经典门户](http://manage.windowsazure.com)，单击左侧列中的“机器学习”。 单击包含想要的 Web 服务的工作区。
   
    ![导航到工作区](./media/machine-learning-create-endpoint/figure-1.png)
2. 单击“Web 服务”。
   
    ![导航到 Web 服务](./media/machine-learning-create-endpoint/figure-2.png)
3. 单击想要的 Web 服务以查看可用终结点列表。
   
    ![导航到终结点](./media/machine-learning-create-endpoint/figure-3.png)
4. 单击页面底部的“添加终结点”。 键入名称和说明，请确保 Web 服务中无相同名称的其他终结点。 除非有特殊要求，请使用具有默认值的限制级别。 若要了解有关限制的详细信息，请参阅[缩放 API 终结点](machine-learning-scaling-webservice.md)。
   
    ![创建终结点](./media/machine-learning-create-endpoint/figure-4.png)

## <a name="next-steps"></a>后续步骤
[如何使用已发布的 Azure 机器学习 Web 服务](machine-learning-consume-web-services.md)。


