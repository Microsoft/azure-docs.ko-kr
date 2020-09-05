---
title: Azure App Service 액세스 제한
description: 액세스 제한을 지정 하 여 Azure App Service에서 앱을 보호 하는 방법을 알아봅니다.
author: ccompy
ms.assetid: 3be1f4bd-8a81-4565-8a56-528c037b24bd
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: ce5882f2621dc5b8c48bcf5be6d4ea3a2f723bfe
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962964"
---
# <a name="azure-app-service-access-restrictions"></a>Azure App Service 액세스 제한

액세스 제한을 통해 앱에 대 한 네트워크 액세스를 제어 하는 우선 순위가 지정 된 허용/거부 목록을 정의할 수 있습니다. 이 목록에는 IP 주소 또는 Azure Virtual Network 서브넷이 포함 될 수 있습니다. 하나 이상의 항목이 있는 경우 목록 끝에 있는 암시적 "거부 모두"가 있습니다.

액세스 제한 기능은를 비롯 한 모든 App Service 호스팅된 작업 로드와 함께 작동 합니다. 웹 앱, API 앱, Linux 앱, Linux 컨테이너 앱 및 함수

앱에 대 한 요청이 수행 되 면 액세스 제한 목록의 IP 주소 규칙에 대해 보낸 사람 주소가 평가 됩니다. 보낸 사람 주소가 Microsoft 웹에 대 한 서비스 끝점으로 구성 된 서브넷에 있는 경우 원본 서브넷은 액세스 제한 목록의 가상 네트워크 규칙과 비교 됩니다. 주소가 목록의 규칙을 기준으로 허용되지 않을 경우 서비스는 [HTTP 403](https://en.wikipedia.org/wiki/HTTP_403) 상태 코드로 답변합니다.

액세스 제한 기능은 코드가 실행 되는 작업자 호스트의 업스트림 인 App Service 프런트 엔드 역할에서 구현 됩니다. 따라서 액세스 제한은 사실상 네트워크 Acl입니다.

Azure Virtual Network (VNet)에서 웹 앱에 대 한 액세스를 제한 하는 기능을 [서비스 끝점][serviceendpoints]이라고 합니다. 서비스 끝점을 사용 하면 선택한 서브넷에서 다중 테 넌 트 서비스에 대 한 액세스를 제한할 수 있습니다. 사용 하도록 설정 된 서비스 뿐만 아니라 네트워킹 쪽에서 사용 하도록 설정 해야 합니다. App Service Environment에서 호스트 되는 앱에 대 한 트래픽을 제한 하는 것은 작동 하지 않습니다. App Service Environment에 있는 경우 IP 주소 규칙을 사용 하 여 앱에 대 한 액세스를 제어할 수 있습니다.

![액세스 제한 흐름](media/app-service-ip-restrictions/access-restrictions-flow.png)

## <a name="adding-and-editing-access-restriction-rules-in-the-portal"></a>포털에서 액세스 제한 규칙 추가 및 편집 ##

앱에 액세스 제한 규칙을 추가 하려면 메뉴를 사용 하 여 **네트워크** > **액세스 제한을** 열고 **액세스 제한 구성** 을 클릭 합니다.

![App Service 네트워킹 옵션](media/app-service-ip-restrictions/access-restrictions.png)  

액세스 제한 UI에서 앱에 대해 정의 된 액세스 제한 규칙 목록을 검토할 수 있습니다.

![액세스 제한 목록](media/app-service-ip-restrictions/access-restrictions-browse.png)

이 목록에는 앱에 있는 현재 제한이 모두 표시 됩니다. 앱에 VNet 제한이 있는 경우 Microsoft 웹에 대해 서비스 끝점을 사용 하도록 설정 하면 테이블이 표시 됩니다. 앱에 대해 정의 된 제한이 없으면 어디에서 나 앱에 액세스할 수 있습니다.  

## <a name="adding-ip-address-rules"></a>IP 주소 규칙 추가

**[+] 규칙 추가** 를 클릭 하 여 새 액세스 제한 규칙을 추가할 수 있습니다. 규칙을 추가하면 즉시 적용됩니다. 규칙은 가장 작은 번호부터 우선적으로 적용됩니다. 단일 규칙을 추가해도 암시적 모두 거부가 적용됩니다.

규칙을 만들 때 허용/거부와 규칙의 유형을 선택 해야 합니다. 또한 우선 순위 값과 액세스를 제한 하는 항목을 제공 해야 합니다.  필요에 따라 규칙에 이름 및 설명을 추가할 수 있습니다.  

![IP 액세스 제한 규칙 추가](media/app-service-ip-restrictions/access-restrictions-ip-add.png)

IP 주소 기반 규칙을 설정 하려면 IPv4 또는 IPv6 유형을 선택 합니다. IP 주소 표기법은 IPv4 및 IPv6 주소 둘 다에 대해 CIDR 표기법으로 지정해야 합니다. 정확한 주소를 지정하려면 1.2.3.4/32와 같이 처음 4개의 8진수로 IP 주소를 나타내고 /32로 마스크를 나타낼 수 있습니다. 모든 주소에 대한 IPv4 CIDR 표기법은 0.0.0.0/0입니다. CIDR 표기법에 대한 자세한 내용은 [Classless Inter-domain Routing](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)을 참조하세요. 

## <a name="service-endpoints"></a>서비스 엔드포인트

서비스 끝점을 사용 하면 선택한 Azure virtual network 서브넷에 대 한 액세스를 제한할 수 있습니다. 특정 서브넷에 대 한 액세스를 제한 하려면 Virtual Network 유형의 제한 규칙을 만듭니다. 액세스를 허용 하거나 거부할 구독, VNet 및 서브넷을 선택할 수 있습니다. 사용자가 선택한 서브넷에 대해 서비스 끝점을 아직 사용 하도록 설정 하지 않은 경우에는이 작업을 수행 하지 않도록 요청 하는 상자를 선택 하지 않으면 자동으로 사용 하도록 설정 됩니다. 서브넷에서 서비스 끝점을 사용 하도록 설정할 수 있는 권한이 있는 경우에는 응용 프로그램에서이 기능을 사용 하도록 설정 하 고 서브넷은 사용 하지 않을 수 있습니다. 다른 사용자가 서브넷에서 서비스 끝점을 사용 하도록 설정 해야 하는 경우 확인란을 선택 하 고 나중에 서브넷에서 사용 하도록 설정 되는 서비스 끝점에 대해 앱을 구성할 수 있습니다. 

![VNet 액세스 제한 규칙 추가](media/app-service-ip-restrictions/access-restrictions-vnet-add.png)

서비스 끝점은 App Service Environment에서 실행 되는 앱에 대 한 액세스를 제한 하는 데 사용할 수 없습니다. 앱이 App Service Environment에 있는 경우 IP 액세스 규칙을 사용 하 여 앱에 대 한 액세스를 제어할 수 있습니다. 

서비스 끝점을 사용 하면 응용 프로그램 게이트웨이 또는 기타 WAF 장치를 사용 하 여 앱을 구성할 수 있습니다. 또한 보안 백 엔드를 사용 하 여 다중 계층 응용 프로그램을 구성할 수 있습니다. 일부 가능성에 대 한 자세한 내용은 [네트워킹 기능 및 App Service](networking-features.md) 와 [서비스 끝점과의 통합 Application Gateway](networking/app-gateway-with-service-endpoints.md)을 참조 하세요.

> [!NOTE]
> 현재 서비스 끝점은 IP SSL VIP (가상 IP)를 사용 하는 웹 앱에 대해 지원 되지 않습니다. 
>

## <a name="managing-access-restriction-rules"></a>액세스 제한 규칙 관리

행을 클릭 하 여 기존 액세스 제한 규칙을 편집할 수 있습니다. 우선 순위 변경을 비롯한 편집 내용은 즉시 적용됩니다.

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/access-restrictions-ip-edit.png)

