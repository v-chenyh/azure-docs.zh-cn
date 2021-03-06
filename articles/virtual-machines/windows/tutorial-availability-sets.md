---
title: "Azure 中 Windows VM 的可用性集教程 | Microsoft Docs"
description: "了解 Azure 中 Windows VM 的可用性集。"
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/08/2017
ms.author: cynthn
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 022396a8bf0478414be179b9f7341a459ed2bc60
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---

# <a name="how-to-use-availability-sets"></a>如何使用可用性集

本教程介绍如何使用称作“可用性集”的功能提高 Azure 上虚拟机解决方案的可用性和可靠性。 可用性集可确保在 Azure 上部署的 VM 能够跨多个隔离的硬件群集分布。 这样，就可以确保当 Azure 中发生硬件或软件故障时，只有一部分 VM 会受到影响，整体解决方案仍可供其客户使用和操作。 

本教程将介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

本教程需要 Azure PowerShell 模块 3.6 或更高版本。 运行 ` Get-Module -ListAvailable AzureRM` 即可查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

## <a name="availability-set-overview"></a>可用性集概述

可用性集是一种逻辑分组功能，在 Azure 中使用它可以确保将 VM 资源部署在 Azure 数据中心后，这些资源相互隔离。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 这样就可以确保当出现硬件或 Azure 软件故障时，只有一部分 VM 会受到影响，整体应用程序仍会保持运行，可供客户使用。 如果想要构建可靠的云解决方案，可用性集是要利用的一项关键功能。

假设某个基于 VM 的典型解决方案包含 4 个前端 Web 服务器，以及 2 个托管数据库的后端 VM。 在 Azure 中，你想要在部署 VM 之前先定义两个可用性集：一个可用性集用于“Web”层，另一个可用性集用于“数据库”层。 然后，在创建新的 VM 时，可在 az vm create 命令中指定可用性集作为参数，Azure 可自动确保在可用性集中创建的 VM 在多个物理硬件资源之间保持隔离。 这意味着，如果运行某个 Web 服务器或数据库服务器的物理硬件有问题，你可以确信 Web 服务器和数据库 VM 的其他实例会保持正常运行，因为它们位于不同的硬件上。

若要在 Azure 中部署可靠的基于 VM 的解决方案时，始终应该使用可用性集。

## <a name="create-an-availability-set"></a>创建可用性集

可以使用 [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) 创建一个可用性集。 在本示例中，将 myResourceGroupAvailability 资源组中名为 myAvailabilitySet 的可用性集的更新域数和容错域数均设置为 2。

创建资源组。

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>在可用性集内创建 VM

需要在可用性集内创建 VM，以确保其正确地分布在硬件中。 创建后，无法将现有 VM 添加到可用性集中。 

同一位置的硬件分为多个更新域和容错域。 更新域是一组可同时重启的 VM 和基础物理硬件。 同一个容错域内的 VM 共享公用存储，以及公用电源和网络交换机。 

使用 [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) 通过配置创建 VM 时，可以使用 `-AvailabilitySetId` 参数指定可用性集，以指定该可用性集的 ID。

在可用性集中使用 [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) 创建 2 个 VM。

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

创建和配置这两个 VM 需要几分钟的时间完成。 完成后，将拥有 2 个跨基础硬件分布的虚拟机。 

## <a name="check-for-available-vm-sizes"></a>检查可用的 VM 大小 

稍后可向可用性集添加更多 VM，但需了解在硬件上可用的 VM 大小。 使用 [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) 列出可用性集的硬件群集上所有可用的大小。

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 创建可用性集
> * 在可用性集中创建 VM
> * 检查可用的 VM 大小

请转到下一教程，了解虚拟机规模集。

> [!div class="nextstepaction"]
> [创建 VM 规模集](tutorial-create-vmss.md)



