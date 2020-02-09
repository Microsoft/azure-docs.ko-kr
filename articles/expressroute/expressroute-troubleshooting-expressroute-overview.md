---
title: 'Azure Express 경로: 연결 확인-문제 해결 가이드'
description: ExpressRoute 회로에 대한 종단 간 연결의 유효성 검사 및 문제 해결에 대한 지침을 제공합니다.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 10/31/2019
ms.author: rambala
ms.custom: seodec18
ms.openlocfilehash: 69c32370b1f7f7cabb642ab140d69dcec57fb4aa
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551984"
---
# <a name="verifying-expressroute-connectivity"></a>ExpressRoute 연결 확인
이 문서는 ExpressRoute 연결 문제를 확인하고 해결하는 데 도움을 줍니다. Express 경로는 연결 공급자가 일반적으로 지 원하는 개인 연결을 통해 온-프레미스 네트워크를 Microsoft 클라우드로 확장 합니다. Express 경로 연결은 일반적으로 다음과 같은 세 가지 고유한 네트워크 영역을 포함 합니다.

-   고객 네트워크
-   공급자 네트워크
-   Microsoft 데이터 센터

> [!NOTE]
> Express 경로에서 10/100 제공 하는 Express 경로 직접 연결 모델에서 고객은 MSEE (Microsoft Enterprise Edge) 라우터의 포트에 직접 연결할 수 있습니다. 따라서 직접 연결 모델에는 고객 및 Microsoft 네트워크 영역만 있습니다.
>


이 문서의 목적은 연결 문제가 있는지 여부와 위치를 확인 하는 데 도움을 주는 것입니다. 따라서 적절 한 팀에서 지원을 검색 하 여 문제를 해결할 수 있습니다. 문제를 해결 하기 위해 Microsoft 지원이 필요한 경우 [Microsoft 지원][Support]를 사용 하 여 지원 티켓을 엽니다.

> [!IMPORTANT]
> 이 문서는 간단한 문제를 진단하고 수정하는 데 사용됩니다. 이 문서는 Microsoft 기술 지원 서비스를 대체하지 않습니다. 제공 된 지침을 사용 하 여 문제를 해결할 수 없는 경우 [Microsoft 지원][Support] 를 사용 하 여 지원 티켓을 엽니다.
>
>

## <a name="overview"></a>개요
다음 다이어그램에서는 ExpressRoute를 통한 고객 네트워크와 Microsoft 네트워크 간의 논리적 연결을 보여 줍니다.
[![1]][1]

위의 다이어그램에서 숫자는 주요 네트워크 지점을 나타냅니다. 이 문서에서는 이러한 네트워크 지점에 대해 연결 된 번호를 기준으로이 문서를 참조 합니다. Express 경로 연결 모델--클라우드 교환 공동 배치, 지점 간 이더넷 연결 또는 임의 위치 (IPVPN)에 따라 네트워크 지점 3과 4는 스위치 (계층 2 장치) 또는 라우터 (계층 3 장치) 일 수 있습니다. 직접 연결 모델에는 네트워크 점수 3 및 4가 없습니다. 대신 CEs (2)는 진한 파이버를 통해 MSEEs에 직접 연결 됩니다. 위의 다이어그램에서 보여 주는 주요 네트워크 지점은 다음과 같습니다.

1.  고객 컴퓨팅 디바이스(예: 서버 또는 PC)
2.  CE: 고객 에지 라우터 
3.  PE(CE 연결): 고객 에지 라우터에 연결되는 공급자 에지 라우터/스위치입니다. 이 문서에서는 PE-CE라고 합니다.
4.  PE(MSEE 연결): MSEE에 연결되는 공급자 에지 라우터/스위치입니다. 이 문서에서는 PE-MSEE라고 합니다.
5.  MSEE: MSEE(Microsoft Enterprise Edge) ExpressRoute 라우터
6.  VNet(Virtual Network) 게이트웨이
7.  Azure VNet의 컴퓨팅 디바이스

클라우드 교환 위치, 지점 간 이더넷 또는 직접 연결 모델이 사용 되는 경우 CEs (2) MSEEs (5)와 BGP 피어 링을 설정 합니다. 

임의 (IPVPN) 연결 모델을 사용 하는 경우 PE는 (4) MSEEs (5)를 사용 하 여 BGP 피어 링을 설정 합니다. PE는 IPVPN 서비스 공급자 네트워크를 통해 Microsoft에서 받은 경로를 고객 네트워크에 다시 전파 합니다.

