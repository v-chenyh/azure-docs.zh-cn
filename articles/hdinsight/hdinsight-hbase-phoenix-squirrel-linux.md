---
title: "将 Apache Phoenix 和 SQuirreL 用于 Azure HDInsight (HBase) | Microsoft Docs"
description: "了解如何在 HDInsight 中使用 Apache Phoenix，以及如何在工作站上安装和配置 SQuirreL 以连接到 HDInsight 中的 HBase 群集。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: cda0f33b-a2e8-494c-972f-ae0bb482b818
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 04e078d662c861d5c587c571a42478603e291587
ms.lasthandoff: 03/25/2017


---
# <a name="use-apache-phoenix-with-linux-based-hbase-clusters-in-hdinsight"></a>将 Apache Phoenix 与 HDinsight 中基于 Linux 的 HBase 群集配合使用
了解如何在 HDInsight 中使用 [Apache Phoenix](http://phoenix.apache.org/)，以及如何使用 SQLLine。 有关 Phoenix 的详细信息，请参阅[在 15 分钟或更短时间内了解 Phoenix](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html)。 有关 Phoenix 语法，请参阅 [Phoenix 语法](http://phoenix.apache.org/language/index.html)。

> [!NOTE]
> 有关 HDInsight 中的 Phoenix 版本信息，请参阅 [HDInsight 提供的 Hadoop 群集版本有有何变化？](hdinsight-component-versioning.md)。
>
>

## <a name="use-sqlline"></a>使用 SQLLine
[SQLLine](http://sqlline.sourceforge.net/) 是用于执行 SQL 的命令行实用工具。

### <a name="prerequisites"></a>先决条件
在使用 SQLLine 之前，必须先准备好以下各项：

* **HDInsight 中的 HBase 群集**。 有关预配 HBase 群集的信息，请参阅 [HDInsight 中的 Apache HBase 入门][hdinsight-hbase-get-started]。
* **通过远程桌面协议连接到 HBase 群集**。 有关说明，请参阅[使用 Azure 经典门户在 HDInsight 中管理 Hadoop 群集][hdinsight-manage-portal]。

在连接到 HBase 群集时，你需要连接到 Zookeeper 之一。 每个 HDInsight 群集有 3 个 Zookeeper。

**找出 Zookeeper 主机名**

1. 通过浏览到 **https://<ClusterName>.azurehdinsight.net** 打开 Ambari。
2. 输入 HTTP（群集）用户名和密码以登录。
3. 单击左侧菜单中的 **ZooKeeper**。 你应看到列出了 3 个 **ZooKeeper 服务器**。
4. 单击列出的其中一个 **ZooKeeper 服务器**。 在“摘要”窗格中，找到**主机名**。 它类似于 *zk1 jdolehb.3lnng4rcvp5uzokyktxs4a5dhd.bx.internal.cloudapp.net*。

**使用 SQLLine**

1. 使用 SSH 连接到群集。 有关详细信息，请参阅 [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)（对 HDInsight 使用 SSH）。

2. 从 SSH 运行以下命令以运行 SQLLine：

        cd /usr/hdp/2.2.9.1-7/phoenix/bin
        ./sqlline.py <ClusterName>:2181:/hbase-unsecure
3. 运行以下命令以创建 HBase 表，并插入一些数据：

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

        !quit

有关详细信息，请参阅 [SQLLine 手册](http://sqlline.sourceforge.net/#manual)和 [Phoenix 语法](http://phoenix.apache.org/language/index.html)。

## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何在 HDInsight 中使用 Apache Phoenix。  若要了解详细信息，请参阅

* [HDInsight HBase 概述][hdinsight-hbase-overview]：HBase 是构建于 Hadoop 上的 Apache 开源 NoSQL 数据库，用于为大量非结构化和半结构化数据提供随机访问和高度一致性。
* [在 Azure 虚拟网络上设置 HBase 群集][hdinsight-hbase-provision-vnet]：通过虚拟网络集成，可将 HBase 群集部署到应用程序所在的虚拟网络，以便应用程序直接与 HBase 进行通信。
* [在 HDInsight 中配置 HBase 复制](hdinsight-hbase-replication.md)：了解如何跨两个 Azure 数据中心配置 HBase 复制。
* [在 HDInsight 中使用 HBase 分析 Twitter 观点][hbase-twitter-sentiment]：了解如何通过在 HDInsight 的 Hadoop 群集中使用 HBase 对大数据执行实时[情绪分析](http://en.wikipedia.org/wiki/Sentiment_analysis)。

[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png