규칙을 편집할 때 IP 주소 규칙과 Virtual Network 규칙 간의 유형을 변경할 수 없습니다. 

![액세스 제한 규칙 편집](media/app-service-ip-restrictions/access-restrictions-vnet-edit.png)

규칙을 삭제 하려면 **규칙에서 ...를 클릭** 한 다음 **제거**를 클릭 합니다.

![액세스 제한 규칙 삭제](media/app-service-ip-restrictions/access-restrictions-delete.png)

## <a name="blocking-a-single-ip-address"></a>단일 IP 주소 차단 ##

첫 번째 IP 제한 규칙을 추가 하는 경우 서비스는 우선 순위가 2147483647 인 명시적 **거부 모든** 규칙을 추가 합니다. 실제로 명시적 **거부** 규칙은 마지막 규칙 실행 되 고 **허용** 규칙을 사용 하 여 명시적으로 허용 되지 않는 모든 IP 주소에 대 한 액세스를 차단 합니다.

사용자가 단일 IP 주소 또는 IP 주소 블록을 명시적으로 차단 하지만 다른 모든 액세스를 허용 하려는 시나리오의 경우 명시적 **허용 모든** 규칙을 추가 해야 합니다.

![단일 ip 주소 차단](media/app-service-ip-restrictions/block-single-address.png)

