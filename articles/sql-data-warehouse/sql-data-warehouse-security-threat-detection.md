---
title: "SQL 数据仓库威胁检测入门"
description: "如何开始使用威胁检测"
services: sql-data-warehouse
documentationcenter: 
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: c9073dd9-6c62-4735-8457-dfb9f859c900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: security
ms.date: 10/31/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f4a2376fe4fb710d031c35ca7fdbf4c7bb0f3caa
ms.lasthandoff: 04/03/2017


---
# <a name="get-started-with-threat-detection"></a>威胁检测入门
> [!div class="op_single_selector"]
> * [审核](sql-data-warehouse-auditing-overview.md)
> * [威胁检测](sql-data-warehouse-security-threat-detection.md)
> 
> 

## <a name="overview"></a>概述
威胁检测会检测异常的数据库活动，指出数据库有潜在的安全威胁。 威胁检测目前以预览版提供，SQL 数据仓库支持此功能。

威胁检测提供新的安全层，在发生异常活动时会提供安全警报，让客户检测潜在威胁并做出响应。 用户可以使用 [Azure SQL 数据仓库审核](sql-data-warehouse-auditing-overview.md)来探查可疑事件，确定这些可疑事件是否是因为有人尝试访问、破坏或利用数据仓库中的数据而生成的。
你不必是安全专家，也不需要管理先进的安全监视系统，就能使用威胁检测轻松解决数据仓库的潜在威胁。

威胁检测会检测异常的数据库活动，指出潜在的 SQL 注入企图。 SQL 注入是 Internet 上常见的 Web 应用程序安全问题之一，用于攻击数据驱动的应用程序。 攻击者利用应用程序漏洞将恶意 SQL 语句注入应用程序入口字段，以破坏或修改数据库中的数据。

## <a name="set-up-threat-detection-for-your-database"></a>为数据库设置威胁检测
1. 启动 Azure 门户 [https://portal.azure.com](https://portal.azure.com)。
2. 导航到你要监视的 SQL 数据仓库的配置边栏选项卡。 在“设置”边栏选项卡中，选择“审核和威胁检测”。
   
    ![导航窗格][1]
3. 在“审核和威胁检测”配置边栏选项卡中，将审核设置为“打开”，随后会显示威胁检测设置。
   
    ![导航窗格][2]
4. 将威胁检测设置为“打开”。
5. 配置在检测到异常数据仓库活动时，要接收安全警报的电子邮件列表。
6. 在“审核和威胁检测”配置边栏选项卡中单击“保存”，以保存新的或更新的审核与威胁检测策略。
   
    ![导航窗格][3]

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>检测到可疑事件时探查异常数据仓库活动
1. 检测到异常数据库活动时，你将收到电子邮件通知。 <br/>
   电子邮件将提供可疑安全事件的相关信息，包括异常活动的性质、数据库名称、服务器名称和事件时间。 此外，还会提供可能原因和建议操作的相关信息，以帮助你调查和缓解数据库的潜在威胁。<br/>
   
    ![导航窗格][4]
2. 在电子邮件中，单击“Azure SQL 审核日志”链接以启动 Azure 经典门户，并显示发生可疑事件前后的相关审核记录。
   
    ![导航窗格][5]
3. 单击审核记录可以查看有关可疑数据库活动的详细信息，例如 SQL 语句、失败原因和客户端的 IP。
   
    ![导航窗格][6]
4. 在“审核记录”边栏选项卡中，单击“在 Excel 中打开”以打开预配置的 Excel 模板，以导入发生可疑事件前后的审核日志，并运行更深入的分析。<br/>
   **注意：**在 Excel 2010 或更高版本中，需要配置 Power Query 和“快速合并”****设置
   
    ![导航窗格][7]
5. 若要配置“快速合并”设置 - 请在“POWER QUERY”功能区选项卡中，选择“选项”以显示“选项”对话框。 选择“隐私”部分，然后选择第二个选项 -“忽略隐私级别并潜在地改善性能”：
   
    ![导航窗格][8]
6. 若要加载 SQL 审核日志，请确保设置选项卡中的参数设置正确，选择“数据”功能区，然后单击“全部刷新”按钮。
   
    ![导航窗格][9]
7. 结果将显示在“SQL 审核日志”工作表中，使你能够对检测到的异常活动运行更深入的分析，并缓解应用程序中安全事件造成的影响。

<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png

