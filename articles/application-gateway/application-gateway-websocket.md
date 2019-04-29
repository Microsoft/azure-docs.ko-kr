---
title: Azure Application Gateway의 WebSocket 지원 | Microsoft Docs
description: 이 페이지에서는 Application Gateway WebSocket 지원에 대한 개요를 제공합니다.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/18/2019
ms.openlocfilehash: 54c34690e678f07d6309a1877b0ca5d0a0b274f5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831256"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Application Gateway의 WebSocket 지원 개요

Application Gateway는 모든 게이트웨이 크기에 WebSocket에 대한 네이티브 지원을 제공합니다. WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정하는 사용자 구성 가능 설정은 없습니다. 

[RFC6455](https://tools.ietf.org/html/rfc6455)에서 표준화된 WebSocket 프로토콜을 사용하면 장기 실행 TCP 연결을 통해 서버와 클라이언트 간의 전이중 통신을 수행할 수 있습니다. 이 기능을 사용하면 웹 서버와 클라이언트 간의 대화형 통신이 가능하며, HTTP 기반 구현에서 필요에 따라 폴링하지 않고도 양방향 통신을 수행할 수 있습니다. WebSocket은 HTTP와 달리 오버헤드가 낮고 여러 요청/응답에 동일한 TCP 연결을 다시 사용하므로 리소스를 더 효율적으로 활용할 수 있습니다. WebSocket 프로토콜은 기존의 HTTP 포트 80 및 443을 통해 작동하도록 디자인되었습니다.

80 또는 443 포트에서 표준 HTTP 수신기를 계속 사용하여 WebSocket 트래픽을 받을 수 있습니다. WebSocket 트래픽은 Application Gateway 규칙에 지정된 대로 적절한 백 엔드 풀을 사용하여 WebSocket 활성화된 백 엔드 서버로 지정됩니다. 백 엔드 서버는 [상태 프로브 개요](application-gateway-probe-overview.md) 섹션에서 설명한 대로 애플리케이션 게이트웨이 프로브에 응답해야 합니다. 애플리케이션 게이트웨이 상태 프로브는 HTTP/HTTPS 전용입니다. 각 백 엔드 서버는 WebSocket 트래픽을 서버로 라우팅하기 위해 애플리케이션 게이트웨이에 대한 HTTP 프로브에 응답해야 합니다.

채팅, 대시보드 및 게임 앱 등 신속 하 고 실시간 통신을 활용 하는 앱에서 사용 됩니다.

## <a name="how-does-websocket-work"></a>WebSocket는 어떻게 작동 하나요

WebSocket 연결을 설정 하려면 특정 HTTP 기반 핸드셰이크를 클라이언트와 서버 간에 교환 됩니다. 성공 하면 응용 프로그램 계층 프로토콜은 "업그레이드" HTTP에서 websocket을 이전에 설정된 된 TCP 연결을 사용 하 여. HTTP는 완전히에 관여;이 발생 하면 데이터를 보낼 수 있습니다 또는 WebSocket 연결이 닫힐 때까지 두 끝점에서 WebSocket 프로토콜을 사용 하 여 수신 합니다. 

![addcert](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>수신기 구성 요소

기존 HTTP 수신기를 사용하여 WebSocket을 지원할 수 있습니다. 다음은 샘플 템플릿 파일에 있는 httpListeners 요소의 코드 조각입니다. WebSocket을 지원하고 WebSocket 트래픽을 보호하기 위해 HTTP 및 HTTPS 수신기가 모두 필요합니다. 수신기를 사용 하 여 응용 프로그램 게이트웨이 만들려면 포털 또는 Azure PowerShell을 사용할 수 있습니다 마찬가지로 포트 80/443 WebSocket 트래픽을 지원 하도록 합니다.

```json
"httpListeners": [
        {
            "name": "appGatewayHttpsListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/DefaultFrontendPublicIP"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort443'"
                },
                "Protocol": "Https",
                "SslCertificate": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/sslCertificates/appGatewaySslCert1'"
                },
            }
        },
        {
            "name": "appGatewayHttpListener",
            "properties": {
                "FrontendIPConfiguration": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendIPConfigurations/appGatewayFrontendIP'"
                },
                "FrontendPort": {
                    "Id": "/subscriptions/{subscriptionId/resourceGroups/{resourceGroupName/providers/Microsoft.Network/applicationGateways/{applicationGatewayName/frontendPorts/appGatewayFrontendPort80'"
                },
                "Protocol": "Http",
            }
        }
    ],
```

## <a name="backendaddresspool-backendhttpsetting-and-routing-rule-configuration"></a>BackendAddressPool, BackendHttpSetting 및 라우팅 규칙 구성

BackendAddressPool은 WebSocket 지원 서버가 포함된 백 엔드 풀을 정의하는 데 사용됩니다. backendHttpSetting은 80 및 443 백 엔드 포트로 정의됩니다. 쿠키 기반 선호도 및 requestTimeouts의 속성은 WebSocket 트래픽과 관련이 없습니다. 라우팅 규칙은 변경할 필요가 없으며, 'Basic'(기본)은 적절한 수신기를 해당 백 엔드 주소 풀에 연결하는 데 사용됩니다. 

```json
"requestRoutingRules": [{
    "name": "<ruleName1>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpsListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }
    }

}, {
    "name": "<ruleName2>",
    "properties": {
        "RuleType": "Basic",
        "httpListener": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/httpListeners/appGatewayHttpListener')]"
        },
        "backendAddressPool": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendAddressPools/ContosoServerPool')]"
        },
        "backendHttpSettings": {
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/applicationGateways/{applicationGatewayName}/backendHttpSettingsCollection/appGatewayBackendHttpSettings')]"
        }

    }
}]
```

## <a name="websocket-enabled-backend"></a>WebSocket 활성화 백 엔드

WebSocket이 작동되려면 사용자의 백 엔드가 구성된 포트(대개 80/443)에서 HTTP/HTTPS 웹 서버를 실행해야 합니다. 이 요구 사항은 WebSocket 프로토콜을 헤더 필드로 업그레이드하여 초기 핸드 셰이크가 HTTP가 되도록 WebSocket 프로토콜에서 요구하기 때문입니다. 다음은 헤더의 예제입니다.

```
    GET /chat HTTP/1.1
    Host: server.example.com
    Upgrade: websocket
    Connection: Upgrade
    Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
    Origin: https://example.com
    Sec-WebSocket-Protocol: chat, superchat
    Sec-WebSocket-Version: 13
```

또 다른 이유는 애플리케이션 게이트웨이 백 엔드 상태 프로브에서 HTTP/HTTPS 프로토콜만 지원하기 때문입니다. 백 엔드 서버에서 HTTP 또는 HTTPS 프로브에 응답하지 않으면 해당 백 엔드 서버가 백 엔드 풀에서 제거됩니다.

## <a name="next-steps"></a>다음 단계

WebSocket 지원에 대해 알아본 후에 [Application Gateway 만들기](quick-create-powershell.md)로 이동하여 WebSocket 활성화 웹 애플리케이션을 시작합니다.