---
title: Azure Event Hubs 방화벽 규칙 | Microsoft Docs
description: 특정 IP 주소에서 Azure Event Hubs로 연결을 차단하도록 방화벽 규칙을 사용합니다.
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: fbf3e67cdde43dbe3d5e02cd4b044d5473f409ac
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185131"
---
# <a name="allow-access-to-azure-event-hubs-namespaces-from-specific-ip-addresses-or-ranges"></a>특정 IP 주소 또는 범위에서 Azure Event Hubs 네임 스페이스에 대 한 액세스 허용
기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 Event Hubs 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트에서만 Azure Event Hub에 액세스할 수 있는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생하는 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route][express-route]와 함께 Event Hubs를 사용하는 경우 온-프레미스 인프라 IP 주소에서 오는 트래픽만 허용하도록 **방화벽 규칙**을 만들 수 있습니다. 

>[!IMPORTANT]
> Event Hubs 네임 스페이스에 대 한 방화벽 규칙을 켜면 요청이 허용 되는 공용 IP 주소에서 작동 하는 서비스에서 시작 되는 경우를 제외 하 고 기본적으로 들어오는 요청이 차단 됩니다. 차단되는 요청에는 다른 Azure 서비스, Azure Portal, 로깅 및 메트릭 서비스 등이 포함됩니다. 
>
> IP 필터링을 사용 하는 경우 Event Hubs 리소스에 액세스할 수 없는 일부 서비스는 다음과 같습니다. 목록은 완전 **하지 않습니다** .
>
> - Azure Stream Analytics
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
> - Azure Event Grid
> - Azure Monitor (진단 설정)
>
> 단, IP 필터링을 사용 하는 경우에도 신뢰할 수 있는 특정 서비스의 Event Hubs 리소스에 대 한 액세스를 허용할 수 있습니다. 신뢰할 수 있는 서비스 목록은 [신뢰할 수 있는 Microsoft 서비스](#trusted-microsoft-services)를 참조 하세요.

## <a name="ip-firewall-rules"></a>IP 방화벽 규칙
IP 방화벽 규칙은 Event Hubs 네임스페이스 수준에 적용됩니다. 따라서 규칙은 지원 되는 모든 프로토콜을 사용 하는 클라이언트의 모든 연결에 적용 됩니다. Event Hubs 네임 스페이스의 허용 되는 IP 규칙과 일치 하지 않는 IP 주소에서의 모든 연결 시도는 권한 없는 것으로 거부 됩니다. 응답은 IP 규칙을 언급 하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

## <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal을 사용하여 Event Hubs 네임스페이스에 대한 IP 방화벽 규칙을 만드는 방법을 보여줍니다. 

1. **Azure Portal**에서 [Event Hubs 네임스페이스](https://portal.azure.com)로 이동합니다.
4. 왼쪽 메뉴의 **설정** 에서 **네트워킹** 을 선택 합니다. **표준** 또는 **전용** 네임 스페이스에 대 한 **네트워킹** 탭만 표시 됩니다. 
    > [!NOTE]
    > 기본적으로 다음 이미지에 표시 된 것 처럼 **선택한 네트워크** 옵션이 선택 됩니다. IP 방화벽 규칙을 지정 하지 않거나이 페이지에서 가상 네트워크를 추가 하는 경우 **공용 인터넷** 을 통해 네임 스페이스에 액세스할 수 있습니다 (액세스 키 사용).  

    :::image type="content" source="./media/event-hubs-firewall/selected-networks.png" alt-text="네트워크 탭-선택한 네트워크 옵션" lightbox="./media/event-hubs-firewall/selected-networks.png":::    

    **모든 네트워크** 옵션을 선택 하는 경우 이벤트 허브는 모든 IP 주소에서 연결을 수락 합니다 (액세스 키 사용). 이 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![방화벽 - 모든 네트워크 옵션 선택됨](./media/event-hubs-firewall/firewall-all-networks-selected.png)
1. 특정 IP 주소에 대 한 액세스를 제한 하려면 **선택한 네트워크** 옵션이 선택 되어 있는지 확인 합니다. **방화벽** 섹션에서 다음 단계를 수행합니다.
    1. **클라이언트 IP 주소 추가** 옵션을 선택하여 현재 클라이언트 IP에 네임스페이스에 대한 액세스 권한을 부여합니다. 
    2. **주소 범위**에 CIDR 표기법으로 특정 IPv4 주소 또는 IPv4 주소 범위를 입력합니다. 
3. **신뢰할 수 있는 Microsoft 서비스가 이 방화벽을 바이패스하도록 허용**할지 여부를 지정합니다. 자세한 내용은 [신뢰할 수 있는 Microsoft 서비스](#trusted-microsoft-services) 를 참조 하세요. 

      ![방화벽 - 모든 네트워크 옵션 선택됨](./media/event-hubs-firewall/firewall-selected-networks-trusted-access-disabled.png)
3. 도구 모음에서 **저장**을 선택하여 설정을 저장합니다. 포털 알림에 확인이 표시될 때가지 몇 분 정도 기다립니다.

    > [!NOTE]
    > 특정 가상 네트워크에 대 한 액세스를 제한 하려면 [특정 네트워크에서 액세스 허용](event-hubs-service-endpoints.md)을 참조 하세요.

[!INCLUDE [event-hubs-trusted-services](../../includes/event-hubs-trusted-services.md)]


## <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용

> [!IMPORTANT]
> 방화벽 규칙은 Event Hubs의 **표준** 및 **전용** 계층에서 지원됩니다. 기본 계층에서는 지원되지 않습니다.

다음과 같은 Resource Manager 템플릿을 사용하면 기존 Event Hubs 네임스페이스에 IP 필터 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

- **ipMask**는 단일 IPv4 주소 또는 CIDR 표기법인 IP 주소 블록입니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

> [!NOTE]
> 가능한 거부 규칙은 없지만 Azure Resource Manager 템플릿은 기본 작업이 **"허용"** 으로 설정되며 연결을 제한하지 않습니다.
> Virtual Network 또는 방화벽 규칙을 만들 때 ***"defaultAction"*** 을 변경해야 합니다.
> 
> 원본
> ```json
> "defaultAction": "Allow"
> ```
> to
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "eventhubNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Event Hubs namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('eventhubNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('eventhubNamespaceName')]",
        "type": "Microsoft.EventHub/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Standard",
          "tier": "Standard"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.EventHub/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.EventHub/namespaces/', parameters('eventhubNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 액세스를 Azure 가상 네트워크로 제한하려면 다음 링크를 참조하세요.

- [Event Hubs용 Virtual Network Service 엔드포인트][lnk-vnet]

<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: event-hubs-service-endpoints.md
