---
title: "流分析：实时欺诈检测 | Microsoft 文档"
description: "了解如何通过流分析创建实时欺诈行为检测解决方案。 使用事件中心进行实时事件处理。"
keywords: "异常检测、欺诈检测、实时异常检测"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: c10dd53f-d17a-4268-a561-cb500a8c04eb
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: deb528faa29a1e547286b0560d0f2b8decf3f90a
ms.lasthandoff: 05/01/2017


---
# <a name="get-started-using-azure-stream-analytics-real-time-fraud-detection"></a>Azure 流分析入门：实时检测欺诈行为
了解如何创建端到端解决方案，以便通过 Azure 流分析实时检测欺诈行为。 将事件引入 Azure 事件中心、编写用于聚合或提醒的流分析查询，并将结果发送到输出接收器，以便通过实时处理来分析数据。 虽然介绍的是电信方面的实时异常检测，但作为示例的技术同样适用于其他类型的欺诈检测，例如盗窃信用卡或身份的情况。

流分析是一种完全托管的服务，可以在云中通过流式数据处理低延迟、高度可用、可伸缩且复杂的事件。 有关详细信息，请参阅 [Azure 流分析简介](stream-analytics-introduction.md)。

## <a name="scenario-telecommunications-and-sim-fraud-detection-in-real-time"></a>方案：实时远程通信和 SIM 欺诈检测
电信公司的传入呼叫数据量很大。 公司需要从其数据中获取以下信息：

* 将此数据减少到一个可管理的数量，然后分析特定时间和特定地理区域客户的使用情况。
* 实时检测 SIM 欺诈行为（在差不多同一时间出现多个同一身份发起的呼叫，但这些呼叫却位于不同的地理区域），以便公司快速应对，向客户发送通知或关闭相应的服务。

Canonical 物联网 (IoT) 方案具有传感器的大量遥测或数据。 客户希望聚合数据或实时接收有关异常的警报。

## <a name="prerequisites"></a>先决条件
* 从 Microsoft 下载中心下载 [TelcoGenerator.zip](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)
* 可选：从 [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator) 获取事件生成器的源代码

## <a name="create-azure-event-hubs-input-and-consumer-group"></a>创建 Azure 事件中心输入和使用者组
示例应用程序将生成事件并将其推送到进行实时处理的事件中心实例。 服务总线事件中心是适合流分析的首选事件引入方法。 在 [Azure 服务总线文档](/azure/service-bus/)中可以了解关于事件中心的详细信息。

创建事件中心的步骤：

