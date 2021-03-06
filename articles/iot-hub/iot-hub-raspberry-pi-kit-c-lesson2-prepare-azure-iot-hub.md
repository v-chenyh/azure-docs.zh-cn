---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 第 2 课：注册设备 | Microsoft Docs"
description: "使用 Azure CLI 创建资源组、创建 Azure IoT 中心，以及在 Azure IoT 中心注册 Pi。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "raspberry pi 云, pi 云连接"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: 4bcfb071-b3ae-48cc-8ea5-7e7434732287
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: d7bc143b509e84d50f3ce0164d31ae51a26f75d4
ms.lasthandoff: 01/24/2017


---
# <a name="create-your-iot-hub-and-register-raspberry-pi-3"></a>创建 IoT 中心并注册 Raspberry Pi 3
## <a name="what-you-will-do"></a>执行的操作
* 创建资源组。
* 在资源组中创建 Azure IoT 中心。
* 使用 Azure 命令行接口 (Azure CLI) 将 Raspberry Pi 3 添加到 Azure IoT 中心。

使用 Azure CLI 将 Pi 添加到 IoT 中心时，服务会为 Pi 生成一个密钥，以便通过服务进行身份验证。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
本文介绍：
* 如何使用 Azure CLI 创建 IoT 中心。
* 如何在 IoT 中心为 Pi 创建设备标识。

## <a name="what-you-need"></a>需要什么
* 一个 Azure 帐户
* 安装了 Azure CLI 的 Mac 或 Windows 计算机

## <a name="create-your-iot-hub"></a>创建 IoT 中心
Azure IoT 中心可帮助你连接、监视并管理数百万 IoT 资产。 若要创建 IoT 中心，请执行以下步骤：

1. 通过运行以下命令登录到 Azure 帐户：

   ```bash
   az login
   ```

   在成功登录后会列出你的所有可用订阅。

2. 通过运行以下命令设置要使用的默认订阅：

   ```bash
   az account set --subscription {subscription id or name}
   ```

   可以在 `az login` 或 `az account list` 命令的输出中找到 `subscription ID or name`。

3. 通过运行以下命令注册提供程序。 资源提供程序是为应用程序提供资源的服务。 必须先注册提供程序，然后才能部署该提供程序提供的 Azure 资源。

   ```bash
   az provider register -n "Microsoft.Devices"
   ```
4. 通过运行以下命令在 West US 区域中创建名为 iot-sample 的资源组：

   ```bash
   az group create --name iot-sample --location westus
   ```

   `westus` 是你在其中创建资源组的位置。 如果希望使用其他位置，可以运行 `az account list-locations -o table` 来查看 Azure 支持的所有位置。
 
5. 通过运行以下命令在 iot-sample 资源组中创建一个 IoT 中心：

   ```bash
   az iot hub create --name {my hub name} --resource-group iot-sample
   ```

   默认情况下，该工具在免费定价层中创建 IoT 中心。 有关详细信息，请参阅 [Azure IoT 中心定价](https://azure.microsoft.com/pricing/details/iot-hub/)。

> [!NOTE]
> IoT 中心的名称必须是全局唯一的。 在 Azure 订阅下只能创建一个 F1 版本的 Azure IoT 中心。

## <a name="register-pi-in-your-iot-hub"></a>在 IoT 中心内注册 Pi
每个将消息发送到 IoT 中心并从 IoT 中心接收消息的设备都必须使用唯一 ID 注册。

运行以下命令，在中心注册 Pi：

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="summary"></a>摘要
你已创建了一个 IoT 中心并在该 IoT 中心内注册了 Pi 的设备标识。 现在可以学习如何从 Pi 向 IoT 中心发送消息了。

## <a name="next-steps"></a>后续步骤
[创建 Azure Function App 和 Azure 存储帐户来处理和存储 IoT 中心消息](iot-hub-raspberry-pi-kit-c-lesson3-deploy-resource-manager-template.md)。


