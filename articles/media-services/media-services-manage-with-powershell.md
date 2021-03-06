---
title: "使用 PowerShell 管理 Azure 媒体服务帐户"
description: "了解如何使用 PowerShell cmdlet 管理 Azure 媒体服务帐户。"
author: Juliako
manager: erikre
editor: 
services: media-services
documentationcenter: 
ms.assetid: 17a10c25-d94f-421c-b6bc-ae0958e2ac96
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2549c876ee35d6a7fa425d43e2f86d24131ca791
ms.openlocfilehash: 3d999d9e27844bc0164cc3572522b9ec022118a1


---
# <a name="manage-azure-media-services-accounts-with-powershell"></a>使用 PowerShell 管理 Azure 媒体服务帐户
> [!div class="op_single_selector"]
> * [门户](media-services-portal-create-account.md)
> * [PowerShell](media-services-manage-with-powershell.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> 若要创建 Azure 媒体服务帐户，你必须有一个 Azure 帐户。 如果你没有帐户，只需花费几分钟就能创建一个免费试用帐户。 有关详细信息，请参阅 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免费试用</a>。
> 
> 

## <a name="overview"></a>概述
本文列出了 Azure Resource Manager 框架中适用于 Azure 媒体服务 (AMS) 的 Azure PowerShell cmdlet。 这些 cmdlet 存在于 **Microsoft.Azure.Commands.Media** 命名空间。

## <a name="versions"></a>版本
**ApiVersion**：“2015-10-01”

## <a name="new-azurermmediaservice"></a>New-AzureRmMediaService
创建媒体服务。

### <a name="syntax"></a>语法
参数集：StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

参数集：StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 名称 |
| --- | --- |
| 必需？ |是 |
| 位置？ |1 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

**-Location &lt;String&gt;**

指定媒体服务的资源位置。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |2 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-StorageAccountId &lt;String&gt;**

指定与媒体服务关联的主存储帐户。

* 仅限受支持的新存储帐户（使用资源管理器 API 创建）。
* 存储帐户必须存在，并具有与媒体服务相同的位置。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |3 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 参数集名称 |StorageAccountIdParamSet |
| 接受通配符？ |false |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

指定与媒体服务关联的存储帐户。

* 仅限受支持的新存储帐户（使用资源管理器 API 创建）。
* 存储帐户必须存在，并具有与媒体服务相同的位置。
* 只能指定一个存储帐户作为主存储帐户。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |3 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 参数集名称 |StorageAccountsParamSet |
| 接受通配符？ |false |

**-Tags &lt;Hashtable&gt;**

指定与媒体服务关联的标记的哈希表。

* 示例：@{"tag1"="value1";"tag2"=:value2"}

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置？ |名为 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="set-azurermmediaservice"></a>Set-AzureRmMediaService
更新媒体服务。

### <a name="syntax"></a>语法
    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 名称 |
| --- | --- |
| 必需？ |True |
| 位置？ |1 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |False |

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

指定与媒体服务关联的存储帐户。

* 仅限受支持的新存储帐户（使用资源管理器 API 创建）。
* 存储帐户必须存在，并具有与媒体服务相同的位置。
* 只能指定一个存储帐户作为主存储帐户。

| 别名 | 无 |
| --- | --- |
| 必需？ |false |
| 位置？ |名为 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 参数集名称 |StorageAccountsParamSet |
| 接受通配符？ |false |

**-Tags &lt;Hashtable&gt;**

指定与此媒体服务关联的标记的哈希表。

* 与媒体服务关联的标记替换为客户指定的值。

| 别名 | 无 |
| --- | --- |
| 必需？ |False |
| 位置？ |名为 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="remove-azurermmediaservice"></a>Remove-AzureRmMediaService
删除媒体服务。

### <a name="syntax"></a>语法
    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |2 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |False |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService
获取资源组中所有的媒体服务或具有给定名称的媒体服务。

### <a name="syntax"></a>语法
ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>]    

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 参数集名称 |ResourceGroupParameterSet、AccountNameParameterSet |

接受通配符？   false

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |1 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 参数集名称 |AccountNameParameterSet |
| 接受通配符？ |false |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys
获取媒体服务的密钥。

### <a name="syntax"></a>语法
    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |1 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="set-azurermmediaservicekey"></a>Set-AzureRmMediaServiceKey
重新生成媒体服务的主密钥或辅助密钥。

### <a name="syntax"></a>语法
    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |1 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-KeyType &lt;KeyType&gt;**

指定媒体服务的密钥类型。

* 主密钥或辅助密钥

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |2 |
| 默认值 |无 |
| 接受管道输入？ |false |
| 接受通配符？ |false |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="sync-azurermmediaservicestoragekeys"></a>Sync-AzureRmMediaServiceStorageKeys
同步与媒体服务关联的存储帐户的存储帐户密钥。

### <a name="syntax"></a>语法
    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>parameters
**-ResourceGroupName &lt;String&gt;**

指定此媒体服务所属资源组的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |0 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-AccountName &lt;String&gt;**

指定媒体服务的名称。

| 别名 | 无 |
| --- | --- |
| 必需？ |是 |
| 位置？ |1 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**-StorageAccountId &lt;String&gt;**

指定与媒体服务关联的存储帐户。

| 别名 | ID |
| --- | --- |
| 必需？ |是 |
| 位置？ |2 |
| 默认值 |无 |
| 接受管道输入？ |true(ByPropertyName) |
| 接受通配符？ |false |

**&lt;CommandParameters&gt;**

此 cmdlet 支持以下常见参数：-Debug、-ErrorAction、-ErrorVariable、-InformationAction、-InformationVariable、-OutVariable、-OutBuffer、-PipelineVariable、-Verbose、-WarningAction 和 -WarningVariable。

### <a name="inputs"></a>输入
输入类型是可以发送到 cmdlet 的对象类型。

### <a name="outputs"></a>Outputs
输出类型是 cmdlet 发出的对象类型。

## <a name="next-step"></a>后续步骤
检查媒体服务学习路径。

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>提供反馈
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Feb17_HO3-->