> [!NOTE]
>고가용성을 위해 Microsoft는 MSEEs (5)와 PE (4) 쌍 사이에 완전 한 중복 병렬 연결을 설정 합니다. 고객 네트워크와 PE CEs 쌍 간에도 완전 한 중복 병렬 네트워크 경로가 권장 됩니다. 고가용성에 대한 자세한 내용은 Express 경로를 [사용 하 여 고가용성을 위한 디자인][HA] 문서를 참조 하세요.
>
>

Express 경로 문제 해결의 논리적 단계는 다음과 같습니다.

* [회로 프로 비전 및 상태 확인](#verify-circuit-provisioning-and-state)
  
* [피어 링 구성 유효성 검사](#validate-peering-configuration)
  
* [ARP 유효성 검사](#validate-arp)
  
* [MSEE에서 BGP 및 경로 유효성 검사](#validate-bgp-and-routes-on-the-msee)
  
* [트래픽 흐름 확인](#confirm-the-traffic-flow)


## <a name="verify-circuit-provisioning-and-state"></a>회로 프로 비전 및 상태 확인
Express 경로 회로를 프로 비전 하면 CEs/s s o m a s/s s o m a s/s e r v e r 2 (2)/(4)와 MSEEs (5) Express 경로 회로를 만들고, 수정 하 고, 프로 비전 하 고, 확인 하는 방법에 대한 자세한 내용은 [express 경로 회로 만들기 및 수정][CreateCircuit]문서를 참조 하세요.

>[!TIP]
>서비스 키는 ExpressRoute 회로를 고유하게 식별합니다. Express 경로 문제를 해결 하기 위해 Microsoft 또는 Express 경로 파트너의 도움이 필요 하면 회로를 쉽게 식별할 수 있도록 서비스 키를 제공 합니다.
>
>

### <a name="verification-via-the-azure-portal"></a>Azure Portal을 통한 확인
Azure Portal에서 Express 경로 회로 블레이드를 엽니다. 블레이드의 ![3][3] 섹션에는 다음 스크린샷에 표시 된 것 처럼 express 경로 essentials가 나열 됩니다.

![4][4]    

ExpressRoute Essentials에서 *회로 상태*는 Microsoft 쪽 회로 상태를 나타냅니다. *공급자 상태*는 서비스 공급자 쪽에서 회로가 *프로비전됨/프로비전되지 않음*인지를 나타냅니다. 

ExpressRoute 회로가 작동하려면 *회로 상태*가 *사용*이고, *공급자 상태*가 *프로비전됨*이어야 합니다.

> [!NOTE]
> Express 경로 회로를 구성한 후 *회로 상태가* 사용 안 함 상태로 설정 된 경우 [Microsoft 지원][Support]에 문의 하세요. 반면 *공급자 상태가* 프로 비전 되지 않음 상태로 설정 된 경우 서비스 공급자에 게 문의 하세요.
>
>

### <a name="verification-via-powershell"></a>PowerShell을 통한 확인
리소스 그룹의 모든 ExpressRoute 회로를 나열하려면 다음 명령을 사용합니다.

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG"

>[!TIP]
>리소스 그룹의 이름을 찾는 경우 *AzResourceGroup* 명령을 사용 하 여 구독에 있는 모든 리소스 그룹을 나열 하 여 가져올 수 있습니다.
>


[리소스 그룹]에서 특정 ExpressRoute 회로를 선택하려면 다음 명령을 사용합니다.

    Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"

샘플 응답은 다음과 같습니다.

    Name                             : Test-ER-Ckt
    ResourceGroupName                : Test-ER-RG
    Location                         : westus2
    Id                               : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt
    Etag                             : W/"################################"
    ProvisioningState                : Succeeded
    Sku                              : {
                                        "Name": "Standard_UnlimitedData",
                                        "Tier": "Standard",
                                        "Family": "UnlimitedData"
                                        }
    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned
    ServiceProviderNotes             : 
    ServiceProviderProperties        : {
                                        "ServiceProviderName": "****",
                                        "PeeringLocation": "******",
                                        "BandwidthInMbps": 100
                                        }
    ServiceKey                       : **************************************
    Peerings                         : []
    Authorizations                   : []

ExpressRoute 회로가 작동하는지 확인하려면 다음 필드에 특히 주의하세요.

    CircuitProvisioningState         : Enabled
    ServiceProviderProvisioningState : Provisioned

> [!NOTE]
> Express 경로 회로를 구성한 후 *회로 상태가* 사용 안 함 상태로 설정 된 경우 [Microsoft 지원][Support]에 문의 하세요. 반면 *공급자 상태가* 프로 비전 되지 않음 상태로 설정 된 경우 서비스 공급자에 게 문의 하세요.
>
>

## <a name="validate-peering-configuration"></a>피어링 구성 유효성 검사
서비스 공급자가 Express 경로 회로 프로 비전을 완료 한 후 CEs/MSEE-Pe (2)/(4)/(4)와 Msee (5) 사이에서 Express 경로 회로를 통해 여러 eBGP 기반 라우팅 구성을 만들 수 있습니다. 각 Express 경로 회로에는 Azure 개인 피어 링 (Azure에서 개인 가상 네트워크로의 트래픽) 및/또는 Microsoft 피어 링 (PaaS 및 SaaS의 공용 엔드포인트에 대한 트래픽)이 있을 수 있습니다. 라우팅 구성을 만들고 수정 하는 방법에 대한 자세한 내용은 [express 경로 회로에 대한 라우팅 만들기 및 수정][CreatePeering]문서를 참조 하세요.

### <a name="verification-via-the-azure-portal"></a>Azure Portal을 통한 확인

> [!NOTE]
> IPVPN 연결 모델에서 서비스 공급자는 피어 링 (계층 3 서비스)을 구성 하는 책임을 처리 합니다. 이러한 모델에서 서비스 공급자가 피어 링을 구성 하 고 포털에서 피어 링이 비어 있으면 포털에서 새로 고침 단추를 사용 하 여 회로 구성을 새로 고쳐 봅니다. 이 작업은 회로에서 현재 라우팅 구성을 가져옵니다. 
>

Azure Portal express 경로 회로 블레이드에서 Express 경로 회로 피어 링의 상태를 확인할 수 있습니다. 블레이드의 ![3][3] 개 섹션에서 express 경로 피어 링은 다음 스크린샷에 표시 된 것 처럼 나열 됩니다.

![5][5]

앞의 예제에서 설명한 대로 Azure 개인 피어 링이 프로 비전 되는 반면, Azure 공용 및 Microsoft 피어 링은 프로 비전 되지 않습니다. 성공적으로 프로 비전 된 피어 링 컨텍스트는 기본 및 보조 지점 간 서브넷도 포함 합니다. /30 서브넷은 MSEEs 및 CEs/PE의 인터페이스 IP 주소에 사용 됩니다. 프로 비전 되는 피어 링의 경우 목록에는 구성을 마지막으로 수정한 사용자도 표시 됩니다. 

> [!NOTE]
> 피어 링을 사용 하도록 설정 하지 못한 경우 할당 된 기본 및 보조 서브넷이 연결 된 CE/PE-MSEE의 구성과 일치 하는지 확인 합니다. 또한 올바른 *VlanId*, *AzureASN*및 *peerasn* 이 msees에서 사용 되는지와 이러한 값이 연결 된 CE/PE-msees에서 사용 된 값에 매핑되는지 여부를 확인 합니다. MD5 해시를 선택 하는 경우에는 공유 키가 MSEE 및 PE-MSEE/CE 쌍에서 동일 해야 합니다. 이전에 구성 된 공유 키가 보안상의 이유로 표시 되지 않습니다. MSEE 라우터에서 이러한 구성을 변경 해야 하는 경우 [express 경로 회로에 대한 라우팅 만들기 및 수정][CreatePeering]을 참조 하세요.  
>

> [!NOTE]
> 인터페이스에 할당 된/30 서브넷에서 Microsoft는 MSEE 인터페이스에 사용할 수 있는 서브넷의 두 번째 IP 주소를 선택 합니다. 따라서 서브넷의 사용 가능한 첫 번째 IP 주소가 피어 링 CE/PE-MSEE에 할당 되었는지 확인 합니다.
>


### <a name="verification-via-powershell"></a>PowerShell을 통한 확인
Azure 프라이빗 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePrivatePeering" -ExpressRouteCircuit $ckt

성공적으로 구성된 프라이빗 피어링에 대한 샘플 응답은 다음과 같습니다.

    Name                       : AzurePrivatePeering
    Id                         : /subscriptions/***************************/resourceGroups/Test-ER-RG/providers/***********/expressRouteCircuits/Test-ER-Ckt/peerings/AzurePrivatePeering
    Etag                       : W/"################################"
    PeeringType                : AzurePrivatePeering
    AzureASN                   : 12076
    PeerASN                    : 123##
    PrimaryPeerAddressPrefix   : 172.16.0.0/30
    SecondaryPeerAddressPrefix : 172.16.0.4/30
    PrimaryAzurePort           : 
    SecondaryAzurePort         : 
    SharedKey                  : 
    VlanId                     : 200
    MicrosoftPeeringConfig     : null
    ProvisioningState          : Succeeded

 성공적으로 사용되는 피어링 컨텍스트에는 기본 및 보조 주소 접두사가 나열되어 있습니다. /30 서브넷은 MSEEs 및 CEs/PE의 인터페이스 IP 주소에 사용 됩니다.

Azure 공용 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
    Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering" -ExpressRouteCircuit $ckt

Microsoft 피어링 구성 세부 정보를 가져오려면 다음 명령을 사용합니다.

    $ckt = Get-AzExpressRouteCircuit -ResourceGroupName "Test-ER-RG" -Name "Test-ER-Ckt"
     Get-AzExpressRouteCircuitPeeringConfig -Name "MicrosoftPeering" -ExpressRouteCircuit $ckt

피어링이 구성되어 있지 않으면 오류 메시지가 표시됩니다. 명시된 피어링(이 예제에서는 Azure 공용 피어링)이 회로 내에 구성되지 않은 경우 샘플 응답은 다음과 같습니다.

    Get-AzExpressRouteCircuitPeeringConfig : Sequence contains no matching element
    At line:1 char:1
        + Get-AzExpressRouteCircuitPeeringConfig -Name "AzurePublicPeering ...
        + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
            + CategoryInfo          : CloseError: (:) [Get-AzExpr...itPeeringConfig], InvalidOperationException
            + FullyQualifiedErrorId : Microsoft.Azure.Commands.Network.GetAzureExpressRouteCircuitPeeringConfigCommand


> [!NOTE]
> 피어 링을 사용 하도록 설정 하지 못한 경우 할당 된 기본 및 보조 서브넷이 연결 된 CE/PE-MSEE의 구성과 일치 하는지 확인 합니다. 또한 올바른 *VlanId*, *AzureASN*및 *peerasn* 이 msees에서 사용 되는지와 이러한 값이 연결 된 CE/PE-msees에서 사용 된 값에 매핑되는지 여부를 확인 합니다. MD5 해시를 선택 하는 경우에는 공유 키가 MSEE 및 PE-MSEE/CE 쌍에서 동일 해야 합니다. 이전에 구성 된 공유 키가 보안상의 이유로 표시 되지 않습니다. MSEE 라우터에서 이러한 구성을 변경 해야 하는 경우 [express 경로 회로에 대한 라우팅 만들기 및 수정][CreatePeering]을 참조 하세요.  
>
>

> [!NOTE]
> 인터페이스에 할당 된/30 서브넷에서 Microsoft는 MSEE 인터페이스에 사용할 수 있는 서브넷의 두 번째 IP 주소를 선택 합니다. 따라서 서브넷의 사용 가능한 첫 번째 IP 주소가 피어 링 CE/PE-MSEE에 할당 되었는지 확인 합니다.
>

## <a name="validate-arp"></a>ARP 유효성 검사

ARP 테이블은 특정 피어 링에 대한 IP 주소와 MAC 주소 매핑을 제공 합니다. ExpressRoute 회로 피어링의 ARP 테이블은 각 인터페이스(기본 및 보조)에 대한 다음 정보를 제공합니다.
* 온-프레미스 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
* ExpressRoute 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
* 매핑 ARP 테이블은 계층 2 구성의 유효성을 검사 하 고 기본적인 계층 2 연결 문제를 해결 하는 데 도움이 될 수 있습니다.


Express 경로 피어 링의 ARP 테이블을 보는 방법 및 정보를 사용 하 여 계층 2 연결 문제를 해결 하는 방법은 [리소스 관리자 배포 모델 문서에서 arp 테이블 가져오기][ARP] 를 참조 하세요.


## <a name="validate-bgp-and-routes-on-the-msee"></a>MSEE에서 BGP 및 경로 유효성 검사

*개인* 라우팅 컨텍스트의 *기본* 경로에 있는 msee에서 라우팅 테이블을 가져오려면 다음 명령을 사용 합니다.

    Get-AzExpressRouteCircuitRouteTable -DevicePath Primary -ExpressRouteCircuitName ******* -PeeringType AzurePrivatePeering -ResourceGroupName ****

예제 응답은 다음과 같습니다.

    Network : 10.1.0.0/16
    NextHop : 10.17.17.141
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.1.0.0/16
    NextHop : 10.17.17.140*
    LocPrf  : 
    Weight  : 0
    Path    : 65515

    Network : 10.2.20.0/25
    NextHop : 172.16.0.1
    LocPrf  : 
    Weight  : 0
    Path    : 123##


> [!NOTE]
> MAB및 CE/PE-MSEE 간의 eBGP 피어 링 상태가 활성 또는 유휴 상태 이면 할당 된 기본 및 보조 피어 서브넷이 연결 된 CE/PE의 구성과 일치 하는지 확인 합니다. 또한 올바른 *VlanId*, *AzureAsn*및 *peerasn* 이 msees에서 사용 되는지와 이러한 값이 연결 된 PE-msees/CE에서 사용 된 값에 매핑되는지 여부를 확인 합니다. MD5 해시를 선택 하는 경우 MSEE 및 CE/PE에서 공유 키가 동일 해야 합니다. MSEE 라우터에서 이러한 구성을 변경 해야 하는 경우 [express 경로 회로에 대한 라우팅 만들기 및 수정][CreatePeering]을 참조 하세요.
>


> [!NOTE]
> 피어 링을 통해 특정 대상에 연결할 수 없는 경우 해당 피어 링 컨텍스트를 확인 하는 MSEEs의 경로 테이블을 확인 합니다. 라우팅 테이블에 일치 하는 접두사 (Ngip 일 수 있음)가 있는 경우 트래픽을 차단 하는 경로에 방화벽/n a m e/n a m e의 Acl이 있는지 확인 합니다.
>


다음 예에서는 존재 하지 않는 피어 링에 대한 명령의 응답을 보여 줍니다.

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="confirm-the-traffic-flow"></a>트래픽 흐름 확인
피어링 컨텍스트의 기본 및 보조 경로 트래픽 통계(송/수신 바이트 수)를 가져오려면 다음 명령을 사용합니다.

    Get-AzureDedicatedCircuitStats -ServiceKey 97f85950-01dd-4d30-a73c-bf683b3a6e5c -AccessType Private

명령 출력의 예제는 다음과 같습니다.

    PrimaryBytesIn PrimaryBytesOut SecondaryBytesIn SecondaryBytesOut
    -------------- --------------- ---------------- -----------------
         240780020       239863857        240565035         239628474

존재하지 않는 피어링에 대한 명령 출력의 예제는 다음과 같습니다.

    Get-AzExpressRouteCircuitRouteTable : The BGP Peering AzurePublicPeering with Service Key ********************* is not found.
    StatusCode: 400

## <a name="next-steps"></a>다음 단계
자세한 정보 또는 도움말을 보려면 다음 링크를 확인하세요.

- [Microsoft 지원][Support]
- [ExpressRoute 회로 만들기 및 수정][CreateCircuit]
- [Express 경로 회로에 대한 라우팅 만들기 및 수정][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-expressroute-overview/expressroute-logical-diagram.png "논리 ExpressRoute 연결"
[2]: ./media/expressroute-troubleshooting-expressroute-overview/portal-all-resources.png "모든 리소스 아이콘"
[3]: ./media/expressroute-troubleshooting-expressroute-overview/portal-overview.png "개요 아이콘"
[4]: ./media/expressroute-troubleshooting-expressroute-overview/portal-circuit-status.png "ExpressRoute Essentials 샘플 스크린샷"
[5]: ./media/expressroute-troubleshooting-expressroute-overview/portal-private-peering.png "ExpressRoute Essentials 샘플 스크린샷"

<!--Link References-->
[Support]: https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ARP]: https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[DR-Pvt]: https://docs.microsoft.com/azure/expressroute/designing-for-disaster-recovery-with-expressroute-privatepeering





