---
title: 'ARP 테이블 가져오기 - ExpressRoute 문제 해결: 클래식: Azure | Microsoft Docs'
description: 이 페이지에서는 ExpressRoute 회로 - 클래식 배포 모델의 ARP 테이블을 가져오기 위한 지침을 제공합니다.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 01/30/2017
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 3e49a1da0e8ea83faf5fc5a10d4c01a41d62fa88
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883099"
---
# <a name="getting-arp-tables-in-the-classic-deployment-model"></a>클래식 배포 모델에서 ARP 테이블 가져오기
> [!div class="op_single_selector"]
> * [PowerShell - Resource Manager](expressroute-troubleshooting-arp-resource-manager.md)
> * [PowerShell - 클래식](expressroute-troubleshooting-arp-classic.md)
> 
> 

이 문서에서는 Azure ExpressRoute 회로에 대한 ARP(주소 확인 프로토콜) 테이블을 가져오는 단계를 안내합니다.

> [!IMPORTANT]
> 이 문서는 간단한 문제를 진단하고 수정하는 데 사용됩니다. 이 문서는 Microsoft 기술 지원 서비스를 대체하지 않습니다. 다음 지침을 사용하여 문제를 해결할 수 없는 경우 [Microsoft Azure 도움말+지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 사용하여 지원 요청을 엽니다.
> 
> 

## <a name="address-resolution-protocol-arp-and-arp-tables"></a>주소 확인 프로토콜(ARP) 및 ARP 테이블
ARP는 [RFC 826](https://tools.ietf.org/html/rfc826)에 정의된 계층 2 프로토콜입니다. ARP는 IP 주소로 이더넷 주소(MAC 주소)를 매핑하는 데 사용됩니다.

ARP 테이블은 특정 피어링에 대한 IPv4 주소와 MAC 주소 매핑을 제공합니다. ExpressRoute 회로 피어링의 ARP 테이블은 각 인터페이스(기본 및 보조)에 대한 다음 정보를 제공합니다.

1. 온-프레미스 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
2. ExpressRoute 라우터 인터페이스 IP 주소를 MAC 주소에 매핑
3. 매핑 사용 기간

ARP 테이블은 계층 2 구성의 유효성을 검사하고 기본적인 계층 2 연결 문제를 해결하는 데 도움을 줄 수 있습니다.

다음은 ARP 테이블의 예제입니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


다음 섹션에서는 ExpressRoute 에지 라우터에서 표시한 ARP 테이블을 보는 방법에 대한 정보를 제공합니다.

## <a name="prerequisites-for-using-arp-tables"></a>ARP 테이블을 사용하기 위한 필수 조건
계속하기 전에 다음이 있는지 확인합니다.

* 1개 이상 피어링으로 구성된 유효한 ExpressRoute 회로 연결 공급자가 완벽히 구성한 회로여야 합니다. 사용자(또는 연결 공급자)는 이 회로에서 하나 이상의 피어링을 구성했어야 합니다(Azure 개인, Azure 공용 또는 Microsoft).
* 피어링(Azure 개인, Azure 공용 및 Microsoft) 구성에 사용한 IP 주소 범위 IP 주소 할당 예제를 검토 합니다 [ExpressRoute 라우팅 요구 사항 페이지](expressroute-routing.md) 쪽에서 및 ExpressRoute 측에서 인터페이스에 IP 주소가 매핑되는 방법을 이해 하려면. [ExpressRoute 피어링 구성 페이지](expressroute-howto-routing-classic.md)를 검토하면 피어링 구성에 관한 정보를 얻을 수 있습니다.
* 해당 IP 주소와 함께 사용되는 인터페이스의 MAC 주소에 대한 네트워킹 팀 또는 연결 공급자가 제공한 정보
* Azure용 최신 Windows PowerShell 모듈(1.50 이상)

## <a name="arp-tables-for-your-expressroute-circuit"></a>ExpressRoute 회로에 대한 ARP 테이블
이 섹션에서는 PowerShell을 사용하여 피어링의 각 형식에 대한 ARP 테이블을 표시하는 방법에 대한 지침을 제공합니다. 계속하기 전에 연결 공급자가 또는 피어링을 구성해야 합니다. 각 회로는 두 가지 경로(기본 및 보조)가 있습니다. 각 경로의 ARP 테이블을 독립적으로 확인할 수 있습니다.

### <a name="arp-tables-for-azure-private-peering"></a>Azure 개인 피어링용 ARP 테이블
다음 cmdlet은 Azure 개인 피어링용 ARP 테이블을 제공합니다.

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure private peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

        # ARP table for Azure private peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

경로 중 하나에 대한 샘플 출력은 다음과 같습니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-azure-public-peering"></a>Azure 공용 피어링용 ARP 테이블
다음 cmdlet은 Azure 공용 피어링용 ARP 테이블을 제공합니다.

        # Required variables
        $ckt = "<your Service Key here>

        # ARP table for Azure public peering--primary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

        # ARP table for Azure public peering--secondary path
        Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

경로 중 하나에 대한 샘플 출력은 다음과 같습니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           10.0.0.1 ffff.eeee.dddd
          0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


경로 중 하나에 대한 샘플 출력은 다음과 같습니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           64.0.0.1 ffff.eeee.dddd
          0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### <a name="arp-tables-for-microsoft-peering"></a>Microsoft 피어링용 ARP 테이블
다음 cmdlet은 Microsoft 피어링용 ARP 테이블을 제공합니다.

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


아래의 샘플 출력은 그 경로 중 하나를 보여줍니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## <a name="how-to-use-this-information"></a>이 정보를 사용하는 방법
피어링의 ARP 테이블을 사용하여 계층 2 구성과 연결의 유효성을 검사할 수 있습니다. 이 섹션은 다양한 시나리오에서 ARP 테이블이 표시되는 방법의 개요를 제공합니다.

### <a name="arp-table-when-a-circuit-is-in-an-operational-expected-state"></a>회로가 작동 (예상) 상태일 때 ARP 테이블
* ARP 테이블은 유효한 IP 주소 및 MAC 주소가 포함된 온-프레미스 측의 항목과 Microsoft 측의 유사한 항목이 있습니다.
* 온-프레미스 IP 주소의 마지막 옥텟은 항상 홀수입니다.
* Microsoft IP 주소의 마지막 옥텟은 항상 짝수입니다.
* 세 개 피어링(기본/보조) 모두에 대해 Microsoft 쪽에 같은 MAC 주소가 나타납니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
         10 On-Prem           65.0.0.1 ffff.eeee.dddd
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### <a name="arp-table-when-its-on-premises-or-when-the-connectivity-provider-side-has-problems"></a>온-프레미스 또는 연결 공급자 측에 문제가 있을 때 ARP 테이블
 ARP 테이블에 하나의 항목만이 나타납니다. Microsoft 측에서 사용된 MAC 주소와 IP 주소 사이의 매핑을 보여 줍니다.

        Age InterfaceProperty IpAddress  MacAddress    
        --- ----------------- ---------  ----------    
          0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

> [!NOTE]
> 다음과 같은 문제가 발생하면 문제를 해결하는 연결 공급자를 사용하여 지원 요청을 엽니다.
> 
> 

### <a name="arp-table-when-the-microsoft-side-has-problems"></a>Microsoft 측에 문제가 있을 때 ARP 테이블
* Microsoft 측에 문제가 있으면 피어링에 대한 ARP 테이블이 나타나지 않습니다.
* [Microsoft Azure 도움말+지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)을 사용하여 지원 요청을 엽니다. 계층 2 연결 문제가 있다고 표시합니다.

## <a name="next-steps"></a>다음 단계
* ExpressRoute 회로에 대한 계층 3 구성의 유효성 검사:
  * 라우트 요약을 가져와서 BGP 세션의 상태 확인
  * 라우트 테이블에서 ExpressRoute에 접두사가 보급되었는지 확인
* 바이트 입출력을 검토하여 데이터 전송의 유효성 검사
* 여전히 문제가 해결되지 않을 경우 [Microsoft Azure 도움말+지원](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 을 사용하여 지원 요청을 엽니다.

