---
title: "基于 URL 的内容路由概述 | Microsoft Docs"
description: "本页提供基于应用程序网关 URL 的内容路由、UrlPathMap 配置和 PathBasedRouting 规则的概述。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: 
ms.assetid: 4409159b-e22d-4c9a-a103-f5d32465d163
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 4b649379ce41a4d6cea93b42fc492fdc0940e689
ms.contentlocale: zh-cn
ms.lasthandoff: 06/08/2017


---
# <a name="url-path-based-routing-overview"></a>基于 URL 路径的路由概述

基于 URL 路径的路由可让你根据请求的 URL 路径，将流量路由到后端服务器池。 

方案之一是将针对不同内容类型的请求路由到不同的后端服务器池。

在以下示例中，应用程序网关针对 contoso.com 从三个后端服务器池提供流量，例如：VideoServerPool、ImageServerPool 和 DefaultServerPool。

![imageURLroute](./media/application-gateway-url-route-overview/figure1.png)

对 http://contoso.com/video* 的请求会路由到 VideoServerPool，对 http://contoso.com/images* 的请求会路由到 ImageServerPool。 如果没有任何路径模式匹配，则选择 DefaultServerPool。
    
## <a name="urlpathmap-configuration-element"></a>UrlPathMap 配置元素

UrlPathMap 元素用于指定后端服务器池映射的路径模式。 以下代码示例是模板文件中 urlPathMap 元素的代码片段。

```json
"urlPathMaps": [{
    "name": "{urlpathMapName}",
    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/urlPathMaps/{urlpathMapName}",
    "properties": {
        "defaultBackendAddressPool": {
            "id": "/subscriptions/    {subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName1}"
        },
        "defaultBackendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpSettingsList/{settingname1}"
        },
        "pathRules": [{
            "name": "{pathRuleName}",
            "properties": {
                "paths": [
                    "{pathPattern}"
                ],
                "backendAddressPool": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendAddressPools/{poolName2}"
                },
                "backendHttpsettings": {
                    "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/backendHttpsettingsList/{settingName2}"
                }
            }
        }]
    }
}]
```

> [!NOTE]
> PathPattern：此设置是要匹配的路径模式列表。 每个模式必须以 / 开头，只允许在后接“/”的末尾处添加“*”。 发送到路径匹配器的字符串不会在第一个 ? 或 # 之后包含任何文本，这些字符在这里是不允许的。

有关详细信息，可以查看[使用基于 URL 的路由的 Resource Manager 模板](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing)。

## <a name="pathbasedrouting-rule"></a>PathBasedRouting 规则

PathBasedRouting 类型的 RequestRoutingRule 可用于将侦听器绑定到 urlPathMap。 此侦听器收到的所有请求将根据 urlPathMap 中指定的策略进行路由。
PathBasedRouting 规则的代码段：

```json
"requestRoutingRules": [
    {

"name": "{ruleName}",
"id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/requestRoutingRules/{ruleName}",
"properties": {
    "ruleType": "PathBasedRouting",
    "httpListener": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/httpListeners/<listenerName>"
    },
    "urlPathMap": {
        "id": "/subscriptions/{subscriptionId}/../microsoft.network/applicationGateways/{gatewayName}/ urlPathMaps/{urlpathMapName}"
    },

}
    }
]
```

## <a name="next-steps"></a>后续步骤

了解基于 URL 的内容路由之后，请转到[使用基于 URL 的路由创建应用程序网关](application-gateway-create-url-route-portal.md)，使用 URL 路由规则创建应用程序网关。

