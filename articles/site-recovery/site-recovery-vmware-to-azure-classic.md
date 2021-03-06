---
title: "在经典 Azure 门户中将 VMware VM 和物理服务器复制到 Azure | Microsoft 文档"
description: "本文介绍如何通过部署 Azure Site Recovery 来协调本地 VMware 虚拟机和 Windows/Linux 物理服务器到 Azure 的复制、故障转移和恢复。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: a9022c1f-43c1-4d38-841f-52540025fb46
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-vmware-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 73c3fb5cf4056ddb9554f598ec7f173d81802f17
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>通过 Azure Site Recovery 将 VMware 虚拟机和物理服务器复制到 Azure
> [!div class="op_single_selector"]
> * [Azure 门户](site-recovery-vmware-to-azure.md)
> * [经典门户](site-recovery-vmware-to-azure-classic.md)
> * [经典门户（旧版）](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

Azure Site Recovery 服务有助于业务连续性和灾难恢复 (BCDR) 策略，因为它可以协调虚拟机和物理服务器的复制、故障转移和恢复。 虚拟机可复制到 Azure 中，也可复制到本地数据中心中。 如需快速概览，请阅读[什么是 Azure Site Recovery？](site-recovery-overview.md)

## <a name="overview"></a>概述
本文将介绍如何：

* **将 VMware 虚拟机复制到 Azure**：部署 Site Recovery，以便协调本地 VMware 虚拟机到 Azure 存储的复制、故障转移和恢复。
* **将物理服务器复制到 Azure**：部署 Azure Site Recovery，以便协调本地 Windows 和 Linux 物理服务器到 Azure 的复制、故障转移和恢复。

> [!NOTE]
> 本文介绍如何复制到 Azure。 如果要将 VMware VM 或 Windows/Linux 物理服务器复制到辅助数据中心，请参阅 [Site Recovery VMware to VMware](site-recovery-vmware-to-vmware.md)（使用 Site Recovery 在 VMware 之间复制）。
>
>

请将任何评论或问题发布到本文底部，或者发布到 [Azure 恢复服务论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)。

## <a name="enhanced-deployment"></a>增强型部署
本文包含的说明适用于 Azure 经典门户中的增强型部署。 建议将此版本用于所有全新的部署。 如果已使用旧版本进行了部署，则建议迁移到新版本。 有关迁移的详细信息，请参阅 [Site Recovery VMware to Azure classic legacy](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment)（在旧版 Azure 经典门户中使用 Site Recovery 复制 VMware）。

增强型部署是一种重大更新。 下面是我们所做改进的摘要：

* **在 Azure 中没有基础结构 VM**：数据直接复制到 Azure 存储帐户。 此外，不需要像在旧部署中一样，为复制和故障转移设置任何基础结构 VM（例如配置服务器或主目标服务器）。  
* **统一安装**：统一安装可以方便本地组件的安装和伸缩操作。
* **安全部署**：所有流量都进行加密，复制管理通信经 HTTPS 443 发送。
* **恢复点**：支持崩溃状态一致的以及与应用程序一致的恢复点（适用于 Windows 和 Linux 环境），支持单 VM 和多 VM 一致的配置。
* **测试故障转移**：支持不中断的测试性故障转移（故障转移到 Azure），不影响生产，也不会导致复制暂停。
* **非计划的故障转移**：支持非计划的故障转移（故障转移到 Azure），并可在故障转移之前通过增强版选项来自动关闭 VM。
* **故障回复**：集成式故障回复，仅将增量更改复制回本地站点。
* **vSphere 6.0**：对 VMware vSphere 6.0 部署的支持有限。

## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Site Recovery 是如何帮助你保护虚拟机和物理服务器的？
* VMware 管理员可以配置针对 Azure 的非现场保护，保护运行在 VMware 虚拟机上的业务工作负荷和应用程序。 服务器管理员可以将本地 Windows 和 Linux 物理服务器复制到 Azure。
* Azure Site Recovery 控制台可以对复制、故障转移和恢复过程进行集中且简单的设置和管理。
* 如果你复制受 vCenter 服务器管理的 VMware 虚拟机，则 Site Recovery 可以自动发现这些 VM。 如果虚拟机位于 ESXi 主机上，则 Site Recovery 可以发现该主机上的 VM。
* 如果运行从本地基础结构到 Azure 的简单故障转移，可以从 Azure 故障回复（还原）到本地站点中的 VMware VM 服务器。
* 可以配置恢复计划，将分布到多个计算机中的应用程序工作负荷组合到一起。 可以对这些计划进行故障转移，而 Site Recovery 提供多 VM 一致性，因此可以将运行相同工作负荷的计算机一起恢复到某个一致的数据点。

## <a name="supported-operating-systems"></a>支持的操作系统
### <a name="windows-64-bit-only"></a>Windows（仅限 64 位）
* Windows Server 2008 R2 SP1+
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux（仅限 64 位）
* Red Hat Enterprise Linux 6.7、7.1 和 7.2
* CentOS 6.5、6.6、6.7、7.0、7.1 和 7.2
* 运行 Red Hat 兼容内核或 Unbreakable Enterprise Kernel Release 3 (UEK3) 的 Oracle Enterprise Linux 6.4 和 6.5
* SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>方案体系结构
方案组件：

* **本地管理服务器**：管理服务器运行 Site Recovery 组件：
  * **配置服务器**：协调通信，同时管理数据复制和恢复过程。
  * **进程服务器**：充当复制网关。 此服务器接收受保护源计算机提供的数据，通过缓存、压缩和加密对其进行优化，然后将复制数据发送到 Azure 存储。 它还处理用于保护计算机的移动服务的推送安装，并执行 VMware VM 的自动发现。
  * **主目标服务器**：处理从 Azure 进行故障回复期间产生的复制数据。
    还可以部署仅充当进程服务器的管理服务器来缩放部署。
* **移动服务**：此组件部署在要复制到 Azure 的每个计算机（VMware VM 或物理服务器）上。 它可以捕获计算机上的数据写入，并将其转发到进程服务器。
* **Azure**：不需要创建任何 Azure VM 来处理复制和故障转移。 Site Recovery 服务处理数据管理操作，数据直接复制到 Azure 存储。 仅当故障转移到 Azure 时，才会自动启动复制的 Azure VM。 但是，如果需要从 Azure 故障回复到本地站点，则需设置一个充当进程服务器的 Azure VM。

下图（由 Henry Robalino 制作）显示了这些组件的交互方式：

![组件体系结构](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

## <a name="capacity-planning"></a>容量计划
计划容量时，需考虑以下问题：

* **源环境**：容量规划或 VMware 基础结构和源计算机要求。
* **管理服务器**：针对运行 Site Recovery 组件的本地管理服务器进行规划。
* **从源到目标的网络带宽**：针对所需的网络带宽进行规划，以便在源和 Azure 之间进行复制

### <a name="source-environment-considerations"></a>源环境注意事项
* **每日最大更改率**：一台受保护的计算机只能使用一个进程服务器。 一个进程服务器每日最多可以处理 2 TB 的数据更改。 因此，2 TB 是受保护计算机支持的每日数据更改率上限。
* **最大吞吐量**：在 Azure 中，一台复制的计算机可以属于一个存储帐户。 标准存储帐户每秒最多可以处理 20,000 个请求，因此建议你将源计算机中的 IOPS 数目保持在 20,000。 例如，如果源计算机有 5 个磁盘，每个磁盘在源上生成 120 IOPS（8 KB 大小），则处于 Azure 的单磁盘 IOPS 限制 (500) 内。 所需的存储帐户数 = 源 IOPs 总计/20,000。

### <a name="management-server-considerations"></a>管理服务器注意事项
管理服务器运行的 Site Recovery 组件负责处理数据的优化、复制和管理。 该服务器应该能够处理跨所有工作负荷（运行在受保护的计算机上）的每日更改率容量，并有足够的带宽，可以持续地将数据复制到 Azure 存储。 具体而言：

* 进程服务器接收受保护计算机提供的复制数据，并在发送到 Azure 之前对其通过缓存、压缩和加密进行优化。 管理服务器应有足够的资源来执行这些任务。
* 处理服务器使用基于磁盘的缓存。 建议在出现网络瓶颈或中断时，使用单独的大小至少为 600 GB 的缓存磁盘来处理存储的数据更改。 在部署过程中，你可以在可用存储空间至少为 5 GB 的任何驱动器上配置缓存，但建议的最小大小为 600 GB。
* 根据最佳做法，我们建议将管理服务器置于与所要保护的计算机相同的网络和 LAN 网段上。 可以将它置于另一网络中，但所要保护的计算机应可通过 L3 网络来查看它。

下表汇总了管理服务器的建议大小：

| **管理服务器 CPU** | **内存** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机** |
| --- | --- | --- | --- | --- |
| 8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz） |16 GB |300 GB |500 GB 或更少 |使用这些设置来部署管理服务器，可复制数量不到 100 台计算机。 |
| 12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5 GHz） |18 GB |600 GB |500 GB 到 1 TB |使用这些设置来部署管理服务器，可复制 100-150 台计算机。 |
| 16 个 vCPU（2 个插槽 * 8 个核心 @ 2.5 GHz） |32 GB |1 TB |1 TB 到 2 TB |使用这些设置来部署管理服务器，可复制 150-200 台计算机。 |
| 部署另一个进程服务器 | | |> 2 TB |如果你要复制 200 多台计算机，或者每日数据更改率超过 2 TB，则需部署额外的进程服务器。 |

其中：

* 每台源计算机均配置 3 个磁盘，每个磁盘 100 GB。
* 我们使用了包含 8 个 SAS 驱动器 (10,000 RPM) 的基准测试存储空间，使用 RAID 10 进行缓存磁盘度量。

### <a name="network-bandwidth-from-source-to-target"></a>从源到目标的网络带宽
请确保计算带宽，带宽是使用 [Capacity Planner 工具](site-recovery-capacity-planner.md)进行初始复制和增量复制所必需的。

#### <a name="throttling-bandwidth-used-for-replication"></a>限制用于复制的带宽
复制到 Azure 的 VMware 流量会经过特定的进程服务器。 你可以在该服务器上限制用于 Site Recovery 复制的带宽，如下所示：

1. 在主管理服务器或运行其他预配的进程服务器的管理服务器上打开 Microsoft Azure 备份 MMC 管理单元。 默认情况下，会在桌面上创建 Microsoft Azure 备份的快捷方式。 也可以在 C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin 中找到它。
2. 在管理单元中，单击“更改属性”。

    ![限制带宽更改属性](./media/site-recovery-vmware-to-azure-classic/throttle1.png)
3. 在“限制”选项卡上，指定可以用于 Site Recovery 复制和相应计划的带宽。

    ![限制带宽复制](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

也可以使用 PowerShell 来设置限制。 下面是一个示例：

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>最大化带宽使用率
若要增大通过 Azure Site Recovery 进行复制时的带宽使用率，需要更改注册表项。

以下项控制进行复制时每个复制磁盘所使用的线程数：

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 在过度预配的网络中，此注册表项需要更改，不能使用默认值。 我们支持的最大数为 32。  

若要详细了解容量规划，请参阅 [Site Recovery Capacity Planner](site-recovery-capacity-planner.md)。

### <a name="additional-process-servers"></a>额外的进程服务器
如果需要保护的计算机超过 200 台，或者每日更改率大于 2 TB，可添加更多服务器来处理负载。 若要横向扩展，可以执行以下操作：

* 增加管理服务器的数量。 例如，可以通过两台管理服务器来保护最多 400 台计算机。
* 添加更多进程服务器，使用这些服务器来处理流量，而不是添加管理服务器。

下表介绍了一种方案，在该方案中：

* 对原始管理服务器进行了设置，仅将其用作配置服务器。
* 你还设置了一个进程服务器。
* 你将受保护的虚拟机配置为使用这个额外的进程服务器。
* 每台受保护的源计算机配置为了三个磁盘，每个磁盘 100 GB。

| **原始管理服务器**<br/><br/>（配置服务器） | **额外的进程服务器** | **缓存磁盘大小** | **数据更改率** | **受保护的计算机** |
| --- | --- | --- | --- | --- |
| 8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz），16 GB RAM |4 个 vCPU（2 个插槽 * 2 个核心 @ 2.5 GHz），8 GB RAM |300 GB |250 GB 或更少 |可以复制 85 台或更少的计算机。 |
| 8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz），16 GB RAM |8 个 vCPU（2 个插槽 * 4 个核心 @ 2.5 GHz），12 GB RAM |600 GB |250 GB 到 1 TB |可以复制 85-150 台计算机。 |
| 12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5 GHz），18 GB RAM |12 个 vCPU（2 个插槽 * 6 个核心 @ 2.5 GHz），24 GB RAM |1 TB |1 TB 到 2 TB |可以复制 150-225 台计算机。 |

如何缩放服务器取决于你是偏好纵向扩展模型还是横向扩展模型。 纵向扩展时，需部署数个高端管理服务器和进程服务器，而横向扩展时，则需部署较多服务器，需要的资源较少。 例如，如果需要对 220 台计算机进行保护，可执行以下操作之一：

* 将原始的管理服务器配置为 12 个 vCPU，18 GB RAM。 将附加的进程服务器配置为 12 个 vCPU，24 GB RAM。 将受保护的计算机配置为仅使用附加的进程服务器。
* 可以配置两个管理服务器（2 x 8 vCPU，16 GB RAM）和两个附加的进程服务器（1 x 8 vCPU 和 1 x 4 vCPU，可处理 135 + 85 (220) 台计算机）。 将受保护的计算机配置为仅使用附加的进程服务器。

遵照[部署附加的进程服务器](#deploy-additional-process-servers)中的说明设置附加的进程服务器。

## <a name="before-you-start-deployment"></a>在开始部署之前
下列表格汇总了部署此方案的先决条件。

### <a name="azure-prerequisites"></a>Azure 先决条件
| **先决条件** | **详细信息** |
| --- | --- |
| **Azure 帐户** |需要一个 [Microsoft Azure](https://azure.microsoft.com/) 帐户。 你可以从 [免费试用版](https://azure.microsoft.com/pricing/free-trial/)开始。 有关 Site Recovery 定价的详细信息，请参阅 [Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)。 |
| **Azure 存储** |需要使用一个 Azure 存储帐户来存储复制的数据。 复制的数据存储在 Azure 存储中，Azure VM 在发生故障转移时启动。 <br/><br/>你需要[标准异地冗余存储帐户](../storage/storage-redundancy.md#geo-redundant-storage)。 该帐户必须位于 Site Recovery 服务所在的同一区域，并与同一订阅相关联。 目前不支持复制到高级存储帐户，因此不应使用该功能。<br/><br/>不支持跨资源组移动使用 [Azure 门户](../storage/storage-create-storage-account.md)创建的存储帐户。 有关详细信息，请参阅 [Microsoft Azure 存储简介](../storage/storage-introduction.md)。<br/><br/> |
| **Azure 网络** |需要一个 Azure 虚拟网络，以便发生故障转移时 Azure VM 能够连接到其中。 Azure 虚拟网络必须位于与 Site Recovery 保管库相同的区域中。<br/><br/>若要在故障转移到 Azure 后进行故障回复，需要设置从 Azure 网络到本地站点的 VPN 连接（或 Azure ExpressRoute）。 |

### <a name="on-premises-prerequisites"></a>本地先决条件
| **先决条件** | **详细信息** |
| --- | --- |
| **管理服务器** |你需要一个运行在虚拟机或物理服务器上的本地 Windows 2012 R2 服务器。 已在此管理服务器上安装所有本地 Site Recovery 组件。<br/><br/> 我们建议你将服务器部署为高度可用的 VMware VM。 从 Azure 故障回复到本地站点时，将始终故障回复到 VMware VM，不管你是对 VM 还是物理服务器进行了故障转移。 如果未将管理服务器配置为 VMware VM，则需要将单独的主目标服务器设置为 VMware VM 来接收故障回复流量。<br/><br/>服务器不应为域控制器。<br/><br/>服务器应拥有一个静态 IP 地址。<br/><br/>服务器的主机名应为 15 个字符或更少。<br/><br/>操作系统的区域设置应仅为英语。<br/><br/>管理服务器需要访问 Internet。<br/><br/>需要服务器的出站访问，如下所述：设置 Site Recovery（以下载 MySQL）期间在 HTTP 80 上的临时访问；在 HTTPS 443 上的正在进行的出站访问用于复制管理；在 HTTPS 9443 上的正在进行的出站访问用于复制流量（此端口可以修改）。<br/><br/> 确保可通过管理服务器访问以下 URL： <br/>- \*.hypervrecoverymanager.windowsazure.com<br/>- \*.accesscontrol.windows.net<br/>- \*.backup.windowsazure.com<br/>- \*.blob.core.windows.net<br/>- \*.store.core.windows.net<br/>- https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>如果在服务器上设置了基于 IP 地址的防火墙规则，请确保这些规则允许与 Azure 通信。 你需要允许 [Azure 数据中心 IP 范围](https://www.microsoft.com/download/details.aspx?id=41653)和 HTTPS (443) 端口。 还需要将所订阅的 Azure 区域（以及美国西部）的 IP 地址范围加入允许列表。 URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") 用于下载 MySQL。 |
| **VMware vCenter/ESXi 主机** |需要一个或多个 VMware vSphere ESX/ESXi 虚拟机监控程序，该程序管理运行 ESX/ESXi 6.0、5.5 或 5.1 版并装有最新更新的 VMware 虚拟机。<br/><br/> 建议部署 VMware vCenter 服务器来管理 ESXi 主机。 它应该运行的是装有最新更新的 vCenter 6.0 或 5.5 版。<br/><br/>请注意，Site Recovery 不支持新的 vCenter 和 vSphere 6.0 功能，例如跨 vCenter vMotion、虚拟卷和存储 DRS。 Site Recovery 支持仅限也可在 5.5 版中使用的功能。 |
| **受保护的计算机** |**Azure**<br/><br/>要保护的计算机应符合创建 Azure VM 的 [Azure 先决条件](site-recovery-prereq.md)。<br><br/>如果想要在故障转移后连接到 Azure VM，需要在本地防火墙上启用远程桌面连接。<br/><br/>受保护计算机上单个磁盘的容量不应超过 1023 GB。 一台 VM 最多可以有 64 个磁盘（因此最大容量为 64 TB）。 如果磁盘超出 1 TB，可以考虑使用数据库复制（例如 SQL Server Always On 或 Oracle 数据防护）。<br/><br/>安装驱动器上至少需要 2 GB 的可用空间才能安装组件。<br/><br/>不支持共享的磁盘来宾群集。 如果有聚集部署，可以考虑使用数据库复制（例如 SQL Server Always On 或 Oracle 数据防护）。<br/><br/>不支持统一可扩展固件接口 (UEFI)/可扩展固件接口 (EFI)。<br/><br/>计算机名称应包含 1 到 63 个字符（字母、数字和连字符）。 名称必须以字母或数字开头，并以字母或数字结尾。 计算机受保护后，可以修改 Azure 名称。<br/><br/>**VMware VM**<br/><br>需要安装 VMware vSphere PowerCLI 6.0。 安装 VMware vSphere PowerCLI 6.0。<br/><br/>要保护的 VMware VM 上应已安装并正在运行 VMware 工具。<br/><br/>如果源 VM 具有 NIC 组合，在故障转移到 Azure 后，它将转换为单个 NIC。<br/><br/>如果受保护的 VM 有 iSCSI 磁盘，则 Site Recovery 会在 VM 故障转移到 Azure 时将受保护的 VM iSCSI 磁盘转换为 VHD 文件。 如果 iSCSI 目标可供 Azure VM 访问，后者会连接到 iSCSI 目标且实际上会看到两个磁盘：Azure VM 上的 VHD 磁盘，以及源 iSCSI 磁盘。 在这种情况下，需要断开连接故障转移的 Azure VM 上显示的 iSCSI 目标。<br/><br/>有关 Site Recovery 所需的 VMware 用户权限的详细信息，请参阅 [VMware 进行 vCenter 访问所需的权限](#vmware-permissions-for-vcenter-access)。<br/><br/> **Windows Server 计算机（位于 VMware VM 或物理服务器上）**<br/><br/>服务器应运行受支持的 64 位操作系统：Windows Server 2012 R2、Windows Server 2012 或 Windows Server 2008 R2 SP1 及更高版本。<br/><br/>操作系统应安装在驱动器 C 上，OS 磁盘应为 Windows 基本磁盘。 （不应在 Windows 动态磁盘上安装 OS。）<br/><br/>对于 Windows Server 2008 R2 计算机，需要安装 .NET Framework 3.5.1。<br/><br/>需要提供管理员帐户（必须是 Windows 计算机上的本地管理员）才能在 Windows 服务器上推送安装移动服务。 如果提供的帐户不是域帐户，则需要在本地计算机上禁用远程用户访问控制。 有关详细信息，请参阅[使用推送安装安装移动服务](#install-the-mobility-service-with-push-installation)。<br/><br/>Site Recovery 支持包含 RDM 磁盘的 VM。 在故障回复期间，如果原始的源 VM 和 RDM 磁盘可用，Site Recovery 会重复使用 RDM 磁盘。 如果这些磁盘不可用，则 Site Recovery 将在故障回复期间为每个磁盘创建一个新的 VMDK 文件。<br/><br/>**Linux 计算机**<br/><br/>需要支持的 64 位操作系统：Red Hat Enterprise Linux 6.7；Centos 6.5、6.6 或 6.7；运行 Red Hat 兼容内核或 Unbreakable Enterprise Kern Release 3 (UEK3) 的 Oracle Enterprise Linux 6.4 或 6.5；SUSE Linux Enterprise Server 11 SP3。<br/><br/>受保护计算机上的 /etc/hosts 文件包含的条目应将本地主机名映射到与所有网络适配器相关联的 IP 地址。 <br/><br/>如果要在故障转移后使用安全外壳客户端 (SSH) 连接运行 Linux 的 Azure 虚拟机，请确保将受保护的计算机上的安全外壳服务设置为在系统启动时自动启动，并且防火墙规则允许与其建立 SSH 连接。<br/><br/>只能在具有以下存储的 Linux 计算机上启用保护：文件系统（EXT3、ETX4、ReiserFS、XFS）；多路径软件设备映射器（多路径）；卷管理器 (LVM2)。 不支持使用 HP CCISS 控制器存储的物理服务器。 ReiserFS 文件系统仅在 SUSE Linux Enterprise Server 11 SP3 上受支持。<br/><br/>Site Recovery 支持包含 RDM 磁盘的 VM。 在针对 Linux 进行故障回复期间，Site Recovery 不重复使用 RDM 磁盘。 它将为每个相应的 RDM 磁盘创建新的 VMDK 文件。 |

仅适用于 Linux VM：在 VMware 中，请确保在 VM 的“配置参数”中设置了 disk.enableUUID=true 设置。 如果此行不存在，请添加此行。 若要为 VMDK 提供一致的 UUID，以便能够正确进行装载，则这是必需的。 如果没有此设置，故障回复时会进行完整的下载，即使 VM 在本地可用。 添加此设置可确保在故障回复过程中仅将增量更改传输回来。

## <a name="step-1-create-a-vault"></a>步骤 1：创建保管库
1. 登录到 [Azure 门户](https://manage.windowsazure.com/)。
2. 展开“**数据服务**” > “**恢复服务**”，并单击“**Site Recovery 保管库**”。
3. 单击“**新建**” > “**快速创建**”。
4. 在“名称”中，输入一个友好名称以标识此保管库。
5. 在“区域” 中，为保管库选择地理区域。 若要查看受支持的区域，请参阅 [Azure Site Recovery 定价详细信息](https://azure.microsoft.com/pricing/details/site-recovery/)。
6. 单击“创建保管库” 。
    ![创建保管库](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

检查状态栏以确认保管库已成功创建。 保管库将以“**活动**”状态列在主要的“**恢复服务**”页上。

## <a name="step-2-set-up-an-azure-network"></a>步骤 2：设置 Azure 网络
设置一个 Azure 网络，使得 Azure VM 能够在故障转移后连接到网络，同时能够正常地故障回复到本地站点。

1. 在 Azure 门户中选择“创建虚拟网络”并指定网络名称、IP 地址范围和子网名称。
2. 如果需要进行故障回复，请将 VPN/ExpressRoute 添加到网络。 甚至可以在故障转移后将 VPN/ExpressRoute 添加到网络。

有关 Azure 网络的详细信息，请参阅[虚拟网络概述](../virtual-network/virtual-networks-overview.md)。

> [!NOTE]
> 用于部署 Site Recovery 的网络不支持跨同一订阅中的资源组或跨订阅[迁移网络](../azure-resource-manager/resource-group-move-resources.md)。
>
>

## <a name="step-3-install-the-vmware-components"></a>步骤 3：安装 VMware 组件
如果想要复制 VMware 虚拟机，请在管理服务器上执行以下步骤：

1. [下载](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)和安装 VMware vSphere PowerCLI 6.0。
2. 重新启动服务器。

## <a name="step-4-download-a-vault-registration-key"></a>步骤 4：下载保管库注册密钥
1. 在 Azure 中通过管理服务器打开 Site Recovery 控制台。 在“**恢复服务**”页上，单击保管库以打开“**快速启动**”页。 也可随时单击相应的图标打开“快速启动”页。

    ![“快速启动”图标](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)
2. 在“快速启动”页中，单击“准备目标资源” > “下载注册密钥”。 此时将自动生成注册文件。 该文件在生成后的 5 天内有效。

## <a name="step-5-install-the-management-server"></a>步骤 5：安装管理服务器
> [!TIP]
> 确保可通过管理服务器访问以下 URL：
>
> * *.hypervrecoverymanager.windowsazure.com
> * *.accesscontrol.windows.net
> * *.backup.windowsazure.com
> * *.blob.core.windows.net
> * *.store.core.windows.net
> * https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
> * https://www.msftncsi.com/ncsi.txt
>
>



>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Setup-Registration/player]



1. 在“快速启动”页上，将统一的安装文件下载到服务器。
2. 运行安装文件，开始在“Site Recovery 统一安装程序”向导中进行安装。
3. 在“开始之前”中选择“安装配置服务器和进程服务器”。

   ![开始之前](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. 在“第三方软件许可证”中单击“我接受”，下载并安装 MySQL。

    ![第三方软件](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)
5. 在“注册”中，通过浏览查找并选择从保管库下载的注册密钥。

    ![注册](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)
6. 在“Internet 设置”中，指定配置服务器上运行的提供程序如何通过 Internet 连接到 Azure Site Recovery。

   * 如果想要使用当前已在计算机上设置的代理进行连接，请选择“使用现有代理设置进行连接”。
   * 如果希望提供程序直接进行连接，请选择“不使用代理直接连接”。
   * 如果现有代理要求身份验证，或者你想要使用自定义代理进行提供程序连接，请选择“使用自定义代理设置进行连接”。
     * 如果使用自定义代理，需要指定地址、端口和凭据。
     * 如果使用代理，应事先允许以下 URL：
       * *.hypervrecoverymanager.windowsazure.com    
       * *.accesscontrol.windows.net
       * *.backup.windowsazure.com
       * *.blob.core.windows.net
       * *.store.core.windows.net

    ![防火墙](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

1. 在“先决条件检查”设置中运行检查，确保安装可以运行。

    ![先决条件](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     如果看到有关**全局时间同步检查**的警告，请检查系统时钟的时间（“日期和时间”设置）是否与时区相同。

     ![时间同步问题](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

1. 在“MySQL 配置”中，创建用于登录到要安装的 MySQL 服务器实例的凭据。

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)
2. 在“环境详细信息”中，选择是否要复制 VMware VM。 如果要复制，安装程序会检查 PowerCLI 6.0 是否已安装。

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)
3. 在“安装位置”中，选择要安装二进制文件和存储缓存的位置。 可以选择至少有 5 GB 可用磁盘空间的驱动器，但我们建议选择至少有 600 GB 可用磁盘空间的缓存驱动器。

   ![安装位置](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)
4. 在“网络选择”中，指定侦听器（网络适配器和 SSL 端口），以便配置服务器在其上发送和接收复制数据。 你可以修改默认端口 (9443)。 除了此端口，负责协调复制操作的 Web 服务器还会使用端口 443。 不要使用端口 443 来接收复制流量。

    ![网络选择](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



1. 在“摘要”中复查信息，然后单击“安装”。 安装完成后，将生成通行短语。 启用复制时需要用到它，因此请复制并将它保存在安全的位置。

   ![摘要](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)


> [!WARNING]
> 必须设置 Microsoft Azure 恢复服务代理。
> 安装完成后，请从 Windows“开始”菜单启动 Microsoft Azure 恢复服务 Shell。 在打开的命令窗口中，运行以下命令集设置代理服务器设置。
>
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
>


### <a name="run-setup-from-the-command-line"></a>从命令行运行安装程序
你还可以从命令行运行统一向导，如下所示：

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

其中：

* /ServerMode：必需。 指定安装时是应同时安装配置服务器和进程服务器，还是只安装进程服务器（用于安装更多的进程服务器）。 输入值：CS、PS。
* InstallDrive：必需。 指定安装组件的文件夹。
* /MySQLCredFilePath。 必需。 指定存储 MySQL 服务器凭据的文件的路径。 获取创建文件所需的模板。
* /VaultCredFilePath。 必需。 保管库凭据文件的位置。
* /EnvType。 必需。 安装类型。 值：VMware、NonVMware。
* /PSIP 和 /CSIP。 必需。 进程服务器和配置服务器的 IP 地址。
* /PassphraseFilePath。 必需。 通行短语文件的位置。
* /ByPassProxy。 可选。 指定由管理服务器在不使用代理的情况下连接到 Azure。
* /ProxySettingsFilePath。 可选。 指定自定义代理（在需要身份验证的服务器上的默认代理，或自定义代理）的设置。

## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>步骤 6：为 vCenter 服务器设置凭据
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Discovery/player]
>
>

进程服务器可以自动发现由 vCenter 服务器管理的 VMware VM。 进行自动发现时，Site Recovery 需要能够访问 vCenter 服务器的帐户和凭据。 如果只复制物理服务器，则这一点不适用。

设置帐户和凭据：

1. 在 vCenter 服务器上创建一个角色 (**Azure_Site_Recovery**)，该角色处于 vCenter 级别，具有[所需的权限](#vmware-permissions-for-vcenter-access)。
2. 将 **Azure_Site_Recovery** 角色分配给 vCenter 用户。

   > [!NOTE]
   > 具有只读角色的 vCenter 用户帐户可以运行故障转移而不关闭受保护的源计算机。 如果想要关闭这些计算机，需要 Azure_Site_Recovery 角色。 如果只是将 VM 从 VMware 迁移到 Azure 且不需要进行故障回复，则只读角色便已足够。
   >
   >
3. 若要添加该帐户，请打开 **cspsconfigtool**。 该帐户作为快捷方式位于桌面上，并位于 [安装位置]\home\svsystems\bin 文件夹中。
4. 在“管理帐户”选项卡中，单击“添加帐户”。

    ![添加帐户](./media/site-recovery-vmware-to-azure-classic/credentials1.png)
5. 在“帐户详细信息”中，添加可用于访问 vCenter 服务器的凭据。 帐户可能需要在 15 分钟以后才出现在门户中。 若要立即更新，请单击“配置服务器”选项卡上的“刷新”。

    ![详细信息](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>步骤 7：添加 vCenter 服务器和 ESXi 主机
如果要复制 VMware VM，需要添加 vCenter 服务器（或 ESXi 主机）。

1. 在“服务器” > “配置服务器”选项卡上，选择“添加 vCenter 服务器”。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)
2. 添加 vCenter 服务器或 ESXi 主机详细信息、在前一步骤中指定的用于访问 vCenter 服务器的帐户的名称，以及将用于发现 vCenter 服务器所管理的 VMware VM 的进程服务器。 vCenter 服务器或 ESXi 主机所在的网络应与在其上安装了进程服务器的服务器所在的网络相同。

   > [!NOTE]
   > 如果在添加 vCenter 服务器或 ESXi 主机时使用的帐户没有对 vCenter 或主机服务器的管理员权限，请确保 vCenter 或 ESXi 帐户启用了以下权限：数据中心、数据存储、文件夹、主机、网络、资源、虚拟机、vSphere 分布式交换机。 vCenter 服务器需要启用“存储视图”特权。
   >
   >

    ![添加 vCenter 服务器](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)
3. 发现完成之后，vCenter 服务器将列在“配置服务器”选项卡中。

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)

## <a name="step-8-create-a-protection-group"></a>步骤 8：创建保护组
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Protection/player]
>
>

保护组是想要使用相同的保护设置保护的虚拟机或物理服务器的逻辑分组。 可以将保护设置应用到保护组，这些设置将应用到在组中添加的所有计算机（虚拟机或物理机）。

1. 转到“受保护的项” > “保护组”，然后单击图标添加一个保护组。

    ![创建保护组](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)
2. 在“指定保护组设置”页上指定组的名称。 在“从”下拉列表中，选择要在其上创建该组的配置服务器。 “目标”为“Microsoft Azure”。

    ![保护组设置](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)
3. 在“指定复制设置”页上，配置要对该组中所有计算机使用的复制设置。

    ![保护组复制](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

   * **多 VM 一致性**：如果启用此设置，可以跨保护组中的计算机创建共享的应用程序一致性恢复点。 当保护组中的所有计算机运行的是相同工作负荷时，此设置最为有用。 所有计算机都将恢复到相同数据点。 不管是复制 VMware VM，还是复制物理服务器（Windows 或 Linux），此项均可用。
   * **RPO 阈值**：设置 RPO。 当连续数据保护复制超过配置的 RPO 阈值时，将生成警报。
   * **恢复点保留期**：指定保留时段。 受保护的计算机可在此时段内恢复到任何点。
   * **应用程序一致性快照频率**：指定创建包含应用程序一致性快照的恢复点的频率。

选中复选标记时，将使用指定名称创建一个保护组。 此外，还将使用名称 *protection-group-name*-Failback 创建第二个保护组。 如果你在故障转移到 Azure 以后故障回复到本地站点，则会使用此保护组。 你可以监视保护组，因为它们是在“**受保护的项**”页上创建的。

## <a name="step-9-install-the-mobility-service"></a>步骤 9：安装移动服务
为虚拟机和物理服务器启用保护时，第一步是安装移动服务。 可通过两种方式实现此目的：

* 从进程服务器自动推送并在每台计算机上安装该服务。 如果将计算机添加到已运行适当版本移动服务的保护组，则不会进行推送安装。 还可以使用企业推送方法（例如 WSUS 或 System Center Configuration Manager）自动安装该服务。 请确保在执行此操作之前，已设置好管理服务器。
* 在想要保护的每台计算机上手动安装该服务。 请确保在执行此操作之前，已设置好管理服务器。

### <a name="install-the-mobility-service-with-push-installation"></a>使用推送安装安装移动服务
在向保护组添加计算机时，进程服务器将自动推送移动服务，并将其安装在每个计算机上。

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>准备在 Windows 计算机上自动推送
若要准备 Windows 计算机，以便进程服务器能够自动安装移动服务：

1. 创建可供进程服务器用来访问计算机的帐户。 该帐户应具有管理员权限（本地或域）。 这些凭据仅用于推送安装移动服务。

   > [!NOTE]
   > 如果使用的不是域帐户，则需要在本地计算机上禁用远程用户访问控制。 为此，请在注册表的 HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System 下添加值为 1 的 DWORD 项 LocalAccountTokenFilterPolicy。 若要从 CLI 添加注册表项，请打开 cmd 或 PowerShell 并输入 **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**。
   >
   >
2. 对于要保护的计算机上的 Windows 防火墙，请选择“允许应用或功能通过防火墙”并启用“文件和打印机共享”和“Windows Management Instrumentation”。 对于属于某个域的计算机，可以使用 GPO 配置防火墙策略。

   ![防火墙设置](./media/site-recovery-vmware-to-azure-classic/mobility1.png)
3. 添加已创建的帐户：

   * 打开 **cspsconfigtool**。 该帐户作为快捷方式位于桌面上，并位于 [安装位置]\home\svsystems\bin 文件夹中。
   * 在“管理帐户”选项卡中，单击“添加帐户”。
   * 添加已创建的帐户。 添加帐户后，在向保护组添加计算机时，需要提供这些凭据。

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>准备在 Linux 服务器上自动推送
1. 确保要保护的 Linux 计算机受支持，如[本地先决条件](#on-premises-prerequisites)中所述。 确保在要保护的计算机和运行进程服务器的管理服务器之间存在网络连接。
2. 创建可供进程服务器用来访问计算机的帐户。 帐户应该是源 Linux 服务器上的 root 用户。 这些凭据仅用于推送安装移动服务。

   * 打开 **cspsconfigtool**。 该帐户作为快捷方式位于桌面上，并位于 [安装位置]\home\svsystems\bin 文件夹中。
   * 在“管理帐户”选项卡中，单击“添加帐户”。
   * 添加已创建的帐户。 添加帐户后，在向保护组添加计算机时，需要提供这些凭据。
3. 确保源 Linux 服务器上的 /etc/hosts 文件包含用于将本地主机名映射到所有网络适配器关联的 IP 地址的条目。
4. 在要保护的计算机上安装最新的 openssh、openssh-server 和 openssl 包。
5. 确保 SSH 已启用且正在端口 22 上运行。
6. 在 sshd_config 文件中启用 SFTP 子系统与密码身份验证，如下所示：

   * 以 root 身份登录。
   * 在 /etc/ssh/sshd_config 文件中，找到以 PasswordAuthentication 开头的行。
   * 取消注释该行，并将值从 **no** 更改为 **yes**。
   * 找到以 **Subsystem** 开头的行，并取消注释该行。

     ![Linux 将重写默认值 no subsystems](./media/site-recovery-vmware-to-azure-classic/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>手动安装移动服务
C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository 中提供了安装程序。

| 源操作系统 | 移动服务安装文件 |
| --- | --- |
| Windows Server（仅限 64 位） |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4、6.5、6.6（仅限 64 位） |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3（仅限 64 位） |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4、6.5（仅限 64 位） |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-the-mobility-service-manually-on-a-windows-server"></a>在 Windows 服务器上手动安装移动服务
1. 下载并运行相关安装程序。
2. 在“开始之前”中选择“移动服务”。

    ![移动服务安装](./media/site-recovery-vmware-to-azure-classic/mobility3.png)
3. 在“配置服务器详细信息”中，指定管理服务器的 IP 地址，以及安装管理服务器组件时生成的通行短语。 可以通过在管理服务器上运行 **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** 来检索通行短语。

    ![移动服务](./media/site-recovery-vmware-to-azure-classic/mobility6.png)
4. 保留“安装位置”中的默认位置，然后单击“下一步”开始安装。
5. 在“安装进度”中检查安装，并在系统提示的情况下重新启动计算机。

还可以在命令行中输入以下文本进行安装：

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

在上面的命令中：

* /Role：必需。 指定是否应安装移动服务。
* /InstallLocation：必需。 指定服务安装位置。
* /PassphraseFilePath：必需。 指定配置服务器通行短语。
* /LogFilePath：必需。 指定日志安装程序文件的位置。

#### <a name="uninstall-the-mobility-service-manually"></a>手动卸载移动服务
可以使用控制面板中的“卸载或更改程序”或命令行卸载移动服务。

用于卸载移动服务的命令行是：

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="change-the-ip-address-of-the-management-server"></a>更改管理服务器的 IP 地址
运行向导之后，可以更改管理服务器的 IP 地址，如下所示：

1. 打开 hostconfig.exe 文件（位于桌面）。
2. 在“全局”选项卡中，可以更改管理服务器的 IP 地址。

   > [!NOTE]
   > 仅更改管理服务器的 IP 地址。 进行管理服务器通信的端口号必须是 443，“使用 HTTPS”必须处于启用状态。 不要更改通行短语。
   >
   >

    ![管理服务器 IP 地址](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-the-mobility-service-manually-on-a-linux-server"></a>在 Linux 服务器上手动安装移动服务
1. 将相应的 tar 存档复制到要保护的 Linux 计算机。 请参阅[手动安装移动服务](#install-the-mobility-service-manually)下面的表格来确定要使用哪个 tar 存档。
2. 打开 shell 程序，并通过运行 `tar -xvzf Microsoft-ASR_UA_8.5.0.0*` 将压缩的 tar 存档解压缩到本地路径
3. 在 tar 存档内容解压缩到的本地目录中创建名为 passphrase.txt 的文件。 为此，请在管理服务器上从 C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase 复制通行短语，然后通过在 shell 中运行 *`echo <passphrase> >passphrase.txt`*，将其保存在 passphrase.txt 中。
4. 输入以下命令安装移动服务：*`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*
5. 指定管理服务器的内部 IP 地址，确保选择端口 443。

#### <a name="install-the-mobility-service-from-the-command-line"></a>从命令行安装移动服务

在管理服务器上从 C:\Program Files (x86)\InMage Systems\private\connection 复制通行短语，再在管理服务器上将其另存为“passphrase.txt”。 然后运行以下命令。 在本示例中，管理服务器 IP 地址为 104.40.75.37，HTTPS 端口为 443：

在生产服务器上安装：

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

在主目标服务器上安装：

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>步骤 10：为计算机启用保护
若要启用保护，请将虚拟机和物理服务器添加到保护组。 在开始之前，请记下以下内容（如果你要保护 VMware 虚拟机）：

* 系统会每隔 15 分钟发现 VMware VM 一次，在发现之后，可能需要 15 分钟以上的时间虚拟机才会出现在 Site Recovery 门户中。
* 虚拟机上的环境更改（例如 VMware 工具安装）也可能需要 15 分钟以上的时间才能在 Site Recovery 中更新。
* 你可以在“**配置服务器**”选项卡上 vCenter 服务器/ESXi 主机的“**上次联系时间**”字段中检查上次发现 VMware VM 的时间。
* 如果在创建保护组后添加 vCenter 服务器或 ESXi 主机，Azure Site Recovery 门户可能需要 15 分钟以上的时间进行刷新，而虚拟机则需要 15 分钟以上的时间才能列在“向保护组添加计算机”对话框中。
* 如果要立即将计算机添加到保护组，而不想等待完成计划的发现，请选中配置服务器（请勿单击），然后单击“刷新”。

此外：

* 我们建议你对保护组进行体系结构设置，使得保护组能够镜像你的工作负荷。 例如，将运行特定应用程序的计算机添加到同一组。
* 在向保护组添加计算机时，进程服务器将自动推送移动服务并进行安装（如果尚未安装）。 需要根据上一步的描述来准备推送机制。

### <a name="add-machines-to-a-protection-group"></a>向保护组中添加计算机

1. 转到“受保护的项” > “保护组” > “计算机” > “添加计算机”。
2. 如果要保护 VMware 虚拟机，请在“选择虚拟机”中，选择负责管理虚拟机（或其运行所在的 EXSi 主机）的 vCenter 服务器，然后选择计算机。

    ![为虚拟机启用保护](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)
3. 如果要保护物理服务器，请在“添加物理计算机”向导的“选择虚拟机”中，提供 IP 地址和友好名称。 然后选择操作系统系列。

   ![为物理服务器启用保护](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
4. 在“指定目标资源”中选择用于复制的存储帐户，并选择是否应将设置用于所有工作负荷。 目前不支持高级存储帐户。

   > [!NOTE]
   > 不支持跨资源组移动使用 [Azure 门户](../storage/storage-create-storage-account.md)创建的存储帐户。                           
   > 用于部署 Site Recovery 的存储帐户不支持跨同一订阅中的资源组或跨订阅[迁移存储帐户](../azure-resource-manager/resource-group-move-resources.md)。
   >
   >

    ![配置目标设置](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)
5. 在“指定帐户”中，选择[设置](#install-the-mobility-service-with-push-installation)用于自动安装移动服务的帐户。

    ![指定帐户](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)
6. 单击复选标记以完成向保护组添加计算机，并对每个计算机启动初始复制。

   > [!NOTE]
   > 如果已准备好推送安装，则会自动在没有移动服务的计算机上安装移动服务，因为这些计算机已被添加到保护组。 安装完服务以后，会启动一个保护作业，然后该作业失败。 失败后，需要手动重启安装了移动服务的每个计算机。 重启后，保护作业重新启动，初始复制开始。
   >
   >

你可以在“**作业**”页上监视状态。

![在“作业”页上监视状态](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

也可以在“受保护的项” >  *保护组名称*  > “虚拟机”中监视保护状态。 初始复制完成后，数据得到同步，计算机状态更改为“受保护”。

![在“受保护的项”中监视状态](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)

## <a name="step-11-set-protected-machine-properties"></a>步骤 11：设置受保护的计算机属性
1. 在计算机的状态为“受保护”后，可以配置其故障转移属性。 在“保护组详细信息”中，选择计算机，然后打开“配置”选项卡。
2. Site Recovery 会自动为 Azure VM 提供属性建议，并检测本地网络设置。

    ![设置虚拟机属性](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)
3. 可在此处更改以下设置：

   * **Azure VM 名称**：这是在故障转移以后，要提供给 Azure 中的计算机的名称。 该名称必须符合 Azure 要求。
   * **Azure VM 大小**：网络适配器数目根据你为目标虚拟机指定的大小来确定。 有关大小和适配器的详细信息，请参阅[大小表格](../virtual-machines/linux/sizes.md)。 请注意：

     * 在修改虚拟机的大小并保存设置后，下一次打开“配置”选项卡时，网络适配器的数量将会改变。 目标虚拟机的网络适配器最小数目等于源虚拟机上的网络适配器最小数目。 网络适配器最大数目由虚拟机的大小决定。
       * 如果源计算机上的网络适配器数小于或等于目标计算机大小允许的适配器数，则目标的适配器数将与源相同。
       * 如果源虚拟机的适配器数大于目标大小允许的数目，则使用目标大小允许的最大数目。
        例如，如果源计算机有两个网络适配器，而目标计算机大小支持四个，则目标计算机将有两个适配器。 如果源计算机有两个适配器，但支持的目标大小只支持一个，则目标计算机只有一个适配器。
     * 如果虚拟机有多个网络适配器，所有适配器应连接到同一个 Azure 网络。
   * **Azure 网络**：必须指定一个在故障转移后供 Azure VM 连接的 Azure 网络。 如果不指定，则 Azure VM 不会连接到任何网络。 此外，如果想要从 Azure 故障回复到本地站点，则也需指定 Azure 网络。 故障回复需要在 Azure 网络和本地网络之间建立 VPN 连接。
   * **Azure IP 地址/子网**：为每个网络适配器选择可供 Azure VM 连接的子网。 请注意，如果源计算机的网络适配器已配置为使用静态 IP 地址，则可为 Azure VM 指定静态 IP 地址。 如果没有提供静态 IP 地址，将分配任何可用的 IP 地址。 如果指定了目标 IP 地址，但该地址已被 Azure 中的其他 VM 使用，则故障转移会失败。 如果源计算机的网络适配器已配置为使用 DHCP，则需要将 DHCP 作为 Azure 的设置。      

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>步骤 12：创建恢复计划并运行故障转移
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failover/player]
>
>

可以针对单台计算机运行故障转移，也可以对多台执行同一任务或运行同一工作负荷的虚拟机进行故障转移。 若要同时对多台计算机进行故障转移，需要将这些计算机添加到某个恢复计划中。

创建恢复计划：

1. 在“恢复计划”页上，单击“添加恢复计划”添加恢复计划。 指定该计划的详细信息，并选择“**Azure**”作为目标。

 ![配置恢复计划](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)
2. 在“选择虚拟机”中，选择保护组，然后选择该组中要添加到恢复计划的计算机。

 ![添加虚拟机](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

你可以自定义该计划以创建组，并排列顺序，恢复计划中的计算机以该顺序进行故障转移。 你还可以添加脚本和进行手动操作的提示。 可以手动创建脚本，也可以通过 [Azure 自动化 Runbook](site-recovery-runbook-automation.md) 创建脚本。 有关自定义恢复计划的详细信息，请参阅[创建恢复计划](site-recovery-create-recovery-plans.md)。

## <a name="run-a-failover"></a>运行故障转移
在运行故障转移之前：

* 确保管理服务器正在运行并可用， 否则故障转移会失败
* 如果运行非计划的故障转移：

  * 在运行非计划的故障转移之前，请尽可能关闭主计算机。 这样可确保不会同时运行源计算机和副本计算机。 如果要复制 VMware VM，在运行非计划的故障转移时，可以指定要求 Site Recovery 尽量关闭源计算机。 这可能有用，也可能无用，具体取决于主站点的状态。 如果要复制物理服务器，Site Recovery 不提供此选项。
  * 非计划的故障转移将停止从主计算机复制数据，因此在非计划的故障转移开始以后，将不会传输任何数据增量。
  * 如果想要在故障转移后连接到 Azure 中的副本虚拟机，可以在运行故障转移之前在源计算机上启用远程桌面连接。 然后允许通过防火墙进行 RDP 连接。 你还需要在故障转移后，在 Azure 虚拟机的公共终结点上启用 RDP。 请遵循[最佳做法](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)，确保 RDP 在故障转移后仍能正常运行。

> [!NOTE]
> 在为 Azure 执行故障转移时，若要获得最佳性能，请确保已在受保护计算机中安装 Azure 代理。 这有助于加快启动速度和诊断问题。 Azure 代理适用于 [Linux](https://github.com/Azure/WALinuxAgent) 和 [Windows](http://go.microsoft.com/fwlink/?LinkID=394789)。
>
>

### <a name="run-a-test-failover"></a>运行测试故障转移
在隔离的不影响生产环境的网络中运行测试性故障转移来模拟故障转移和恢复过程，常规复制仍正常工作。 测试性故障转移在源中启动，可以采用多种方式来运行它：

* **不指定 Azure 网络**：如果在没有网络的情况下运行测试性故障转移，该测试会检查虚拟机是否在 Azure 中正常启动和显示。 虚拟机在故障转移后不会连接到 Azure 网络。
* **指定 Azure 网络**：此类型的故障转移检查整个复制环境是否按预期运行，以及 Azure 虚拟机是否连接到指定的网络。

若要运行测试故障转移，请执行以下操作：

1. 在“恢复计划”页中，选择该计划，然后单击“测试性故障转移”。

 ![选择计划](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)
2. 在“确认测试性故障转移”中，选择“无”，表示不要使用 Azure 网络进行测试性故障转移；或者选择在故障转移后测试性 VM 将要连接到的网络。 单击复选标记开始故障转移。

 ![做出选择](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)
3. 在“**作业**”选项卡上监视故障转移进度。

 ![监视进度](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)
4. 故障转移完成后，应该还会看到副本 Azure 计算机显示在 Azure 门户上的“虚拟机”中。 如果想要启动一个 RDP 连接来连接到 Azure VM，需要在 VM 终结点上打开端口 3389。
5. 完成后，在故障转移到达“完成测试”阶段时，请单击“完成测试”完成故障转移。 在“说明”中，记录并保存与测试性故障转移相关联的任何观测结果。
6. 单击“**测试故障转移已完成**”以自动清理测试环境。 此操作完成后，测试故障转移会显示“完成”状态。 测试性故障转移期间自动创建的任何元素或 VM 将被删除。 如果测试故障转移持续了两周以上，系统会强行将其结束。

### <a name="run-an-unplanned-failover"></a>运行非计划的故障转移
非计划的故障转移从 Azure 启动，在主站点不可用的情况下也可以执行。

1. 在“恢复计划”页上选择该计划，然后单击“故障转移” > “非计划的故障转移”。

 ![选择计划](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)
2. 如果要复制 VMware 虚拟机，可以尝试关闭本地 VM。 系统会尽力完成此操作，不管此操作是否成功，故障转移都会继续。 如果不成功，错误详细信息会显示在“非计划的故障转移作业”下面的“作业”选项卡中。

 ![用于关闭本地 VM 的选项](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

 > [!NOTE]
 > 如果你复制的是物理服务器，则此选项不可用。 需要尽量手动关闭这些 VM。
 >
 >

3. 在“确认故障转移”中检查故障转移方向（故障转移到 Azure），然后选择要用于故障转移的恢复点。 配置复制属性时，如果启用了“多 VM”，可以恢复到与应用程序一致的或崩溃状态一致的最新恢复点。 你还可以选择“**自定义恢复点**”，以便恢复到更早的时间点。 单击复选标记开始故障转移。

 ![确认故障转移方向](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)
4. 等待非计划的故障转移作业完成。 在“**作业**”选项卡上，你可以监视故障转移进度。 即使在未计划的故障转移期间发生错误，恢复计划也会运行至完成。 此外，Azure 门户上的“虚拟机”中应会显示副本 Azure 计算机。

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>在故障转移后连接到复制的 Azure 虚拟机
故障转移后，若要在 Azure 中连接到复制的虚拟机，需要做好以下准备：

- 在主计算机上启用远程桌面连接。
- 将主计算机上的 Windows 防火墙设置为允许 RDP。
- 将 RDP 添加到 Azure 虚拟机的公共终结点。

有关此项设置的详细信息，请参阅 [Troubleshooting remote desktop connection after failover using ASR](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)（使用 ASR 排查故障转移后的远程桌面连接问题）。

## <a name="deploy-additional-process-servers"></a>部署附加的进程服务器
如果必须将部署扩展到 200 台源计算机以上，或者每日变动率总计超过 2 TB，则需要部署附加的进程服务器来处理流量。 若要设置附加的进程服务器，请查看[附加的进程服务器](#additional-process-servers)中的要求，然后根据以下说明设置该进程服务器。 设置该服务器后，可以通过配置源计算机来使用它。

### <a name="set-up-an-additional-process-server"></a>设置附加的进程服务器
按如下所述设置附加的进程服务器：

* 运行统一的向导，将管理服务器仅配置为进程服务器。
* 在只使用新进程服务器的情况下，如果想要管理数据复制，需要迁移受保护的计算机。

### <a name="install-the-process-server"></a>安装进程服务器
1. 在“快速启动”页中下载统一的安装文件来安装 Site Recovery 组件。 运行安装程序。
2. 在“开始之前”中选择“添加额外的进程服务器以扩大部署”。

 ![添加进程服务器](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)
3. 像[设置](#step-5-install-the-management-server)第一个管理服务器时一样完成向导。
4. 在“配置服务器详细信息”中，输入在其上安装了配置服务器的原始管理服务器的 IP 地址，然后输入通行短语。 如果没有通行短语，请在原始管理服务器上运行 **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** 来获取通行短语。

 ![配置服务器详细信息](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>对计算机进行迁移，以使用新的进程服务器
1. 打开“配置服务器” > “服务器” >  *原始管理服务器的名称*  > “服务器详细信息”。

 ![服务器详细信息](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)
2. 在“进程服务器”列表中，选择要更改的服务器旁边的“更改进程服务器”。

 ![更新进程服务器](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)
3. 依次选择“更改进程服务器”、“目标进程服务器”、新的管理服务器。 然后选择新进程服务器将要处理的虚拟机。 单击信息图标，获取服务器的相关信息。 随后会显示将每个所选虚拟机复制到新进程服务器所需的平均空间，以帮助你做出负载决策。 单击复选标记，开始复制到新的进程服务器。

 ![更改进程服务器](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

## <a name="vmware-permissions-for-vcenter-access"></a>VMware 进行 vCenter 访问的权限
进程服务器可以自动发现 vCenter 服务器上的 VM。 若要执行自动发现，需在 vCenter 级别定义一个角色 (Azure_Site_Recovery)，以便 Site Recovery 能够访问 vCenter 服务器。 如果只需将 VMware 计算机迁移到 Azure 而不需要从 Azure 进行故障回复，则可定义一个具有足够权限的只读角色。 可根据[步骤 6：为 vCenter 服务器设置凭据](#step-6-set-up-credentials-for-the-vcenter-server)中所述设置权限。 下表汇总了角色权限：

| **角色** | **详细信息** | **权限** |
| --- | --- | --- |
| Azure_Site_Recovery 角色 |VMware VM 发现 |为 v-Center 服务器分配这些特权：<br/><br/>数据存储：分配空间、浏览数据存储、低级别文件操作、删除文件、更新虚拟机文件<br/><br/>网络：网络分配<br/><br/>资源：将虚拟机分配给资源池、迁移电源关闭的虚拟机、迁移已打开的虚拟机<br/><br/>任务：创建任务、更新任务<br/><br/>虚拟机 > 配置<br/><br/>虚拟机 > 交互 > 回答问题、设备连接、配置 CD 媒体、配置软盘媒体、关机、开机、安装 VMware 工具<br/><br/>虚拟机 > 库存 > 创建、注册、取消注册<br/><br/>虚拟机 > 预配 > 允许虚拟机下载、允许虚拟机文件上传<br/><br/>虚拟机 > 快照 > 删除快照 |
| vCenter 用户角色 |VMware VM 发现/在不关闭源 VM 的情况下进行故障转移 |为 v-Center 服务器分配这些特权：<br/><br/>数据中心对象 > 传播到子对象、角色=只读 <br/><br/>用户在数据中心级别进行分配，因此具有数据中心内所有对象的访问权限。 若要限制访问权限，请将具有“传播到子对象”权限的“禁止访问”角色分配给子对象（ESX 主机、数据存储、VM 和网络）。 |
| vCenter 用户角色 |故障转移和故障回复 |为 v-Center 服务器分配这些特权：<br/><br/>数据中心对象 – 传播到子对象、角色=Azure_Site_Recovery<br/><br/>用户在数据中心级别进行分配，并因此具有数据中心内所有对象的访问权限。  若要限制访问权限，请将具有“传播到子对象”权限的**禁止访问**角色分配给子对象（ESX 主机、数据存储、VM 和网络）。 |

## <a name="third-party-software-notices-and-information"></a>第三方软件通知和信息
<!--Do Not Translate or Localize-->

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

完整文件可以在 [ Microsoft 下载中心](http://go.microsoft.com/fwlink/?LinkId=529428)上找到。 Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## <a name="next-steps"></a>后续步骤
[详细了解故障回复](site-recovery-failback-azure-to-vmware-classic.md)，以便将 Azure 中运行的已故障转移的计算机回复到本地环境。

