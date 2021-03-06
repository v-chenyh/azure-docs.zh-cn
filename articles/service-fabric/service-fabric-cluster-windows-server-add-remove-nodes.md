---
title: "向独立 Service Fabric 群集添加或删除节点 | Microsoft 文档"
description: "了解如何向运行 Windows Server 的本地或任意云中物理计算机或虚拟机上的 Azure Service Fabric 群集添加节点。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/02/2017
ms.author: chackdan
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: fa59bae2b824e6b75e120ab2b61027746ee1ea78
ms.lasthandoff: 04/27/2017


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>向在 Windows Server 上运行的独立 Service Fabric 群集添加或删除节点
[在 Windows Server 计算机上创建独立 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)之后，你的业务需求可能会有改变，因此可能需要向群集添加或删除多个节点。 本文提供了实现此目标的详细步骤。

## <a name="add-nodes-to-your-cluster"></a>向群集添加节点
1. 按照[准备计算机以满足群集部署的先决条件](service-fabric-cluster-creation-for-windows-server.md)部分中所提及的步骤，准备要添加到群集中的 VM/计算机。
2. 规划要向哪些容错域和升级域添加此 VM/计算机。
3. 通过远程桌面 (RDP) 方式进入需要向群集添加的 VM/计算机。
4. 向此 VM/计算机复制或[下载适用于 Windows Server 的 Service Fabric 独立包](http://go.microsoft.com/fwlink/?LinkId=730690)，并解压该包。
5. 以管理员身份运行 Powershell，并导航到解压缩包所在的位置。
6. 使用描述要添加的新节点的参数运行 *AddNode.ps1* Powershell。 以下示例将名为 VM5、类型为 NodeType0 且 IP 地址为 182.17.34.52 的新节点添加到 UD1 和 fd:/dc1/r0 中。 *ExistingClusterConnectionEndPoint* 是现有群集中已有节点的连接终结点。 对于此终结点，可以选择群集中*任何*节点的 IP 地址。

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA

```
可以检查新节点是否是通过运行 cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) 进行添加的。


## <a name="remove-nodes-from-your-cluster"></a>从群集中删除节点
根据针对群集选择的可靠性级别，可能无法删除主节点类型的前 n 个 (3/5/7/9) 节点。 另请注意，不支持在开发群集上运行 RemoveNode 命令。

1. 通过远程桌面 (RDP) 方式进入需要从群集中删除的 VM/计算机。
2. 复制或[下载适用于 Windows Server 的 Service Fabric 独立包](http://go.microsoft.com/fwlink/?LinkId=730690)，并将该包解压缩到此 VM/计算机。
3. 以管理员身份运行 Powershell，并导航到解压缩包所在的位置。
4. 在 Powershell 中运行 *RemoveNode.ps1*。 以下示例从群集中删除当前节点。 *ExistingClientConnectionEndpoint* 是将保留在群集中的任何节点的客户端连接终结点。 选择群集中*任何***其他节点**的 IP 地址和终结点端口。 **另一个节点**反过来将更新已删除节点的群集配置。 

```

.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000

```

> [!NOTE]
> 由于系统服务的依赖关系，某些节点可能无法删除。 这些节点是主节点，可以通过使用 `Get-ServiceFabricClusterManifest` 并查找标有 `IsSeedNode=”true”` 的节点条目查询群集清单来标识。 
> 
> 

即使在删除节点之后，如果它在查询和 SFX 中显示处于脱机，请记住这是已知缺陷。 我们会在即将发布的版本中修复此缺陷。 


## <a name="remove-node-types-from-your-cluster"></a>从群集中删除节点类型
删除节点类型时需要特别注意。 删除节点类型前，请仔细检查是否存在任何引用该节点类型的节点。


## <a name="replace-primary-nodes-of-your-cluster"></a>替换群集的主节点
应该逐个执行主节点的替换，而不是批量删除然后添加。


## <a name="next-steps"></a>后续步骤
* [独立 Windows 群集的配置设置](service-fabric-cluster-manifest.md)
* [使用 X509 证书保护 Windows 上的独立群集](service-fabric-windows-cluster-x509-security.md)
* [使用运行 Windows 的 Azure VM 创建独立 Service Fabric 群集](service-fabric-cluster-creation-with-windows-azure-vms.md)


