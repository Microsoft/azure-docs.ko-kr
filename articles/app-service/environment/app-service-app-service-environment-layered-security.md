---
title: App Service Environment로 계층화된 보안 아키텍처 - Azure
description: App Service Environment로 계층화된 보안 아키텍처 구현
services: app-service
documentationcenter: ''
author: stefsch
manager: erikre
editor: ''
ms.assetid: 73ce0213-bd3e-4876-b1ed-5ecad4ad5601
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: 5e25de1ad2042ac978c3698165b9d9baba20e816
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130690"
---
# <a name="implementing-a-layered-security-architecture-with-app-service-environments"></a>App Service Environment로로 계층화된 보안 아키텍처 구현
## <a name="overview"></a>개요
App Service Environment가 가상 네트워크에 배포된 격리된 런타임 환경을 제공하므로 개발자는 실제 애플리케이션 계층 각각에 서로 다른 수준으로 네트워크 액세스를 제공하는 계층화된 보안 아키텍처를 만들 수 있습니다.

일반적으로 일반 인터넷 액세스로부터 API 백 엔드를 숨기거나 API가 업스트림 웹앱에서 호출될 수 있도록 하기 원합니다.  [네트워크 보안 그룹(NSG)][NetworkSecurityGroups]은 App Service 환경을 포함하는 서브넷에서 사용되어 API 애플리케이션에 대한 공용 액세스를 제한할 수 있습니다.

아래 다이어그램은 App Service Environment에 배포된 Web API 기반 앱을 사용한 예제 아키텍처를 보여줍니다.  3개의 별도 App Service Environment에 배포된 3개의 별도 웹앱 인스턴스는 동일한 Web API 앱에 백 엔드 호출을 수행합니다.

![개념적 아키텍처][ConceptualArchitecture] 

녹색 더하기 기호는 "apiase"를 포함하는 서브넷의 네트워크 보안 그룹이 자체에서 호출 뿐만 아니라 업스트림 웹앱에서 인바운드 호출을 허용한다는 사실을 나타냅니다.  그러나 동일한 네트워크 보안 그룹은 인터넷에서 일반 인바운드 트래픽에 대한 액세스를 명시적으로 거부합니다. 

이 문서의 나머지 부분에서는 "apiase"를 포함하는 서브넷에 네트워크 보안 그룹을 구성하는 데 필요한 단계를 안내합니다.

## <a name="determining-the-network-behavior"></a>네트워크 동작 확인
어떤 네트워크 보안 규칙이 필요한지 알기 위해 어떤 네트워크 클라이언트가 API 앱을 포함하는 App Service Environment에 연결할 수 있고 어떤 클라이언트를 차단할지 결정해야 합니다.

[네트워크 보안 그룹(NSG)][NetworkSecurityGroups]이 서브넷에 적용되고 App Service 환경이 서브넷에 배포되기 때문에 NSG에 포함된 규칙은 App Service 환경에서 실행하는 **모든** 앱에 적용됩니다.  네트워크 보안 그룹이 "apiase"를 포함하는 서브넷에 적용되면 이 문서에 대한 샘플 아키텍처를 사용하여 "apiase" App Service Environment에서 실행되는 모든 앱은 동일한 집합의 보안 규칙에 의해 보호됩니다. 

* **업스트림 호출자의 아웃바운드 IP 주소 확인:**  업스트림 호출자의 IP 주소는 무엇인가요?  이러한 주소는 NSG에서 명시적으로 액세스하도록 허용해야 합니다.  App Service Environment 간의 호출이 "Internet" 호출을 고려하기 때문에 각 세 업스트림 App Service Environments에 할당된 아웃 바운드 IP 주소는 "apiase" 서브넷에 대한 NSG에서 액세스하도록 허용해야 합니다.   App Service 환경에서 실행되는 앱에 대한 아웃 바운드 IP 주소를 확인하는 데 대한 자세한 내용은 [네트워크 아키텍처][NetworkArchitecture] 개요 문서를 참조하세요.
* **백 엔드 API 앱 자체를 호출해야 합니까?**  때로는 간과되고 미묘한 점은 백 엔드 애플리케이션이 자신을 호출해야 한다는 시나리오입니다.  또한 App Service Environment에서 백 엔드 API 애플리케이션이 자신을 호출하는 경우 "인터넷" 호출로 처리됩니다.  샘플 아키텍처에서는 "apiase" App Service Environment의 아웃 바운드 IP 주소에서 액세스하도록 허락이 필요합니다.

## <a name="setting-up-the-network-security-group"></a>네트워크 보안 그룹 설치
아웃 바운드 IP 주소 집합을 모두 알고 나면 다음 단계에서 네트워크 보안 그룹을 생성합니다.  클래식 가상 네트워크뿐만 아니라 가상 네트워크를 기반으로 하는 두 Resource Manager에 네트워크 보안 그룹을 만들 수 있습니다.  아래 예제에서는 Powershell을 사용하여 기존 가상 네트워크에 NSG를 만들고 구성하는 방법을 보여 줍니다.

샘플 아키텍처의 경우 환경은 미국 중남부에 있으므로 빈 NSG는 해당 지역에서 만들어집니다.

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

먼저 명시적 허용 규칙은App Service 환경에 대한 [인바운드 트래픽][InboundTraffic] 문서에서 설명한 대로 Azure 관리 인프라에 추가됩니다.

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP

다음으로 첫 번째 업스트림 App Service Environment("fe1ase")에서 HTTP 및 HTTPS 호출을 허용하도록 두 규칙을 추가합니다.

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

두 번째 및 세 번째 업스트림 App Service Environment("fe2ase" 및 "fe3ase")를 다듬고 반복합니다.

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

마지막으로 백 엔드 API의 App Service Environment의 아웃 바운드 IP 주소에 대한 액세스를 부여하므로 자신에 다시 호출할 수 있습니다.

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

모든 NSG에 기본적으로 인터넷에서 인바운드 액세스를 차단하는 기본 규칙의 집합이 있기 때문에 다른 네트워크 보안 규칙은 필요 없습니다.

네트워크 보안 그룹에 있는 규칙의 전체 목록은 아래에 표시됩니다.  강조 표시된 마지막 규칙이 명시적으로 액세스가 부여된 호출자를 제외한 모든 호출자의 인바운드 액세스를 어떻게 차단하는지 확인합니다.

![NSG 구성][NSGConfiguration] 

마지막 단계는 "apiase" App Service Environment에 포함된 서브넷에 NSG를 적용합니다.

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

서브넷에 적용된 NSG를 사용하여 3개의 업스트림 App Service Environment 및 API 백 엔드를 포함하는 App Service Environment는 "apiase" 환경으로 호출하도록 합니다.

## <a name="additional-links-and-information"></a>추가 링크 및 정보
[네트워크 보안 그룹](../../virtual-network/security-overview.md)에 대한 정보.

[아웃바운드 IP 주소][NetworkArchitecture] 및 App Service 환경 이해.

App Service 환경에서 사용되는 [네트워크 포트][InboundTraffic].

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  app-service-app-service-environment-network-architecture-overview.md
[InboundTraffic]:  app-service-app-service-environment-control-inbound-traffic.md

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png
