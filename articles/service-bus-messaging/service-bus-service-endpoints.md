---
title: Azure Service Bus에 대한 가상 네트워크 서비스 엔드포인트 및 규칙 | Microsoft Docs
description: 가상 네트워크에 Microsoft.ServiceBus 서비스 엔드포인트를 추가합니다.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: aschhab
ms.openlocfilehash: 0801469d586e6f2d6514927cdc7b894900a3aa35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61471964"
---
# <a name="use-virtual-network-service-endpoints-with-azure-service-bus"></a>Azure Service Bus에서 Virtual Network 서비스 엔드포인트 사용

[VNet(Virtual Network) 서비스 엔드포인트][vnet-sep]와 Service Bus를 통합하면 양쪽 끝에서 네트워크 트래픽 경로를 보호하여 가상 네트워크에 바인딩된 가상 머신과 같은 워크로드의 메시징 기능에 대한 액세스를 보호할 수 있습니다.

적어도 하나의 가상 네트워크 서브넷 서비스 엔드포인트에 바인딩되도록 구성하면 해당하는 Service Bus 네임스페이스는 권한이 부여된 가상 네트워크를 제외한 곳의 트래픽을 더 이상 허용하지 않습니다. 가상 네트워크 큐브 뷰에서 Service Bus 네임스페이스를 서비스 엔드포인트에 바인딩하면 가상 네트워크 서브넷에서 메시징 서비스로 격리된 네트워킹 터널을 구성합니다.

메시징 서비스 엔드포인트의 관찰 가능한 네트워크 주소가 공용 IP 범위에 있음에도 서브넷에 바인딩된 워크로드와 해당하는 Service Bus 네임스페이스 간에 격리된 개인 관계가 생성됩니다.

>[!WARNING]
> Virtual Networks 통합을 구현하면 다른 Azure 서비스가 Service Bus와 상호 작용하지 않도록 방지할 수 있습니다.
>
> 신뢰할 수 있는 Microsoft 서비스는 Virtual Networks가 구현되는 시점에 지원되지 않습니다.
>
> Virtual Networks가 작동하지 않는 일반적인 Azure 시나리오(목록은 전체 목록이 **아님**) -
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

> [!IMPORTANT]
> Virtual Network는 [프리미엄 계층](service-bus-premium-messaging.md) Service Bus 네임스페이스에서만 지원됩니다.

## <a name="enable-service-endpoints-with-service-bus"></a>Service Bus에서 서비스 엔드포인트를 사용하도록 설정

Service Bus에서 VNet 서비스 엔드포인트를 사용할 경우의 중요한 고려 사항은 표준 및 프리미엄 계층 Service Bus 네임스페이스를 혼합하는 애플리케이션에서는 이러한 엔드포인트를 사용하지 않아야 한다는 것입니다. 표준 계층은 VNet을 지원하지 않으므로, 엔드포인트는 프리미엄 계층 네임스페이스로만 제한됩니다.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>VNet 통합에서 사용하도록 설정한 고급 보안 시나리오 

엄격하게 분류된 보안이 필요한 솔루션은 일반적으로 해당 구획에 위치한 서비스 간에 통신 경로가 필요합니다. 해당 솔루션에서 가상 네트워크 서브넷은 분류된 서비스 간를 구분합니다.

TCP/IP에서 HTTPS를 수행하는 경로를 비롯한 구획 간의 즉시 IP 경로를 사용하면 네트워크 레이어에서 취약성을 악용할 위험이 있습니다. 메시징 서비스는 완전히 격리된 통신 경로를 제공합니다. 여기서 메시지는 항목을 전환할 때 디스크에도 작성됩니다. 동일한 Service Bus 인스턴스에 바인딩된 두 개의 고유 가상 네트워크에 있는 워크로드는 메시지를 통해 효율적이고 안정적으로 통신할 수 있습니다. 이 동안에 해당 네트워크 격리 경계의 무결성은 지켜집니다.
 
즉, 보안이 중요한 클라우드 솔루션은 Azure에서 제공하는 업계 최고의 안정적이고 확장성 있는 비동기 메시징 기능에 액세스할 뿐만 아니라 메시징을 사용하여 HTTPS 및 기타 TLS 보안 소켓 프로토콜을 비롯한 보안 솔루션 구획(피어 간 통신 모드로 달성 가능한 수준보다 본질적으로 더 안전함) 간에 통신을 만들 수도 있습니다.

## <a name="binding-service-bus-to-virtual-networks"></a>Virtual Networks에 Service Bus 바인딩

*가상 네트워크 규칙*은 Azure Service Bus 서버가 특정 가상 네트워크 서브넷의 연결을 수락할지 여부를 제어하는 방화벽 보안 기능입니다.

Virtual Networks에 Service Bus를 바인딩하는 작업은 2단계 프로세스입니다. 먼저 Virtual Network 서브넷에서 **Virtual Network 서비스 엔드포인트**를 만들고 [서비스 엔드포인트 개요][vnet-sep]에서 설명한 대로 "Microsoft.ServiceBus"에 사용하도록 설정해야 합니다. 서비스 엔드포인트를 추가했다면 여기에 *가상 네트워크 규칙*을 사용하여 Service Bus 네임스페이스를 바인딩합니다.

가상 네트워크 규칙은 가상 네트워크 서브넷을 사용하는 Service Bus 네임스페이스의 연결입니다. 규칙이 있는 한 서브넷에 바인딩된 모든 워크로드는 Service Bus 네임스페이스에 대한 액세스 권한이 부여됩니다. Service Bus 자체는 아웃바운드 연결을 설정하지 않고, 액세스 권한을 가져올 필요도 없습니다. 따라서 이 규칙을 사용하여 서브넷에 대한 액세스 권한이 부여되지 않습니다.

### <a name="creating-a-virtual-network-rule-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 가상 네트워크 규칙 만들기

다음과 같은 Resource Manager 템플릿을사용 하면 기존 Service Bus 네임스페이스에 가상 네트워크 규칙을 추가할 수 있습니다.

템플릿 매개 변수:

* **namespaceName**: Service Bus 네임스페이스.
* **virtualNetworkingSubnetId**: 가상 네트워크 서브넷에 대해 정규화된 Resource Manager 경로입니다(예: 가상 네트워크 기본 서브넷의 경우 `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default`).

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

템플릿:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

템플릿을 배포하려면 [Azure Resource Manager][lnk-deploy]에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

가상 네트워크에 대한 자세한 내용은 다음 링크를 참조하세요.

- [Azure 가상 네트워크 서비스 엔드포인트][vnet-sep]
- [Azure Service Bus IP 필터링][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/resource-group-template-deploy.md
[ip-filtering]: service-bus-ip-filtering.md
