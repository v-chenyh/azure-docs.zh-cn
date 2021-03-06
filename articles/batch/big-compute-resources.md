---
title: "Azure 云中的 Batch 和 HPC 的资源 | Microsoft Docs"
description: "列出了旨在帮助你在 Azure 中运行大规模并行批处理和高性能计算 (HPC) 工作负荷的技术资源。"
services: batch, cloud-services, virtual-machines
documentationcenter: 
author: dlepow
manager: timlt
editor: 
ms.assetid: 6f8be911-c841-41ae-88d3-3bcfc029eb7f
ms.service: multiple
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: big-compute
ms.date: 03/17/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: aff11fe3d7e4cea3580f73d54a53d9624e4f3ac9
ms.lasthandoff: 04/03/2017


---
# <a name="big-compute-in-azure-technical-resources-for-batch-and-high-performance-computing"></a>Azure 中的大型计算：用于批处理和高性能计算的技术资源
此为技术资源指南，旨在帮助用户在 Azure 中运行大规模并行批处理和高性能计算 (HPC) 工作负荷。 可以使用各种 Azure 服务将现有的批处理或 HPC 工作负荷扩展到 Azure 云，或者生成新的大型计算解决方案。

## <a name="solutions-options"></a>解决方案选项
了解 Azure 中的大型计算选项，并根据工作负荷和业务需要选择适当的方法。

