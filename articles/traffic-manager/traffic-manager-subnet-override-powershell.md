---
title: Azure PowerShell를 사용 하 여 Azure Traffic Manager 서브넷 재정의 | Microsoft Docs
description: 이 문서는 Azure를 사용 하 여 엔드포인트 매핑에 미리 정의 된 IP 범위를 통해 최종 사용자 IP 주소를 기반으로 하는 엔드포인트으로 트래픽을 전달 하기 위해 Traffic Manager 서브넷 재정의를 Traffic Manager 사용 하는 방법을 이해 하는 데 도움이 됩니다. 슬래시.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 323093ec78a9486d19496b0ee90e37cb42eea341
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938415"
---
# <a name="traffic-manager-subnet-override-using-azure-powershell"></a>Azure Powershell을 사용 하 여 서브넷 재정의 Traffic Manager

Traffic Manager 서브넷 재정의를 사용 하 여 프로필의 라우팅 방법을 변경할 수 있습니다.  재정의를 추가 하면 최종 사용자의 IP 주소를 기반으로 하는 트래픽을 엔드포인트 매핑에 미리 정의 된 IP 범위로 보냅니다. 

## <a name="how-subnet-override-works"></a>서브넷 재정의 작동 방법

트래픽 관리자 프로필에 서브넷 재정의를 추가 하면 Traffic Manager는 먼저 최종 사용자의 IP 주소에 대 한 서브넷 재정의가 있는지 확인 합니다. 사용자의 DNS 쿼리가 있는 경우 해당 엔드포인트으로 이동 됩니다.  매핑이 없으면 Traffic Manager 프로필의 원래 라우팅 메서드로 대체 됩니다. 

IP 주소 범위는 CIDR 범위 (예: 1.2.3.0/24) 또는 주소 범위 (예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 각 엔드포인트과 연결 된 IP 범위는 해당 엔드포인트에 대해 고유 해야 합니다. 서로 다른 엔드포인트 간에 IP 범위를 겹치면 Traffic Manager에 의해 프로필이 거부 됩니다.

서브넷 재정의를 지 원하는 라우팅 프로필에는 다음 두 가지 유형이 있습니다.

* **지리적** -Traffic Manager DNS 쿼리의 IP 주소에 대 한 서브넷 재정의를 찾으면 엔드포인트의 상태가 무엇이 든 엔드포인트으로 쿼리를 라우팅합니다.
* **성능** -Traffic Manager DNS 쿼리의 IP 주소에 대 한 서브넷 재정의를 찾으면 정상 상태인 경우에만 트래픽을 엔드포인트으로 라우팅합니다.  서브넷 재정의 엔드포인트이 정상이 아닌 경우 Traffic Manager는 성능 라우팅 추론으로 대체 됩니다.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager 서브넷 재정의 만들기

Traffic Manager 서브넷 재정의를 만들려면 Azure PowerShell를 사용 하 여 재정의를 위한 서브넷을 Traffic Manager 엔드포인트에 추가할 수 있습니다.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure Cloud Shell](https://shell.azure.com/powershell) 뒤에 오는 명령을 실행하거나 또는 컴퓨터에서 PowerShell을 실행합니다. Azure Cloud Shell은 무료 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 컴퓨터에서 PowerShell을 실행하는 경우 Azure PowerShell 모듈 버전 1.0.0 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 설치된 버전을 확인할 수 있습니다. 설치 또는 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`도 실행하여 Azure에 로그인해야 합니다.


1. **Traffic Manager 엔드포인트을 검색 합니다.**

    서브넷 재정의를 사용 하도록 설정 하려면 재정의를 추가할 엔드포인트을 검색 하 고 [AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)를 사용 하 여 변수에 저장 합니다.

    Name, ProfileName 및 ResourceGroupName를 변경 하는 엔드포인트의 값으로 바꿉니다.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **IP 주소 범위를 엔드포인트에 추가 합니다.**
    
    IP 주소 범위를 엔드포인트에 추가 하려면 [AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) 를 사용 하 여 범위를 추가 합니다.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27
 
    ```
    범위가 추가 되 면 [AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 를 사용 하 여 엔드포인트을 업데이트 합니다.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```
IP 주소 범위 제거는 [AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/remove-aztrafficmanageripaddressrange?view=azps-2.5.0)을 사용 하 여 완료할 수 있습니다.

1.  **Traffic Manager 엔드포인트을 검색 합니다.**

    서브넷 재정의를 사용 하도록 설정 하려면 재정의를 추가할 엔드포인트을 검색 하 고 [AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0)를 사용 하 여 변수에 저장 합니다.

    Name, ProfileName 및 ResourceGroupName를 변경 하는 엔드포인트의 값으로 바꿉니다.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **엔드포인트에서 IP 주소 범위를 제거 합니다.**

    ```powershell
    
    ### Remove a range of IPs ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Remove a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Remove a range of IPs with a subnet ###
    Remove-AzTrafficManagerIpAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ```
     범위가 제거 되 면 [AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) 를 사용 하 여 엔드포인트을 업데이트 합니다.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## <a name="next-steps"></a>다음 단계
Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

[서브넷 트래픽 라우팅 방법](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) 에 대 한 자세한 정보
