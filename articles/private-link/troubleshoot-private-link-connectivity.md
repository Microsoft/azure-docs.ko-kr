---
title: Azure 개인 링크 연결 문제 해결
description: 개인 링크 연결을 진단 하는 단계별 지침
services: private-link
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
ms.openlocfilehash: 0d26ad6802e551523875dcad13066fdbdbf39ada
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77191048"
---
# <a name="troubleshoot-private-link-service-connectivity-problems"></a>Private Link Service 연결 문제 해결

이 가이드에서는 개인 링크 서비스 설정에 대 한 연결의 유효성을 검사 하 고 진단 하는 단계별 지침을 제공 합니다. 

Azure 개인 링크를 사용 하면 가상 네트워크의 개인 끝점을 통해 azure PaaS 서비스 (예: Azure Storage, Azure Cosmos DB 및 SQL Database)와 Azure에서 호스트 되는 고객/파트너 서비스에 액세스할 수 있습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. VNet (가상 네트워크)에서 개인 링크 서비스를 만들어 고객에 게 개인적으로 제공할 수도 있습니다. 

개인 링크 액세스를 위해 Azure 표준 Load Balancer 뒤에서 실행 되는 서비스를 사용 하도록 설정할 수 있습니다. 서비스 소비자는 가상 네트워크 내에 개인 끝점을 만든 다음이 서비스에 매핑하여 개인적으로 액세스할 수 있습니다.

개인 링크 서비스에서 사용할 수 있는 연결 시나리오는 다음과 같습니다.
- 동일한 지역의 가상 네트워크 
- 지역적으로 피어 링 가상 네트워크
- 글로벌 피어 링 가상 네트워크
- VPN 또는 Express 경로 회로를 통한 온-프레미스 고객

## <a name="deployment-troubleshooting"></a>배포 문제 해결

개인 링크 서비스에 대 한 사용자가 선택한 서브넷에서 원본 IP 주소를 선택할 수 없는 경우 문제 해결을 위해 [개인 링크 서비스에서 네트워크 정책 사용 안 함](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) 정보를 검토 합니다.

원본 IP 주소를 선택 하는 서브넷에 대해 **privateLinkServiceNetworkPolicies** 설정을 사용 하지 않도록 설정 했는지 확인 합니다.

## <a name="diagnosing-connectivity-problems"></a>연결 문제 진단

개인 링크 설정에 연결 하는 데 문제가 발생 하는 경우 아래 나열 된 단계를 진행 하 여 모든 일반적인 구성이 예상 대로 작동 하는지 확인 합니다.

1. 리소스를 검색 하 여 개인 링크 서비스 구성을 검토 합니다. 

    a) **개인 링크 센터** 로 이동

      ![개인 링크 센터](./media/private-link-tsg/private-link-center.png)

    b) 왼쪽 탐색 창에서 **개인 링크 서비스** 를 선택 합니다.

      ![개인 링크 서비스](./media/private-link-tsg/private-link-service.png)

    c) 진단 하려는 개인 링크 서비스를 필터링 하 고 선택 합니다.

    d) 개인 끝점 연결을 검토 합니다.
     - 연결을 검색 하는 개인 끝점이 **승인** 된 연결 상태로 나열 되는지 확인 합니다. 
     - **보류 중인**경우 선택 하 고 승인 합니다. 

       ![개인 끝점 연결](./media/private-link-tsg/pls-private-endpoint-connections.png)

     - 이름을 클릭 하 여 연결 하려는 개인 끝점으로 이동 합니다. 연결 상태가 **승인 됨**으로 표시 되는지 확인 합니다.

       ![개인 끝점 연결 개요](./media/private-link-tsg/pls-private-endpoint-overview.png)

     - 양쪽 모두 승인 되 면 연결을 다시 시도 합니다.

    e) [속성] 탭에서 [개요] 탭 및 [ **리소스 ID** ]에서 **별칭** 을 검토 하십시오. 
     - 이 서비스에 대 한 개인 끝점을 만드는 데 사용 하는 별칭/리소스 **id** 가 **별칭/리소스** id와 일치 하는지 확인 합니다. 

       ![별칭 확인](./media/private-link-tsg/pls-overview-pane-alias.png)

       ![리소스 ID 확인](./media/private-link-tsg/pls-properties-pane-resourceid.png)

    f) 가시성 (개요 섹션) 정보를 검토 합니다.
     - 구독이 **표시** 범위에 속하는지 확인 합니다.

       ![표시 여부 확인](./media/private-link-tsg/pls-overview-pane-visibility.png)

    g) Load Balancer (개요) 정보 검토
     - 부하 분산 장치 링크를 클릭 하 여 부하 분산 장치로 이동할 수 있습니다.

       ![확인 Load Balancer](./media/private-link-tsg/pls-overview-pane-ilb.png)

     - 요구 사항에 따라 Load Balancer 설정이 구성 되었는지 확인 합니다.
       - 프런트 엔드 IP 구성 검토
       - 백 엔드 풀 검토
       - 부하 분산 규칙 검토

       ![Load Balancer 속성 확인](./media/private-link-tsg/pls-ilb-properties.png)

     - 위의 설정에 따라 Load Balancer 작동 하는지 확인 합니다.
       - Load Balancer 백 엔드 풀을 사용할 수 있는 서브넷 이외의 서브넷에서 VM을 선택 합니다.
       - 위의 VM에서 부하 분산 장치 프런트 엔드에 액세스 해 보세요.
       - 부하 분산 규칙에 따라 백 엔드 풀에 연결 하는 경우 부하 분산 장치가 작동 합니다.
       - Azure Monitor를 통해 Load Balancer 메트릭을 검토 하 여 부하 분산 장치를 통해 데이터가 흐르는 경우를 확인할 수도 있습니다.

2. [**Azure Monitor**](https://docs.microsoft.com/azure/azure-monitor/overview) 를 사용 하 여 데이터 흐름 검토

    a) 개인 링크 서비스 리소스에서 **메트릭** 을 선택 합니다.
     - **바이트** 또는 **바이트 제한** 선택
     - 개인 링크 서비스에 연결 하려고 할 때 검토 데이터가 흐르는 중입니다. 10 분 정도 지연 될 것으로 간주 합니다.

       ![개인 링크 서비스 메트릭 확인](./media/private-link-tsg/pls-metrics.png)

3. 문제가 여전히 해결 되지 않으며 연결 문제가 여전히 존재 하는 경우 [Azure 지원](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) 팀에 문의 하세요. 

## <a name="next-steps"></a>다음 단계

 * [개인 링크 서비스 만들기 (CLI)](https://docs.microsoft.com/azure/private-link/create-private-link-service-cli)

 * [개인 끝점 문제 해결 가이드](troubleshoot-private-endpoint-connectivity.md)
