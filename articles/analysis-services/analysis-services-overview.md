---
title: "什么是 Azure Analysis Services | Microsoft Docs"
description: "Azure 中的 Analysis Services 简介。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 83d7a29c-57ae-4aa0-8327-72dd8f00247d
ms.service: analysis-services
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 05/26/2017
ms.author: owend
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 34726377836d00d484ca01edb098f6c7cbfa9dbf
ms.contentlocale: zh-cn
ms.lasthandoff: 06/17/2017


---
# <a name="what-is-azure-analysis-services"></a>什么是 Azure Analysis Services？
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services 基于 Microsoft SQL Server Analysis Services 中经验证的分析引擎构建，可在云中提供企业级的数据建模。 

观看此视频，了解 Azure Analysis Services 如何适应 Microsoft 的整体 BI 功能，以及将数据模型导入到云的益处。


>[!VIDEO https://channel9.msdn.com/series/Azure-Analysis-Services/Azure-Analysis-Services-overview/player]
>
>


## <a name="built-on-sql-server-analysis-services"></a>基于 SQL Server Analysis Services
Azure Analysis Services 与用户熟悉的同一个 SQL Server Analysis Services Enterprise Edition 兼容。 Azure Analysis Services 支持 1200 或更高版本兼容级别的表格模型。 支持 DirectQuery、分区、行级别安全性、双向关系和转换。

## <a name="use-the-tools-you-already-know"></a>使用已经熟悉的工具
![BI 开发人员工具](./media/analysis-services-overview/aas-overview-dev-tools.png)

可使用与 SQL Server Analysis Services 相同的工具创建 Azure Analysis Services 数据模型。 通过使用 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) 创作并部署模型。 通过使用 [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) 管理服务器和模型数据库。 以及通过 [PowerShell](analysis-services-powershell.md) 和 [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) 模板自动处理任务 

## <a name="supports-the-latest-features"></a>支持最新功能
Azure Analysis Services 支持 1200 和 1400 预览版兼容级别的表格模型。

**表格 1200** - 首先包括在 SQL Server 2016 Analysis Services 中。1200 引入了表格对象模型 (TOM)，用于描述表、列、关系之类的模型对象。 表格对象模型通过[表格模型脚本语言 (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) 在 JSON 中公开，通过 [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) 命名空间在 AMO 数据定义语言中公开。

**表格 1400 预览版** - 引入了对详细信息行、对象级安全性的支持、不规则层次结构支持、针对数据连接的新式“获取数据”体验，以及许多其他的增强功能。 若要充分利用所有最新的功能，需使用最新的 [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)。 由于表格 1400 仍为预览版，因此很多东西变化很快。 若要获取最新内容，请查看我们的[博客文章](https://azure.microsoft.com/blog/1400-models-in-azure-as/)。

## <a name="data-sources"></a>数据源
部署到 Azure 中服务器的数据模型支持连接到组织或云中的本地数据源。 为混合 BI 解决方案合并来自本地和云数据源的数据。

由于服务器处于云中，因此可无缝连接到云数据源。 连接到本地数据源时，[本地数据网关](analysis-services-gateway.md)可确保快速、安全地连接到云中的服务器。 若要详细了解哪些本地数据源受支持，请参阅 [Azure Analysis Services 中支持的数据源](analysis-services-datasource.md)。


## <a name="explore-your-data-from-anywhere"></a>随时随地浏览数据
随时随地连接服务器并从服务器获取数据。 Azure Analysis Services 支持从 Power BI Desktop、Excel、自定义应用和基于浏览器的工具连接。

![数据可视化](./media/analysis-services-overview/aas-overview-visualization.png)


## <a name="secure"></a>安全
#### <a name="user-authentication"></a>用户身份验证
Azure Analysis services 的用户身份验证通过 [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md) 处理。 用户尝试登录 Azure Analysis Services 数据库时，使用组织帐户标识，该标识对用户尝试访问的数据库具有访问权限。 这些用户标识必须是 Azure Analysis Services 服务器所在订阅的默认 Azure Active Directory 的成员。 AAD 与本地 Active Directory 之间的[目录集成](https://technet.microsoft.com/library/jj573653.aspx)是一种让用户访问 Azure Analysis Services 数据库的有效方法，但并非所有方案都需要此方法。

用户可通过其帐户的用户主体名称 (UPN)和密码登录。 与本地 Active Directory 同步时，用户的 UPN 通常是其组织的电子邮件地址。

通过将用户分配到 Azure 订阅中的角色来处理 Azure Analysis Services 服务器资源的管理权限。 默认情况下，订阅管理员在 Azure 中具有对服务器资源的所有者权限。 可通过 Azure Resource Manager 添加其他用户。

#### <a name="data-security"></a>数据安全
Azure Analysis Services 使用 Azure Blob 存储来持久保留 Analysis Services 数据库的存储和元数据。 使用 Azure Blob 服务器端加密 (SSE) 加密 Blob 中的数据文件。 使用“直接查询”模式时，仅存储元数据。 实际数据是在查询时从数据源访问的。

#### <a name="on-premises-data-sources"></a>本地数据源
通过安装和配置[本地数据网关](analysis-services-gateway.md)，可以实现对组织内本地驻留数据的安全访问。 网关提供在直接查询和内存模式下的数据访问。 当 Azure Analysis Services 模型连接到本地数据源时，将创建查询以及本地数据源的加密凭据。 网关云服务分析该查询，并将请求推送到 Azure 服务总线。 本地网关会针对挂起的请求轮询 Azure 服务总线。 然后，网关会获取查询，对凭据进行解密，然后连接到数据源开始执行。 随后，结果会从数据源返回到网关，然后返回到 Azure Analysis Services 数据库。

Azure Analysis Services 受 [Microsoft 联机服务条款](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)和 [Microsoft 联机服务隐私声明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)约束。
有关 Azure 安全性的详细信息，请参阅 [Microsoft 信任中心](https://www.microsoft.com/trustcenter/Security/AzureSecurity)。

## <a name="get-help"></a>获取帮助

### <a name="documentation"></a>文档
Azure Analysis Services 的设置和管理非常简单。 用户可以在这里找到创建和管理服务器所需的所有信息。 在创建要部署到服务器的数据模型时，过程与创建部署到本地服务器的数据模型的非常相似。 [Analysis Services](https://docs.microsoft.com/sql/analysis-services/analysis-services) 中提供了内容丰富的概念、过程、教程和参考文章库。

### <a name="videos"></a>视频
可在[第 9 频道上的 Azure Analysis Services](https://channel9.msdn.com/series/Azure-Analysis-Services) 查看帮助视频。

### <a name="blogs"></a>博客
信息会不断更新。 在 [Analysis Services 团队博客](https://blogs.msdn.microsoft.com/analysisservices/)和 [Azure 博客](https://azure.microsoft.com/blog/)上可以随时获取最新信息。

### <a name="community"></a>社区
Analysis Services 拥有一个充满活力的用户社区。 参与 [Azure Analysis Services 论坛](https://aka.ms/azureanalysisservicesforum)上的对话。

## <a name="feedback"></a>反馈
想提出建议或功能请求？ 请务必在 [Azure Analysis Services 反馈](https://aka.ms/azureanalysisservicesfeedback)中发表你的评论。

有关于文档的建议？ 可使用 Livefyre 在每篇文章底部添加意见。

## <a name="next-steps"></a>后续步骤
现在已详细了解了 Azure Analysis Services，可以开始使用了。 了解如何在 Azure 中[创建服务器](analysis-services-create-server.md)并对其[部署表格模型](analysis-services-deploy.md)。