## <a name="scm-site"></a>SCM 사이트 

앱에 대 한 액세스를 제어 하는 것 외에도 앱에서 사용 하는 scm 사이트에 대 한 액세스를 제한할 수 있습니다. Scm 사이트는 웹 배포 끝점과 Kudu 콘솔 이기도 합니다. 앱에서 scm 사이트에 대 한 액세스 제한을 개별적으로 할당 하거나 앱과 scm 사이트 모두에 대해 동일한 집합을 사용할 수 있습니다. 확인란을 선택 하 여 앱과 동일한 제한 사항을 적용 하는 경우 모든 항목은 숨겨집니다. 이 확인란의 선택을 취소 하면 scm 사이트에서 이전에 수행한 모든 설정이 적용 됩니다. 

![액세스 제한 목록](media/app-service-ip-restrictions/access-restrictions-scm-browse.png)

## <a name="programmatic-manipulation-of-access-restriction-rules"></a>액세스 제한 규칙의 프로그래밍 방식 조작 ##

[Azure CLI](/cli/azure/webapp/config/access-restriction?view=azure-cli-latest) 및 [Azure PowerShell](/powershell/module/Az.Websites/Add-AzWebAppAccessRestrictionRule?view=azps-3.1.0) 는 액세스 제한 편집을 지원 합니다. Azure CLI를 사용 하 여 액세스 제한을 추가 하는 예제:

```azurecli-interactive
az webapp config access-restriction add --resource-group ResourceGroup --name AppName \
    --rule-name 'IP example rule' --action Allow --ip-address 122.133.144.0/24 --priority 100
```
Azure PowerShell를 사용 하 여 액세스 제한을 추가 하는 예제:

```azurepowershell-interactive
Add-AzWebAppAccessRestrictionRule -ResourceGroupName "ResourceGroup" -WebAppName "AppName"
    -Name "Ip example rule" -Priority 100 -Action Allow -IpAddress 122.133.144.0/24
```

리소스 관리자 또는 Azure Resource Manager 템플릿을 사용 하 여 앱 구성에서 [Azure REST API](/rest/api/azure/) PUT 작업을 사용 하 여 수동으로 값을 설정할 수도 있습니다. 예를 들어, resources.azure.com을 사용하고, ipSecurityRestrictions 블록을 편집하여 필요한 JSON을 추가할 수 있습니다.

Resource Manager에서 이 정보는 다음 위치에 제공됩니다.

management.azure.com/subscriptions/**구독 ID**/resourceGroups/**리소스 그룹**/providers/Microsoft.Web/sites/**웹앱 이름**/config/web?api-version=2018-02-01

앞의 예제에 대한 JSON 구문은 다음과 같습니다.
```json
{
  "properties": {
    "ipSecurityRestrictions": [
      {
        "ipAddress": "122.133.144.0/24",
        "action": "Allow",
        "priority": 100,
        "name": "IP example rule"
      }
    ]
  }
}
```

## <a name="azure-functions-access-restrictions"></a>Azure Functions 액세스 제한

App Service 계획과 동일한 기능을 사용 하는 함수 앱에 대 한 액세스 제한도 사용할 수 있습니다. 액세스 제한을 사용 하도록 설정 하면 허용 되지 않는 Ip에 대해 포털 코드 편집기를 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계
[Azure Functions에 대 한 액세스 제한](../azure-functions/functions-networking-options.md#inbound-ip-restrictions)

[서비스 끝점과 Application Gateway 통합](networking/app-gateway-with-service-endpoints.md)

<!--Links-->
[serviceendpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md