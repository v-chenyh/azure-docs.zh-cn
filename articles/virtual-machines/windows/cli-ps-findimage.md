---
title: "导航和选择 Windows VM 映像 | Microsoft Docs"
description: "了解在使用 Resource Manager 部署模型创建 Windows 虚拟机时如何确定映像的发布者、产品和 SKU。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: danlep
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 28bb214570fcca94c5ceb6071c4851b81ec00c8d
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---
<a id="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell" class="xliff"></a>

# 使用 PowerShell 在 Azure 中导航和选择 Windows 虚拟机映像
本主题介绍如何查找每个可能部署到目标位置的 VM 映像发布者、产品、SKU 和版本。 举例来说，某些常用 Windows VM 映像包括：

<a id="table-of-commonly-used-windows-images" class="xliff"></a>

## 常用 Windows 映像表
| PublisherName | 产品 | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

<a id="find-azure-images-with-powershell" class="xliff"></a>

## 使用 PowerShell 查找 Azure 映像
> [!NOTE]
> 下载并配置[最新的 Azure PowerShell](/powershell/azure/overview)。 如果使用低于 1.0 版本的 Azure PowerShell 模块，则仍使用以下命令，但必须先执行 `Switch-AzureMode AzureResourceManager`。 
> 
> 

使用 Azure 资源管理器创建新的虚拟机时，在某些情况下，你需要使用以下映像属性组合来指定映像：

* 发布者
* 产品
* SKU

例如，`Set-AzureRMVMSourceImage` PowerShell cmdlet 或资源组模板文件需要这些值，必须在此命令或文件中指定要创建的虚拟机类型。

如果你需要确定这些值，可以浏览映像以确定这些值：

1. 列出映像发布者。
2. 对于给定的发布者，列出其产品。
3. 对于给定的产品，列出其 SKU。

首先，使用以下命令列出发布者：

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

填写选择的发布者名称，然后运行以下命令：

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

填写选择的产品名称，然后运行以下命令：

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

从 `Get-AzureRMVMImageSku` 命令的输出来看，你已获得了为新虚拟机指定映像所需的所有信息。

下面是一个完整示例：

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

对于“MicrosoftWindowsServer”发布者：

```powershell
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

对于“WindowsServer”产品：

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

从上面的列表中复制选择的 SKU 名称，你已获得 `Set-AzureRMVMSourceImage` PowerShell cmdlet 或资源组模板的所有信息。

<a id="next-steps" class="xliff"></a>

## 后续步骤
现在，你可以确切地选择想要使用的映像。 若要使用刚刚找到的映像信息快速创建虚拟机，或要使用包含该映像信息的模板，请参阅[使用 Resource Manager 和 PowerShell 创建 Windows VM](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

