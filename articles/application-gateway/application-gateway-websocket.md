---
title: Azure 애플리케이션 게이트웨이에서 WebSocket 지원
description: Application Gateway는 모든 게이트웨이 크기에 WebSocket에 대한 네이티브 지원을 제공합니다. 사용자가 구성할 수 있는 설정이 없습니다.
author: vhorne
ms.author: amsriva
ms.service: application-gateway
services: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.openlocfilehash: baa02c4d946a121f26f421af99835ae2bea18847
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130337"
---
# <a name="overview-of-websocket-support-in-application-gateway"></a>Application Gateway의 WebSocket 지원 개요

Application Gateway는 모든 게이트웨이 크기에 WebSocket에 대한 네이티브 지원을 제공합니다. WebSocket 지원을 선택적으로 사용하거나 사용하지 않도록 설정하는 사용자 구성 가능 설정은 없습니다. 

[RFC6455](https://tools.ietf.org/html/rfc6455)에서 표준화된 WebSocket 프로토콜을 사용하면 장기 실행 TCP 연결을 통해 서버와 클라이언트 간의 전이중 통신을 수행할 수 있습니다. 이 기능을 사용하면 웹 서버와 클라이언트 간의 대화형 통신이 가능하며, HTTP 기반 구현에서 필요에 따라 폴링하지 않고도 양방향 통신을 수행할 수 있습니다. WebSocket은 HTTP와 달리 오버헤드가 낮고 여러 요청/응답에 동일한 TCP 연결을 다시 사용하므로 리소스를 더 효율적으로 활용할 수 있습니다. WebSocket 프로토콜은 기존의 HTTP 포트 80 및 443을 통해 작동하도록 디자인되었습니다.

80 또는 443 포트에서 표준 HTTP 수신기를 계속 사용하여 WebSocket 트래픽을 받을 수 있습니다. WebSocket 트래픽은 Application Gateway 규칙에 지정된 대로 적절한 백 엔드 풀을 사용하여 WebSocket 활성화된 백 엔드 서버로 지정됩니다. 백 엔드 서버는 [상태 프로브 개요](application-gateway-probe-overview.md) 섹션에서 설명한 대로 애플리케이션 게이트웨이 프로브에 응답해야 합니다. 애플리케이션 게이트웨이 상태 프로브는 HTTP/HTTPS 전용입니다. 각 백 엔드 서버는 WebSocket 트래픽을 서버로 라우팅하기 위해 애플리케이션 게이트웨이에 대한 HTTP 프로브에 응답해야 합니다.

채팅, 대시보드, 게임 앱 등의 신속한 실시간 통신을 활용 하는 앱에서 사용 됩니다.

## <a name="how-does-websocket-work"></a>WebSocket 작동 방식

WebSocket 연결을 설정 하기 위해 특정 HTTP 기반 핸드셰이크는 클라이언트와 서버 간에 교환 됩니다. 성공 하면 응용 프로그램 계층 프로토콜이 이전에 설정 된 TCP 연결을 사용 하 여 HTTP에서 Websocket으로 "업그레이드" 됩니다. 이 경우 HTTP는 완전히 그림에서 제외 됩니다. WebSocket 연결이 닫힐 때까지 두 엔드포인트에서 WebSocket 프로토콜을 사용 하 여 데이터를 보내거나 받을 수 있습니다. 

![서버당](./media/application-gateway-websocket/websocket.png)

### <a name="listener-configuration-element"></a>수신기 구성 요소

기존 HTTP 수신기를 사용하여 WebSocket을 지원할 수 있습니다. 다음은 샘플 템플릿 파일에 있는 httpListeners 요소의 코드 조각입니다. WebSocket을 지원하고 WebSocket 트래픽을 보호하기 위해 HTTP 및 HTTPS 수신기가 모두 필요합니다. 마찬가지로 포털 또는 Azure PowerShell를 사용 하 여 WebSocket 트래픽을 지원 하기 위해 포트 80/443의 수신기를 사용 하 여 응용 프로그램 게이트웨이를 만들 수 있습니다.

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

BackendAddressPool은 WebSocket 지원 서버가 포함된 백 엔드 풀을 정의하는 데 사용됩니다. backendHttpSetting은 80 및 443 백 엔드 포트로 정의됩니다. HTTP 설정의 요청 시간 제한 값은 WebSocket 세션에도 적용 됩니다. 적절 한 수신기를 해당 백 엔드 주소 풀에 연결 하는 데 사용 되는 라우팅 규칙에 필요한 변경 내용이 없습니다. 

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