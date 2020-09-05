---
title: Azure Relay 네임 스페이스에 대 한 IP 방화벽 구성
description: 이 문서에서는 특정 IP 주소에서 Azure Relay 네임스페이스로의 연결을 허용하도록 방화벽 규칙을 사용하는 방법을 설명합니다.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e47c5071a5fc7207d4eabc162fcb24ab6ad57d28
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141859"
---
# <a name="configure-ip-firewall-for-an-azure-relay-namespace"></a>Azure Relay 네임스페이스에 대한 IP 방화벽 구성
기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 릴레이 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

이 기능은 잘 알려진 특정 사이트에서만 Azure Relay에 액세스할 수 있는 시나리오에서 유용합니다. 방화벽 규칙을 사용하면 특정 IPv4 주소에서 발생하는 트래픽을 허용하도록 규칙을 구성할 수 있습니다. 예를 들어 [Azure Express Route](../expressroute/expressroute-faqs.md#supported-services)와 함께 릴레이를 사용하는 경우 온-프레미스 인프라 IP 주소에서 오는 트래픽만 허용하도록 **방화벽 규칙**을 만들 수 있습니다. 


> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. 


## <a name="enable-ip-firewall-rules"></a>IP 방화벽 규칙 사용
IP 방화벽 규칙은 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 모든 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

### <a name="use-azure-portal"></a>Azure Portal 사용
이 섹션에서는 Azure Portal을 사용하여 네임스페이스에 대한 IP 방화벽 규칙을 만드는 방법을 보여줍니다. 

1. [Azure Portal](https://portal.azure.com)에서 **릴레이 네임스페이스**로 이동합니다.
2. 왼쪽 메뉴에서 **네트워킹** 옵션을 선택합니다. **액세스 허용** 섹션에서 **모든 네트워크** 옵션을 선택하면 릴레이 네임스페이스는 모든 IP 주소로부터의 연결을 허용합니다. 이 설정은 0.0.0.0/0 IP 주소 범위를 수락하는 규칙과 같습니다. 

    ![방화벽 - 모든 네트워크 옵션 선택됨](./media/ip-firewall/all-networks-selected.png)
1. 특정 네트워크 및 IP 주소에 대한 액세스를 제한하려면 **선택한 네트워크** 옵션을 선택합니다. **방화벽** 섹션에서 다음 단계를 수행합니다.
    1. **클라이언트 IP 주소 추가** 옵션을 선택하여 현재 클라이언트 IP에 네임스페이스에 대한 액세스 권한을 부여합니다. 
    2. **주소 범위**에 CIDR 표기법으로 특정 IPv4 주소 또는 IPv4 주소 범위를 입력합니다. 

        ![방화벽 - 모든 네트워크 옵션 선택됨](./media/ip-firewall/selected-networks-trusted-access-disabled.png)
3. 도구 모음에서 **저장**을 선택하여 설정을 저장합니다. 포털 알림에 확인이 표시될 때가지 몇 분 정도 기다립니다.


### <a name="use-resource-manager-template"></a>Resource Manager 템플릿 사용
다음 Resource Manager 템플릿을 사용하면 기존 릴레이 네임스페이스에 IP 필터 규칙을 추가할 수 있습니다.

템플릿은 단일 IPv4 주소 또는 CIDR 표기법의 IP 주소 블록인 **ipMask** 매개 변수를 사용합니다. 예를 들어 CIDR 표기법으로 70.37.104.0/24는 70.37.104.0부터 70.37.104.255까지의 256개 IPv4 주소를 나타냅니다. 여기서 24는 범위에 대한 중요 접두사 비트의 수를 의미합니다.

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
      "relayNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Relay namespace"
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
      "namespaceNetworkRuleSetName": "[concat(parameters('relayNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('relayNamespaceName')]",
        "type": "Microsoft.Relay/namespaces",
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
        "type": "Microsoft.Relay/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.Relay/namespaces/', parameters('relayNamespaceName'))]"
        ],
        "properties": {
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

템플릿을 배포하려면 [Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)에 대한 지침을 따르세요.



## <a name="next-steps"></a>다음 단계
다른 네트워크 보안 관련 기능에 대한 자세한 내용은 [네트워크 보안](network-security.md)을 참조하세요.


<!-- Links -->

[express-route]:  ../expressroute/expressroute-faqs.md#supported-services