1. 在“[Azure 门户中](https://manage.windowsazure.com/)，依次单击“新建” > “应用服务” > “服务总线” > “事件中心” > “快速创建”。 提供创建新事件中心所需的名称、区域以及新的或现有的命名空间。  
2. 最佳做法是让每个流分析作业都从单个事件中心使用者组读取数据。 我们稍后将引导你完成创建使用者组的过程。 [了解关于使用者组的详细信息](https://msdn.microsoft.com/library/azure/dn836025.aspx)。 若要创建使用者组，请转到新创建的事件中心，单击“使用者组”选项卡、单击页面底部的“创建”，然后为使用者组提供名称。
3. 若要授予对事件中心的访问权限，需创建共享访问策略。 单击事件中心的“配置”选项卡。
4. 在“共享访问策略”下，创建具有“管理”权限的新策略。

   ![共享访问策略：你可以在其中使用管理权限创建策略。](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-shared-access-policies.png)
5. 单击页面底部的“保存”。
6. 转到“仪表板”，单击页面底部的“连接信息”，然后复制并保存该连接信息。

## <a name="configure-and-start-the-event-generator-application"></a>配置并启动事件生成器应用程序
我们提供了一个客户端应用程序，该程序会生成示例性的传入呼叫元数据并将其推送到事件中心。 使用以下步骤设置此应用程序。  

1. 下载 [TelcoGenerator.zip 文件](http://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip)，然后将其解压缩到某个目录。

   > [!NOTE]
   > Windows 可能会阻止下载的 zip 文件。 右键单击该文件并选择“属性”。 如果看到“此文件来自其他计算机，可能被阻止以帮助保护该计算机。”的消息，则选择“取消阻止”框，然后单击该 zip 文件上的“应用”。
   >
   > 
2. 将 telcodatagen.exe.config 中的 Microsoft.ServiceBus.ConnectionString 和 EventHubName 值替换为事件中心的连接字符串和名称。

   从 Azure 门户复制的连接字符串会将连接名称放在末尾。 确保从“add key=”字段中删除“;EntityPath=<value>”。
3. 启动应用程序。 用法如下：

   telcodatagen.exe [#NumCDRsPerHour] [SIM Card Fraud Probability] [#DurationHours]

以下示例将生成 1000 个事件，在为时两小时的过程中，有 20% 的可能性会出现欺诈行为。

    telcodatagen.exe 1000 .2 2

你会看到记录被发送到事件中心。 将在此实时欺诈检测应用程序中使用的某些关键字段定义如下：

| 记录      | 定义                               |
| ----------- | ---------------------------------------- |
| CallrecTime | 调用开始时间的时间戳。       |
| SwitchNum   | 用于连接呼叫的电话交换机。 |
| CallingNum  | 呼叫方的电话号码。              |
| CallingIMSI | 国际移动用户标识 (IMSI)。  呼叫方的唯一标识符。 |
| CalledNum   | 呼叫接收人的电话号码。      |
| CalledIMSI  | 国际移动用户标识 (IMSI)。  呼叫接收人的唯一标识符。 |

## <a name="create-a-stream-analytics-job"></a>创建流分析作业
现在，我们已获得远程通信事件流，因此可以设置一个流分析作业来实时分析这些事件。

### <a name="provision-a-stream-analytics-job"></a>预配流分析作业
1. 在 Azure 门户中，依次单击“新建” > “数据服务” > “流分析” > “快速创建”。
2. 指定以下值，然后单击“创建流分析作业”：

   * **作业名称**：输入作业名称。
   * **区域**：选择要运行作业的区域。 考虑将作业和事件中心放在同一区域，以确保获得更好的性能，并确保在不同区域之间传输数据时不需付费。
   * **存储帐户**：选择要使用的 Azure 存储帐户，以便为所有在此区域运行的流分析作业存储监视数据。 可以选择现有存储帐户，也可以创建新存储帐户。
3. 单击左窗格中的“流分析”，列出流分析作业。

   ![流分析服务图标](./media/stream-analytics-real-time-fraud-detection/stream-analytics-service-icon.png)

   新作业显示的状态为“已创建”。 请注意，页面底部的“启动”按钮已禁用。 你必须先配置作业输入、输出和查询，然后才能启动作业。

### <a name="specify-job-input"></a>指定作业输入
1. 在流分析作业中，单击页面顶部的“输入”，然后单击“添加输入”。 此时会打开一个对话框，引导你完成设置输入的若干步骤。
2. 单击“数据流”，然后单击右侧按钮。
3. 选择“事件中心”，然后单击右侧按钮。
4. 在第三页中键入或选择以下值：

   * **输入别名**：为此作业输入友好名称，如 *CallStream*。 请注意，你需要在后面的查询中使用此名称。
   * **事件中心**：如果创建的事件中心与流分析作业属于同一订阅，请选择事件中心所在的命名空间。

     如果事件中心属于其他订阅，请选择“使用其他订阅的事件中心”，然后手动输入以下项目的相关信息：**服务总线命名空间**、**事件中心名称**、**事件中心策略名称**、**事件中心策略密钥**以及**事件中心分区计数**。
   * **事件中心名称**：选择事件中心名称。
   * **事件中心策略名称**：选择此前在本教程中创建的事件中心策略。
   * **事件中心使用者组**：键入此前在本教程中创建的使用者组。
5. 单击右侧按钮。
6. 指定以下值：

   * **事件序列化程序格式**：JSON
   * **编码**：UTF8
7. 单击“检查”按钮以添加此源并验证流分析是否可以成功连接到事件中心。

### <a name="specify-job-query"></a>指定作业查询
流分析支持简单的声明性查询模型，用于描述实时处理的转换。 若要了解有关语言的详细信息，请参阅 [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/dn834998.aspx)。 本教程将帮助你创作和测试多个可通过实时调用数据流完成的查询。

#### <a name="optional-sample-input-data"></a>可选：示例输入数据
若要针对实际的作业数据来验证查询，可使用“示例数据”功能从流中提取事件，然后创建包含要测试事件的 .JSON 文件。  以下步骤显示如何执行该操作。 我们还提供了一个用于测试的示例性 [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) 文件。

1. 选择事件中心输入，然后单击页面底部的“示例数据”。
2. 在出现的对话框中，指定开始收集数据的“开始时间”，以及使用额外数据的“持续时间”。
3. 单击相应的“勾选”按钮，开始对输入中的数据取样。  生成数据文件可能需要一到两分钟。  完成该过程后，请单击“详细信息”，下载生成的 .JSON 文件，并将其保存。

   ![在 JSON 文件中下载并保存已处理的数据](./media/stream-analytics-real-time-fraud-detection/stream-analytics-download-save-json-file.png)

#### <a name="pass-through-query"></a>传递查询
如果你想要将每个事件存档，可使用传递查询读取事件或消息有效负载中的所有字段。 开始时，进行简单的传递查询来投射事件中的所有字段。

1. 单击流分析作业页顶部的“查询”。
2. 将以下内容添加到代码编辑器：

     SELECT * FROM CallStream

   > [!IMPORTANT]
   > 请确保输入源的名称与此前指定的输入的名称相匹配。
   >
   > 
3. 单击查询编辑器下的“测试”。
4. 提供测试文件。 使用在之前步骤中创建的文件，或者使用 [telco.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/SampleDataFiles/Telco.json)。
5. 单击“勾选”按钮，然后就会看到结果显示在查询定义下方。

   ![查询定义结果](./media/stream-analytics-real-time-fraud-detection/stream-analytics-sim-fraud-output.png)

### <a name="column-projection"></a>列投影
现在，我们将返回的字段缩减成较小的集合。

1. 在代码编辑器中将查询更改为：

     SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNum, CalledNum   FROM CallStream
2. 单击查询编辑器下的“重新运行”，查看查询结果。

   ![查询编辑器中的输出。](./media/stream-analytics-real-time-fraud-detection/stream-analytics-query-editor-output.png)

### <a name="count-of-incoming-calls-by-region-tumbling-window-with-aggregation"></a>按区域计算传入呼叫数：带聚合功能的翻转窗口
为了比较按区域划分的传入呼叫数，我们将使用 [TumblingWindow](https://msdn.microsoft.com/library/azure/dn835055.aspx) 每五秒获取通过 **SwitchNum** 分组的传入呼叫计数。

1. 在代码编辑器中将查询更改为：

     SELECT System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount   FROM CallStream TIMESTAMP BY CallRecTime   GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum

   此查询使用 **Timestamp By** 关键字指定在临时计算中使用的有效负载的时间戳字段。 如果未指定此字段，将根据每个事件到达事件中心的时间执行窗口化操作。 请参阅[流分析查询语言参考中的“到达时间与应用程序时间”](https://msdn.microsoft.com/library/azure/dn834998.aspx)。

   请注意，可以使用 **System.Timestamp** 属性访问每个窗口结束时的时间戳。
2. 单击查询编辑器下的“重新运行”，查看查询结果。

   ![Timestand By 的查询结果](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-rerun.png)

### <a name="sim-fraud-detection-with-a-self-join"></a>使用自联接进行 SIM 欺诈行为检测
为了确定可能存在的欺诈性使用情况，我们需要查找在不到 5 秒的时间内，由同一用户发出但位置不同的呼叫。  我们会让呼叫事件流自行[联接](https://msdn.microsoft.com/library/azure/dn835026.aspx)，看是否存在此类情况。

1. 在代码编辑器中将查询更改为：

     SELECT System.Timestamp as Time, CS1.CallingIMSI, CS1.CallingNum as CallingNum1,   CS2.CallingNum as CallingNum2, CS1.SwitchNum as Switch1, CS2.SwitchNum as Switch2   FROM CallStream CS1 TIMESTAMP BY CallRecTime   JOIN CallStream CS2 TIMESTAMP BY CallRecTime   ON CS1.CallingIMSI = CS2.CallingIMSI   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5   WHERE CS1.SwitchNum != CS2.SwitchNum
2. 单击查询编辑器下的“重新运行”，查看查询结果。

   ![联接的查询结果](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-query-editor-join.png)

### <a name="create-output-sink"></a>创建输出接收器
现在，我们已经定义了事件流、用于引入事件的事件中心输入，以及用于通过流执行转换的查询，最后一步是为作业定义输出接收器。 我们会将发生欺诈行为的事件写入 Azure Blob 存储。

使用以下步骤为 Blob 存储创建容器（如果还没有）。

1. 使用现有存储帐户，或者通过单击“新建”>“数据服务”>“存储”>“快速创建”，然后按照说明操作，创建新存储帐户。
2. 选择存储帐户，单击页面顶部的“容器”，然后单击“添加”。
3. 指定容器的“名称”，然后将其“访问权限”设置为“公共 Blob”。

## <a name="specify-job-output"></a>指定作业输出
1. 在流分析作业中，单击页面顶部的“输出”，然后单击“添加输出”。 此时会打开一个对话框，引导你完成设置输出的若干步骤。
2. 单击“Blob 存储”，然后单击右侧按钮。
3. 在第三页中键入或选择以下值：

   * **输出别名**：输入此作业输出的友好名称。
   * **订阅**：如果创建的 Blob 存储与流分析作业属于同一订阅，请单击“使用当前订阅中的存储帐户”。 如果存储属于其他订阅，请单击“使用其他订阅中的存储帐户”，然后针对“存储帐户”、“存储帐户密钥”和“容器”手动输入相关信息。
   * **存储帐户**：选择存储帐户的名称。
   * **容器**：选择容器名称。
   * **文件名前缀**：键入写入 blob 输出时要使用的文件前缀。
4. 单击右侧按钮。
5. 指定以下值：

   * **事件序列化程序格式**：JSON
   * **编码**：UTF8
6. 单击相应“勾选”按钮以添加此源，并验证流分析是否可以成功连接到存储帐户。

## <a name="start-job-for-real-time-processing"></a>启动要实时处理的作业
由于作业输入、查询和输出均已指定，因此我们可以启动流分析作业来实时检测欺诈行为。

1. 在作业“仪表板”上，单击页面底部的“启动”。
2. 在打开的对话框中，单击“作业开始时间”，然后单击对话框底部的“勾选”按钮。 作业状态将更改为“正在启动”，然后很快变为“正在运行”。

## <a name="view-fraud-detection-output"></a>查看欺诈检测输出
在将欺诈性事件实时写入到输出中时，使用 [Azure 存储资源管理器](http://storageexplorer.com/)或 [Azure 资源管理器](http://www.cerebrata.com/products/azure-explorer/introduction)之类的工具查看这些事件。  

![欺诈行为检测：实时查看欺诈性事件](./media/stream-analytics-real-time-fraud-detection/stream-ananlytics-view-real-time-fraudent-events.png)

## <a name="get-support"></a>获取支持
如需进一步的帮助，请试用我们的 [Azure 流分析论坛](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)。

## <a name="next-steps"></a>后续步骤
* [Azure 流分析简介](stream-analytics-introduction.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)


