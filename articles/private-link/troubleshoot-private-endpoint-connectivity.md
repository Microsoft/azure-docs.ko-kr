---
title: Azure 개인 끝점 연결 문제 해결
description: 개인 끝점 연결을 진단 하는 단계별 지침
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: df4ec6ddbba029eb29d2440717697968f8c79302
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191056"
---
# <a name="troubleshoot-private-endpoint-connectivity-problems"></a>프라이빗 엔드포인트 연결 문제 해결

이 가이드에서는 개인 끝점 연결 설정의 유효성을 검사 하 고 진단 하는 단계별 지침을 제공 합니다. 

Azure 개인 끝점은 개인 링크 서비스에 안전 하 게 연결 되는 네트워크 인터페이스입니다. 이 솔루션은 가상 네트워크에서 Azure 서비스 리소스에 대 한 개인 연결을 제공 하 여 Azure에서 워크 로드를 보호 하는 데 도움이 됩니다. 이러한 서비스를 가상 네트워크에 효과적으로 제공 합니다. 

개인 끝점에서 사용할 수 있는 연결 시나리오는 다음과 같습니다. 
- 동일한 지역의 가상 네트워크 
- 지역적으로 피어 링 가상 네트워크
- 글로벌 피어 링 가상 네트워크
- VPN 또는 Express 경로 회로를 통한 온-프레미스 고객

## <a name="diagnosing-connectivity-problems"></a>연결 문제 진단 
아래 나열 된 단계를 수행 하 여 모든 일반적인 구성이 개인 끝점 설정의 연결 문제를 해결 하는 것으로 예상 되는지 확인 합니다.

1. 리소스를 검색 하 여 개인 끝점 구성을 검토 합니다. 

    a) **개인 링크 센터** 로 이동

      ![개인 링크 센터](./media/private-endpoint-tsg/private-link-center.png)

    b) 왼쪽 탐색 창에서 개인 끝점을 선택 합니다.
    
      ![전용 끝점](./media/private-endpoint-tsg/private-endpoints.png)

    c) 진단 하려는 개인 끝점을 필터링 하 고 선택 합니다.

    d) 가상 네트워크 및 DNS 정보 검토
    
     - 연결 상태가 **승인** 됨 인지 확인
     - VM이 개인 끝점을 호스트 하는 VNet에 연결 되어 있는지 확인 합니다.
     - FQDN 정보 (복사) 및 할당 된 개인 IP 주소
    
       ![VNet 및 DNS 구성](./media/private-endpoint-tsg/vnet-dns-configuration.png)    
    
2. [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) 를 사용 하 여 데이터 흐름 검토

    a) 개인 끝점 리소스에서 **모니터** 를 선택 합니다.
     - 데이터 또는 데이터를 선택 하 고 개인 끝점에 연결을 시도할 때 데이터 흐름이 진행 되는지 검토 합니다. 10 분 정도 지연 될 것으로 간주 합니다.
    
       ![개인 끝점 원격 분석 확인](./media/private-endpoint-tsg/private-endpoint-monitor.png)

3. **Network Watcher** 에서 VM 연결 문제 해결 사용

    a) 클라이언트 VM 선택

    b) **연결 문제 해결** 섹션, **아웃 바운드 연결** 탭을 선택 합니다.
    
      ![Network Watcher-아웃 바운드 연결 테스트](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c) **자세한 연결 추적을 위해 Network Watcher 사용을 선택 합니다** .
    
      ![Network Watcher 연결 문제 해결](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d) **FQDN으로 테스트** 선택
     - 개인 끝점 리소스에서 FQDN 붙여넣기
     - 포트를 제공 합니다 (*일반적으로 Azure Storage 또는 COSMOS의 경우 443, Sql의 경우 1336* ).

    e) **테스트** 를 클릭 하 고 테스트 결과의 유효성을 검사 합니다.
    
      ![Network Watcher-테스트 결과](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
4. 테스트 결과의 DNS 확인에는 개인 끝점에 할당 된 것과 동일한 개인 IP 주소가 있어야 합니다.

    a) DNS 설정이 올바르지 않으면 다음을 수행 합니다.
     - 개인 영역 사용: 
       - 클라이언트 VM VNet이 개인 영역에 연결 되어 있는지 확인 합니다.
       - 사설 DNS 영역 레코드가 있는지 검토 하 고, 기존 항목이 아닌 경우 만들기
    
     - 사용자 지정 DNS 사용:
       - 고객 DNS 설정을 검토 하 고 DNS 구성이 올바른지 확인 합니다.
       지침은 [개인 끝점 개요-DNS 구성](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration) 을 참조 하세요.

    b) NSG/UDRs 때문에 연결에 실패 하는 경우
     - NSG 아웃 바운드 규칙을 검토 하 고 트래픽을 허용 하는 적절 한 아웃 바운드 규칙을 만듭니다.
    
       ![NSG 아웃 바운드 규칙](./media/private-endpoint-tsg/nsg-outbound-rules.png)

5. 연결의 유효성을 검사 한 경우 연결 문제가 응용 프로그램 계층의 암호, 토큰, 암호 등의 다른 측면과 관련 될 수 있습니다.
   - 이 경우 개인 끝점과 연결 된 개인 링크 리소스의 구성을 검토 합니다. [개인 링크 문제 해결 가이드](troubleshoot-private-link-connectivity.md)를 참조 하세요. 

6. 문제가 여전히 해결 되지 않으며 연결 문제가 여전히 존재 하는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의 하세요. 

## <a name="next-steps"></a>다음 단계

 * [업데이트 된 서브넷에 개인 끝점 만들기 (Azure Portal)](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)

 * [개인 링크 문제 해결 가이드](troubleshoot-private-link-connectivity.md)
