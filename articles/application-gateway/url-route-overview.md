---
title: Azure Application Gateway URL 기반 콘텐츠 라우팅 개요
description: 이 문서에서는 Application Gateway URL 기반 콘텐츠 라우팅, UrlPathMap 구성 및 PathBasedRouting 규칙에 대한 개요를 제공합니다.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 04/23/2018
ms.date: 04/17/2019
ms.author: v-junlch
ms.openlocfilehash: ee0267146140d095487b293331a7de493ba151c6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61361964"
---
# <a name="azure-application-gateway-url-path-based-routing-overview"></a>Azure Application Gateway URL 경로 기반 라우팅 개요

URL 경로 기반 라우팅을 사용하여 요청의 URL 경로에 따라 트래픽을 백 엔드 서버 풀로 라우팅할 수 있습니다. 

시나리오 중 하나는 여러 콘텐츠 형식에 대한 요청을 서로 다른 백 엔드 서버 풀로 라우팅하는 것입니다.

다음 예제에서는 Application Gateway가 세 개의 백 엔드 서버 풀(예: VideoServerPool, ImageServerPool 및 DefaultServerPool)에서 contoso.com에 대한 트래픽을 제공합니다.

![imageURLroute](./media/url-route-overview/figure1.png)

에 대 한 요청 <http://contoso.com/video/*> videoserverpool로 라우팅됩니다 및 <http://contoso.com/images/*> ImageServerPool로 라우팅됩니다. 경로 패턴과 일치하는 항목이 없는 경우 DefaultServerPool이 선택됩니다.

> [!IMPORTANT]
> 규칙은 포털에 나열된 순서대로 처리됩니다. 기본 수신기를 구성하기 전에 먼저 다중 사이트 수신기를 구성하는 것이 좋습니다.  그러면 트래픽이 올바른 백 엔드로 라우팅됩니다. 기본 수신기가 먼저 나열되고 들어오는 요청과 일치하면 해당 수신기에서 처리합니다.

## <a name="urlpathmap-configuration-element"></a>UrlPathMap 구성 요소

urlPathMap 요소는 백 엔드 서버 풀 매핑에 대한 경로 패턴을 지정하는 데 사용됩니다. 다음 코드 예제는 템플릿 파일의 urlPathMap 요소 조각입니다.

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
> PathPattern: 이 설정은 일치 하는 경로 패턴의 목록입니다. 각각은 /로 시작해야 하고 "*"는 "/" 다음의 끝에 올 수 있습니다. 경로 검사기에 제공하는 문자열은 ? 또는 #으로 시작하는 텍스트는 포함하지 않습니다. 이러한 문자는 허용되지 않습니다.

자세한 내용은 [URL 기반 라우팅을 사용하는 Resource Manager 템플릿](https://azure.microsoft.com/documentation/templates/201-application-gateway-url-path-based-routing)을 참조하세요.

## <a name="pathbasedrouting-rule"></a>PathBasedRouting 규칙

PathBasedRouting 형식의 RequestRoutingRule은 수신기를 urlPathMap에 바인딩하는 데 사용됩니다. 이 수신기에 대해 수신되는 모든 요청은 urlPathMap에 지정된 정책에 따라 라우팅됩니다.
PathBasedRouting 규칙 조각:

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

## <a name="next-steps"></a>다음 단계

URL 기반 콘텐츠 라우팅을 알아본 후에는 [URL 기반 라우팅을 사용하여 애플리케이션 게이트웨이 만들기](tutorial-url-route-powershell.md)로 이동하여 URL 라우팅 규칙을 사용하여 애플리케이션 게이트웨이를 만듭니다.

<!-- Update_Description: update metedata properties -->