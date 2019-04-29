---
title: Azure Application Gateway에서 여러 사이트 호스팅
description: 이 문서에서는 Azure Application Gateway 다중 사이트 지원에 대한 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
origin.date: 01/17/2019
ms.date: 04/16/2019
ms.author: v-junlch
ms.topic: conceptual
ms.openlocfilehash: 335545f86c9c23feefb6ac21ca9cc5c8fcb5e7fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60715863"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway 다중 사이트 호스팅

다중 사이트 호스팅을 통해 동일한 애플리케이션 게이트웨이 인스턴스에서 둘 이상의 웹 애플리케이션을 구성할 수 있습니다. 이 기능을 사용하면 최대 100개의 웹 사이트를 하나의 애플리케이션 게이트웨이에 추가하여 배포에 대해 보다 효율적인 토폴로지를 구성할 수 있습니다. 각 웹 사이트는 고유한 백 엔드 풀로 이동할 수 있습니다. 다음 예제에서 애플리케이션 게이트웨이는 두 개의 백 엔드 서버 풀(ContosoServerPool 및 FabrikamServerPool)에서 contoso.com 및 fabrikam.com에 대한 트래픽을 처리합니다.

![imageURLroute](./media/multiple-site-overview/multisite.png)

> [!IMPORTANT]
> 규칙은 포털에 나열된 순서대로 처리됩니다. 기본 수신기를 구성하기 전에 먼저 다중 사이트 수신기를 구성하는 것이 좋습니다.  그러면 트래픽이 올바른 백 엔드로 라우팅됩니다. 기본 수신기가 먼저 나열되고 들어오는 요청과 일치하면 해당 수신기에서 처리합니다.

http://contoso.com에 대한 요청은 ContosoServerPool로 라우팅되고, http://fabrikam.com에 대한 요청은 FabrikamServerPool로 라우팅됩니다.

마찬가지로 같은 부모 도메인의 하위 도메인 두 개를 동일한 애플리케이션 게이트웨이 배포에서 호스트할 수 있습니다. 하위 도메인을 사용하는 예제에는 단일 Application Gateway 배포에 호스팅되는 http://blog.contoso.com 및 http://app.contoso.com이 포함됩니다.

## <a name="host-headers-and-server-name-indication-sni"></a>호스트 헤더 및 SNI(서버 이름 표시)

동일한 인프라에서 여러 사이트를 호스트하도록 설정할 수 있는 세 가지 일반적인 메커니즘이 있습니다.

1. 여러 웹 애플리케이션 각각을 고유한 IP 주소에서 호스트합니다.
2. 동일한 IP 주소에서 호스트 이름을 사용하여 여러 웹 애플리케이션을 호스트합니다.
3. 동일한 IP 주소에서 다른 포트를 사용하여 여러 웹 애플리케이션을 호스트합니다.

현재 애플리케이션 게이트웨이는 단일 공용 IP 주소를 사용하여 트래픽을 수신합니다. 따라서 고유한 IP 주소로 여러 애플리케이션을 지원하는 기능은 현재 지원되지 않습니다. Application Gateway는 각각 서로 다른 포트에서 수신하는 여러 애플리케이션 호스트를 지원하지만 이 시나리오에서는 애플리케이션이 비표준 포트에서 트래픽을 허용해야 하므로 이는 바람직한 구성이 아닌 경우가 많습니다. Application Gateway는 HTTP 1.1 호스트 헤더를 기반으로 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트합니다. 애플리케이션 게이트웨이에서 호스트되는 사이트는 SNI(서버 이름 표시) TLS 확장을 통해 SSL 오프로드를 지원할 수도 있습니다. 따라서 이 시나리오는 클라이언트 브라우저 및 백 엔드 웹 팜은 RFC 6066에 정의된 대로 HTTP/1.1 및 TLS 확장을 지원해야 함을 의미합니다.

## <a name="listener-configuration-element"></a>수신기 구성 요소

애플리케이션 게이트웨이에서 적절한 백 엔드 풀로 트래픽을 라우팅하는 데 사용하는 호스트 이름 및 서버 이름 표시 요소를 지원하기 위해 기존 HTTPListener 구성 요소가 향상되었습니다. 다음 코드 예제는 템플릿 파일의 HttpListeners 요소 조각입니다.

```json
"httpListeners": [
    {
        "name": "appGatewayHttpsListener1",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/DefaultFrontendPublicIP"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort443'"
            },
            "Protocol": "Https",
            "SslCertificate": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/sslCertificates/appGatewaySslCert1'"
            },
            "HostName": "contoso.com",
            "RequireServerNameIndication": "true"
        }
    },
    {
        "name": "appGatewayHttpListener2",
        "properties": {
            "FrontendIPConfiguration": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendIPConfigurations/appGatewayFrontendIP'"
            },
            "FrontendPort": {
                "Id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/frontendPorts/appGatewayFrontendPort80'"
            },
            "Protocol": "Http",
            "HostName": "fabrikam.com",
            "RequireServerNameIndication": "false"
        }
    }
],
```

종단 간 템플릿 기반 배포는 [다중 사이트 호스팅을 사용하는 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/201-application-gateway-multihosting)을 방문하세요.

## <a name="routing-rule"></a>라우팅 규칙

라우팅 규칙에 필요한 변경 사항은 없습니다. 적절한 사이트 수신기를 해당 백 엔드 주소 풀에 연결하기 위해 계속 '기본' 라우팅 규칙을 선택해야 합니다.

```json
"requestRoutingRules": [
{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpsListener1')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

},
{
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/httpListeners/appGatewayHttpListener2')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/FabrikamServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/<subid>/resourceGroups/<rgName>/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}
]
```

## <a name="next-steps"></a>다음 단계

다중 사이트 호스팅에 대해 알아본 후에는 [다중 사이트 호스팅을 사용하여 애플리케이션 게이트웨이 만들기](tutorial-multiple-sites-powershell.md)로 이동해 둘 이상의 웹 애플리케이션 지원 기능으로 애플리케이션 게이트웨이를 만듭니다.


<!-- Update_Description: update metedata properties -->