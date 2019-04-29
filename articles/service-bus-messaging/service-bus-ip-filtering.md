---
title: Azure Service Bus 방화벽 규칙 | Microsoft Docs
description: 특정 IP 주소에서 Azure Service Bus로 연결을 허용하도록 방화벽 규칙을 사용하는 방법입니다.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 04/23/2019
ms.author: aschhab
ms.openlocfilehash: 540435e3e018ae77477030ae8b9f727d71782121
ms.sourcegitcommit: a95dcd3363d451bfbfea7ec1de6813cad86a36bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62736814"
---
# <a name="use-firewall-rules"></a>방화벽 규칙 사용

잘 알려진 특정 사이트에서 Azure Service Bus만이 액세스 가능한 시나리오의 경우 방화벽 규칙을 사용하면 특정 IPv4 주소에서 시작된 트래픽을 허용하는 규칙을 구성할 수 있습니다. 예를 들어 이 주소는 회사 NAT 게이트웨이의 주소일 것입니다.

## <a name="when-to-use"></a>사용하는 경우

지정된 범위의 IP 주소에서 오는 트래픽만 수신하고 이외의 트래픽은 거부해야 하는 Service Bus를 설정하려는 경우 *방화벽*을 활용하여 다른 IP 주소의 Service Bus 엔드포인트를 차단할 수 있습니다. 예를 들어 [Azure ExpressRoute][express-route]에서 Service Bus를 사용하여 온-프레미스 인프라에 개인 연결을 만듭니다. 

## <a name="how-filter-rules-are-applied"></a>필터 규칙이 적용되는 방식

IP 필터 규칙은 Service Bus 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다.

Service Bus 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다.

## <a name="default-setting"></a>기본 설정

기본적으로 Service Bus에 대한 포털의 **IP 필터** 그리드는 비어있습니다. 이러한 기본 설정은 네임스페이스가 모든 IP 주소의 연결을 수락한다는 것을 의미합니다. 이러한 기본 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다.

## <a name="ip-filter-rule-evaluation"></a>IP 필터 규칙 평가

IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

>[!WARNING]
> 방화벽 규칙을 구현하면 다른 Azure 서비스가 Service Bus와 상호 작용하는 것을 방지할 수 있습니다.
>
> 신뢰할 수 있는 Microsoft 서비스는 IP 필터링(방화벽 규칙)이 구현되는 시점에 지원되지 않으며 곧 제공될 예정입니다.
>
> IP 필터링이 작동하지 않는 일반적인 Azure 시나리오(목록은 전체 목록이 **아님**) -
> - Azure Monitor
> - Azure Stream Analytics
> - Azure Event Grid와 통합
> - Azure IoT Hub 경로
> - Azure IoT Device Explorer
> - Azure Data Explorer
>
> 아래 Microsoft 서비스는 가상 네트워크에 있어야 합니다.
> - Azure App Service
> - Azure 기능

### <a name="creating-a-virtual-network-and-firewall-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 가상 네트워크 및 방화벽 규칙 만들기

> [!IMPORTANT]
> 방화벽 및 가상 네트워크에만 지원 합니다 **premium** 계층 Service Bus의 합니다.

다음과 같은 Resource Manager 템플릿을사용 하면 기존 Service Bus 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

- **ipMask**는 단일 IPv4 주소 또는 CIDR 표기법인 IP 주소 블록입니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

> [!NOTE]
> 가능한 거부 규칙은 없지만 Azure Resource Manager 템플릿은 기본 작업이 **"허용"** 으로 설정되며 연결을 제한하지 않습니다.
> Virtual Network 또는 방화벽 규칙을 만들 때 ***"defaultAction"*** 을 변경해야 합니다.
> 
> from
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
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
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
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

Service Bus에 대한 액세스를 Azure 가상 네트워크로 제한하려면 다음 링크를 참조하세요.

- [Service Bus의 Virtual Network 서비스 엔드포인트][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
