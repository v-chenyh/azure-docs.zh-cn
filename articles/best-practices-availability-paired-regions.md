---
title: "业务连续性和灾难恢复 (BCDR)：Azure 配对区域 | Microsoft Docs"
description: "了解 Azure 区域对，以确保应用程序在数据中心发生故障期间可保持复原能力。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: c2d0a21c-2564-4d42-991a-bc31723f61a4
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: fc27849f3309f8a780925e3ceec12f318971872c
ms.openlocfilehash: 32bff438c2e56b1c21aa907b6bff6c1d1e879de3
ms.contentlocale: zh-cn
ms.lasthandoff: 06/14/2017

---

# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>业务连续性和灾难恢复 (BCDR)：Azure 配对区域

## <a name="what-are-paired-regions"></a>什么是配对区域？

Azure 在世界各地的多个地理位置运营。 Azure 地理位置是至少包含一个 Azure 区域的规定世界区域。 Azure 区域是包含一个或多个数据中心的地理位置内的某个区域。

每个 Azure 区域与同一地理位置中另一个区域配对。 但巴西南部除外，它与自身地理位置外部的某个区域配对。

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

图 1 – Azure 区域对示意图

| 地理位置 | 配对区域 |  |
|:--- |:--- |:--- |
| 亚洲 |东亚 |东南亚 |
| 澳大利亚 |澳大利亚东部 |澳大利亚东南部 |
| 巴西 |巴西南部 (1) |美国中南部 |
| 加拿大 |加拿大中部 |加拿大东部 |
| 中国 |中国北部 |中国东部|
| 欧洲 |欧洲北部 |欧洲西部 |
| 德国 |德国中部 |德国东北部 |
| 印度 |印度中部 |印度南部 |
| 日本 |日本东部 |日本西部 |
| 韩国 |韩国中部 |韩国南部 |
| 北美 |美国中北部 |美国中南部 |
| 北美 |美国东部 |美国西部 |
| 北美 |美国东部 2 |美国中部 |
| 北美 |美国西部 2 |美国中西部 |
| 欧洲 |欧洲北部 |欧洲西部 |
| 亚洲 |亚洲东南部 |东亚 |
| 中国 |中国东部 |中国北部 |
| 日本 |日本东部 |日本西部 |
| 巴西 |巴西南部 (1) |美国中南部 |
| 澳大利亚 |澳大利亚东部 |澳大利亚东南部 |
| 美国政府 |美国政府爱荷华州 |美国政府弗吉尼亚州 |
| 印度 |印度中部 |印度南部 |
| 加拿大 |加拿大中部 |加拿大东部 |
| 英国 |英国西部 |英国南部 |
| 韩国 |韩国中部 |韩国南部 |
| 德国 |德国中部 |德国东北部 |

表 1 - Azure 区域对映射

> (1) 巴西南部与其他区域的不同之处在于，它与自身地理位置外部的区域配对。 巴西南部的次要区域是美国中南部，但是美国中南部的次要区域不是巴西南部。


我们建议你在区域对之间复制工作负荷，以受益于 Azure 的隔离与可用性策略。 例如，计划的 Azure 系统更新将在配对区域之间依序部署（并不是同时部署）。 这意味着，即使发生罕见的更新失败，两个区域也不会同时受到影响。 此外，如果遭遇少见的广泛中断，至少会优先恢复每个对中的一个区域。

## <a name="an-example-of-paired-regions"></a>配对区域的示例
以下图 2 显示了使用区域对进行灾难恢复的虚构应用程序。 绿色数字突出显示了三个 Azure 服务（Azure 计算、存储和数据库）的跨区域活动，以及这些服务如何配置为跨区域复制。 橙色数字突出显示了跨配对区域部署的独特优势。

![配对区域的优势概览](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

图 2 – 虚构的 Azure 区域对

## <a name="cross-region-activities"></a>跨区域活动
如图 2 所示。

![PaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure 计算 (PaaS)** - 必须提前预配附加的计算资源，确保在发生灾难期间另一个区域可以提供资源。 有关详细信息，请参阅 [Azure resiliency technical guidance](resiliency/resiliency-technical-guidance.md)（Azure 复原技术指南）。

![](./media/best-practices-availability-paired-regions/2Green.png) **Azure 存储** - 创建 Azure 存储帐户时，默认将配置异地冗余存储 (GRS)。 使用 GRS 时，数据将在主要区域自动复制三次，并在配对区域复制三次。 有关详细信息，请参阅 [Azure 存储冗余选项](storage/storage-redundancy.md)。

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL 数据库** - 使用 Azure SQL 标准异地复制，可以配置为将事务异步复制到配对区域。 使用高级异地复制，可以配置为复制到全球任何区域；但是，我们建议在配对区域中为大多数灾难恢复方案部署这些资源。 有关详细信息，请参阅 [Azure SQL 数据库中的异地复制](sql-database/sql-database-geo-replication-overview.md)。

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** - Resource Manager 原本就能跨区域提供服务管理组件的逻辑隔离。 这意味着某个区域发生逻辑故障不太可能会影响另一个区域。

## <a name="benefits-of-paired-regions"></a>配对区域的优势
如图 2 所示。  

![隔离](./media/best-practices-availability-paired-regions/5Orange.png)
**物理隔离** - Azure 希望区域对中数据中心之间的距离尽量至少保持 300 英里，不过，要在所有地理位置满足这个要求并不实际且不可能。 物理数据中心隔离能够降低自然灾害、社会动乱、电力中断或物理网络中断同时影响两个区域的可能性。 隔离受限于地理位置的约束（地理位置大小、电源/网络基础结构可用性、法规，等等）。  

![复制](./media/best-practices-availability-paired-regions/6Orange.png)
**平台提供的复制** - 某些服务（例如异地冗余存储）提供自动复制到配对区域的功能。

![恢复](./media/best-practices-availability-paired-regions/7Orange.png)
**区域恢复顺序** - 如果发生广泛中断，会优先恢复每个对中的某一个区域。 可以保证跨配对区域部署的应用程序在其中一个区域中优先恢复。 如果应用程序部署在未配对的区域中，则可能会发生恢复延迟；最坏的情况是，选择两个的区域可能最后才会得到恢复。

![更新](./media/best-practices-availability-paired-regions/8Orange.png)
**依序更新** - 计划的 Azure 系统更新将按顺序发布到配对的区域（不是同时发布），以便在出现罕见的更新失败时，将停机时间、Bug 影响和逻辑故障的可能性降到最低。

![数据](./media/best-practices-availability-paired-regions/9Orange.png)
**数据驻留** - 一个区域驻留在与其配对区域相同的地理位置（巴西南部除外），以符合税务和执法管辖范围方面的数据驻留要求。