* [Batch 和 HPC 解决方案](batch-hpc-solutions.md)
* [视频：使用 Azure 和 HPC 在云中进行大型计算](https://azure.microsoft.com/documentation/videos/teched-europe-2014-big-compute-in-the-cloud-with-high-performance-computing-on-azure/)

## <a name="azure-batch"></a>Azure 批处理
[Batch](https://azure.microsoft.com/services/batch/) 是一种平台服务，可让用户轻松地在 Linux 和 Windows 应用程序中启用云功能并运行作业，而无需设置和管理群集与作业计划程序。 使用 SDK 可将不同语言的客户端应用与 Azure Batch 集成，将数据迁移到 Azure，以及生成作业运行管道。

* [文档](https://azure.microsoft.com/documentation/services/batch/)
* [.NET](https://msdn.microsoft.com/library/azure/mt348682.aspx)、[Python](http://azure-sdk-for-python.readthedocs.io/latest/)、[Node.js](http://azure.github.io/azure-sdk-for-node/azure-batch/latest/)、[Java](http://azure.github.io/azure-sdk-for-java/) 和 [REST](https://msdn.microsoft.com/library/azure/dn820158.aspx) API 参考
* [Batch 管理 .NET 库](https://msdn.microsoft.com/library/mt463120.aspx)参考
* 教程：[用于 .NET 的 Azure Batch 库](batch-dotnet-get-started.md)入门和 [Batch Python 客户端](batch-python-tutorial.md)入门
* [Batch 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch)
* [Batch 视频](https://azure.microsoft.com/documentation/videos/index/?services=batch)

## <a name="hpc-cluster-solutions"></a>HPC 群集解决方案
将现有的 Windows 或 Linux HPC 群集部署或扩展到 Azure，以运行计算密集型工作负荷。  

### <a name="microsoft-hpc-pack"></a>Microsoft HPC Pack
HPC Pack 是在 Microsoft Azure 和 Windows Server 技术基础之上构建的 Microsoft 免费 HPC 解决方案，它能够运行 Windows 和 Linux HPC 工作负荷。  

* [下载 HPC Pack 2016](https://www.microsoft.com/download/details.aspx?id=54507)
* [下载 HPC Pack 2012 R2 Update 3](https://www.microsoft.com/download/details.aspx?id=49922)
* [文档](https://technet.microsoft.com/library/jj899572.aspx)
* Azure 中的 HPC Pack 群集选项：[Linux](../virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 和 [Windows](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 
* [使用 HPC Pack 迸发到 Azure 辅助角色实例](https://technet.microsoft.com/library/gg481749.aspx)
* [使用 HPC Pack 迸发到 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)
* [Windows HPC 论坛](https://social.microsoft.com/Forums/home?category=windowshpc)

### <a name="linux-and-oss-cluster-solutions"></a>Linux 和 OSS 群集解决方案
使用这些 Azure 模板来部署 Linux HPC 群集。

* [运转 SLURM 群集](https://azure.microsoft.com/documentation/templates/slurm/)和[博客文章](http://blogs.technet.com/b/windowshpc/archive/2015/06/06/deploy-a-slurm-cluster-on-azure.aspx)
* [运转 Torque 群集](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [使用 PBS Professional 计算网格模板](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

## <a name="hpc-storage"></a>HPC 存储
* [Azure 上用于 HPC 存储的并行文件系统](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)
* [用于 Lustre 软件的 Intel 云版本 - 评估](https://azure.microsoft.com/marketplace/partners/intel/lustre-cloud-edition-evaleval-lustre-2-7/)
* [CentOS 7.2 上的 BeeGFS 模板](https://github.com/smith1511/hpc/tree/master/beegfs-shared-on-centos7.2)




## <a name="microsoft-mpi"></a>Microsoft MPI
[Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) (MS-MPI) 是 Microsoft 实现的消息传递接口标准，用于在 Windows 平台上开发和运行并行应用程序。

* [下载 MS-MPI](http://go.microsoft.com/FWLink/p/?LinkID=389556)
* [MS-MPI 参考](https://msdn.microsoft.com/library/dn473458.aspx)
* [MPI 论坛](https://social.microsoft.com/Forums/en-us/home?forum=windowshpcmpi)

## <a name="compute-intensive-instances"></a>计算密集型实例
Azure 提供了[一系列 VM 大小](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)，包括能够连接到后端 RDMA 网络的[计算密集型 H 系列](../virtual-machines/windows/a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)实例，以运行 Linux 和 Windows HPC 工作负荷。 

* [设置 Linux RDMA 群集以运行 MPI 应用程序](../virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [通过 Microsoft HPC Pack 设置 Windows RDMA 群集以运行 MPI 应用程序](../virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

有关 GPU 密集型工作负载，请查看 [NC 和NV 大小](https://azure.microsoft.com/blog/azure-n-series-general-availability-on-december-1/)。

## <a name="samples-and-demos"></a>示例和演示
* [Azure Batch C# 和 Python 代码示例](https://github.com/Azure/azure-batch-samples)
* [ Shipyard](https://azure.github.io/batch-shipyard/) 工具包，可以轻松地将批处理样式的 Dockerized 工作负荷部署到 Azure Batch
* 基于 Azure Batch 构建的 [doAzureParallel](http://www.github.com/Azure/doAzureParallel) R 包
* [Test drive SUSE Linux Enterprise Server for HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)（试用 SUSE Linux Enterprise Server for HPC）

## <a name="related-azure-services"></a>相关的 Azure 服务
* [Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)
* [机器学习](https://azure.microsoft.com/documentation/services/machine-learning/)
* [HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [虚拟机](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [虚拟机规模集](https://azure.microsoft.com/documentation/services/virtual-machine-scale-sets/)
* [云服务](https://azure.microsoft.com/documentation/services/cloud-services/)
* [应用服务](https://azure.microsoft.com/documentation/services/app-service/)
* [媒体服务](https://azure.microsoft.com/documentation/services/media-services/)
* [函数](https://azure.microsoft.com/documentation/services/functions/)

## <a name="architecture-blueprints"></a>体系结构蓝图
* [HPC and data orchestration using Azure Batch and Azure Data Factory](http://go.microsoft.com/fwlink/?linkid=717686)（HPC 和数据的业务流程使用 Azure Batch 和 Azure 数据工厂）(PDF) 和[文章](../data-factory/data-factory-data-processing-using-batch.md)

## <a name="industry-solutions"></a>行业解决方案
* [银行和资本市场](https://finance.azure.com/)
* [Engineering simulations](https://simulation.azure.com/)（工程模拟） 

## <a name="customer-stories"></a>客户案例
* [ANEO](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=4168) 
* [d3View](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=22088)
* [路德维格癌症研究所](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5830)
* [Microsoft Research](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=15634)
* [Milliman](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=14967)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=26266)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18222)
* [UberCloud](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)

## <a name="next-steps"></a>后续步骤
* 有关最新通告，请参阅 [Microsoft HPC 和批处理团队博客](http://blogs.technet.com/b/windowshpc/)与 [Azure 博客](https://azure.microsoft.com/blog/tag/hpc/)。
* 另请参阅 [Batch 中的新增功能](https://azure.microsoft.com/updates/?service=batch)或订阅 [RSS 源](https://azure.microsoft.com/updates/feed/?service=batch)。


