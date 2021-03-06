---
title: Azure Network Watcher에 대 한 FAQ (질문과 대답) | Microsoft Docs
description: 이 문서에서는 Azure Network Watcher 서비스에 대 한 자주 묻는 질문에 답변 합니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: e7585880b98f62f819ff344c82846c2cfb1fd620
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98019825"
---
# <a name="frequently-asked-questions-faq-about-azure-network-watcher"></a>Azure Network Watcher에 대 한 FAQ (질문과 대답)
[Azure Network Watcher](./network-watcher-monitoring-overview.md) 서비스는 azure 가상 네트워크에서 리소스에 대 한 로그를 모니터링 하 고, 진단 하 고, 보고, 사용 하거나 사용 하지 않도록 설정 하는 도구 모음을 제공 합니다. 이 문서에서는 서비스에 대 한 일반적인 질문에 답변 합니다.

## <a name="general"></a>일반

### <a name="what-is-network-watcher"></a>Network Watcher란?
Network Watcher는 Azure virtual network의 Virtual Machines, 가상 네트워크, 응용 프로그램 게이트웨이, 부하 분산 장치 및 기타 리소스를 포함 하는 IaaS (Infrastructure as a Service) 구성 요소의 네트워크 상태를 모니터링 하 고 복구 하도록 설계 되었습니다. PaaS (Platform as a Service) 인프라를 모니터링 하거나 웹/모바일 분석을 받기 위한 솔루션은 아닙니다.

### <a name="what-tools-does-network-watcher-provide"></a>Network Watcher에서 제공 하는 도구
Network Watcher는 세 가지 주요 기능 집합을 제공 합니다.
* 모니터링
  * [토폴로지 보기](./view-network-topology.md) 에는 가상 네트워크의 리소스와 이러한 리소스 간의 관계가 표시 됩니다.
  * [연결 모니터](./connection-monitor.md) 를 사용 하면 VM과 다른 네트워크 리소스 간의 연결 및 대기 시간을 모니터링할 수 있습니다.
  * [네트워크 성능 모니터](../azure-monitor/insights/network-performance-monitor.md) 를 사용 하면 하이브리드 네트워크 아키텍처, express 경로 회로 및 서비스/응용 프로그램 끝점에서 연결 및 대기 시간을 모니터링할 수 있습니다.  
* 진단
  * [IP 흐름 확인](./network-watcher-ip-flow-verify-overview.md) 을 사용 하면 VM 수준에서 트래픽 필터링 문제를 검색할 수 있습니다.
  * [다음 홉](./network-watcher-next-hop-overview.md) 은 트래픽 경로를 확인 하 고 라우팅 문제를 검색 하는 데 도움이 됩니다.
  * [연결 문제 해결](./network-watcher-connectivity-portal.md) 을 사용 하면 VM과 다른 네트워크 리소스 간에 일회성 연결 및 대기 시간을 확인할 수 있습니다.
  * [패킷 캡처](./network-watcher-packet-capture-overview.md) 를 사용 하면 가상 네트워크의 VM에 대 한 모든 트래픽을 캡처할 수 있습니다.
  * [Vpn 문제 해결](./network-watcher-troubleshoot-overview.md) 에서는 문제를 디버그 하는 데 도움이 되도록 vpn gateway 및 연결에 대 한 여러 진단 검사를 실행 합니다.
* 로깅
  * [Nsg 흐름 로그](./network-watcher-nsg-flow-logging-overview.md) 를 사용 하 여 [Nsg (네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md) 의 모든 트래픽을 기록할 수 있습니다.
  * [트래픽 분석](./traffic-analytics.md) 는 Nsg 흐름 로그 데이터를 처리 하 여 네트워크 트래픽을 시각화, 쿼리, 분석 및 이해할 수 있도록 합니다.


자세한 내용은 [Network Watcher 개요 페이지](./network-watcher-monitoring-overview.md)를 참조 하세요.


### <a name="how-does-network-watcher-pricing-work"></a>Network Watcher 가격은 어떻게 작동 하나요?
Network Watcher 구성 요소 및 가격 책정에 대 한 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/network-watcher/) 를 방문 하세요.

### <a name="which-regions-is-network-watcher-supportedavailable-in"></a>Network Watcher 지원/사용할 수 있는 지역은 어디 인가요?
[Azure 서비스 가용성 페이지](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher) 에서 최신 지역의 가용성을 확인할 수 있습니다.

### <a name="which-permissions-are-needed-to-use-network-watcher"></a>Network Watcher를 사용 하는 데 필요한 권한은 무엇입니까?
[Network Watcher를 사용 하는 데 필요한 AZURE RBAC 권한](./required-rbac-permissions.md)목록을 참조 하세요. 리소스를 배포 하는 경우 NetworkWatcherRG에 대 한 참가자 권한이 필요 합니다 (아래 참조).

### <a name="how-do-i-enable-network-watcher"></a>Network Watcher를 사용하도록 설정하려면 어떻게 해야 하나요?
Network Watcher 서비스는 모든 구독에 대해 [자동으로 사용 하도록 설정](https://azure.microsoft.com/updates/azure-network-watcher-will-be-enabled-by-default-for-subscriptions-containing-virtual-networks/) 됩니다.

### <a name="what-is-the-network-watcher-deployment-model"></a>Network Watcher 배포 모델은 무엇 인가요?
Network Watcher 부모 리소스는 모든 지역에 고유한 인스턴스를 사용 하 여 배포 됩니다. 이름 지정 형식: NetworkWatcher_RegionName. 예: NetworkWatcher_centralus은 "미국 중부" 지역에 대 한 Network Watcher 리소스입니다.

### <a name="what-is-the-networkwatcherrg"></a>NetworkWatcherRG 무엇 인가요?
Network Watcher 리소스는 자동으로 생성 되는 hidden **NetworkWatcherRG** 리소스 그룹에 있습니다. 예를 들어 NSG 흐름 로그 리소스는 Network Watcher의 자식 리소스 이며 NetworkWatcherRG에서 사용 하도록 설정 됩니다.

### <a name="why-do-i-need-to-install-the-network-watcher-extension"></a>Network Watcher 확장을 설치 해야 하는 이유는 무엇 인가요? 
Network Watcher 확장은 VM에서 트래픽을 생성 하거나 가로채는 데 필요한 모든 기능에 필요 합니다. 

### <a name="which-features-require-the-network-watcher-extension"></a>Network Watcher 확장이 필요한 기능은 무엇입니까?
패킷 캡처, 연결 문제 해결 및 연결 모니터 기능을 설치 하려면 Network Watcher 확장이 필요 합니다.

### <a name="what-are-resource-limits-on-network-watcher"></a>Network Watcher에 대 한 리소스 제한은 무엇 인가요?
모든 제한에 대해서는 [서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#network-watcher-limits) 페이지를 참조 하세요.  

### <a name="why-is-only-one-instance-of-network-watcher-allowed-per-region"></a>지역별 Network Watcher 인스턴스는 하나만 허용 되는 이유는 무엇 인가요? 
Network Watcher 작동 하는 기능에 대 한 구독에 대해 한 번만 사용 하도록 설정 해야 하는 것은 서비스 제한이 아닙니다.

### <a name="how-can-i-manage-the-network-watcher-resource"></a>Network Watcher 리소스를 어떻게 관리할 수 있나요? 
Network Watcher 리소스는 Network Watcher에 대 한 백 엔드 서비스를 나타내며 Azure를 통해 완전히 관리 됩니다. 고객은 관리할 필요가 없습니다. Move 등의 작업은 리소스에서 지원 되지 않습니다. 그러나 [리소스를 삭제할 수 있습니다](./network-watcher-create.md#delete-a-network-watcher-in-the-portal). 

## <a name="service-availability-and-redundancy"></a>서비스 가용성 및 중복성 

### <a name="is-the-network-watcher-service-zone-resilient"></a>Network Watcher 서비스 영역 복원 력이 있나요? 
예. Network Watcher 서비스는 기본적으로 영역 복원 가능 합니다. 

### <a name="how-do-i-configure-the-network-watcher-service-to-be-zone-resilient"></a>Network Watcher 서비스를 영역 복원 력이 되도록 구성 어떻게 할까요? 
영역 복원력을 사용하도록 설정하기 위한 고객 구성은 필요 없습니다. 영역-Network Watcher 리소스에 대 한 복원 력은 기본적으로 제공 되며 서비스 자체에서 관리 됩니다. 

## <a name="nsg-flow-logs"></a>NSG 흐름 로그

### <a name="what-does-nsg-flow-logs-do"></a>NSG 흐름 로그는 어떻게 되나요?
Azure 네트워크 리소스는 [NSGs (네트워크 보안 그룹)](../virtual-network/network-security-groups-overview.md)를 통해 결합 및 관리할 수 있습니다. NSG 흐름 로그를 사용 하 여 Nsg를 통한 모든 트래픽에 대 한 5 튜플 흐름 정보를 기록할 수 있습니다. 원시 흐름 로그는 필요에 따라 추가로 처리, 분석, 쿼리 또는 내보낼 수 있는 Azure Storage 계정에 기록 됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-firewall"></a>방화벽 뒤의 저장소 계정을 사용 하 여 NSG 흐름 로그를 사용 어떻게 할까요??

방화벽 뒤의 저장소 계정을 사용 하려면 저장소 계정에 액세스 하기 위해 신뢰할 수 있는 Microsoft 서비스에 대 한 예외를 제공 해야 합니다.

* 포털의 전역 검색 또는 [저장소 계정 페이지](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Storage%2FStorageAccounts) 에서 저장소 계정 이름을 입력 하 여 저장소 계정으로 이동 합니다.
* **설정** 섹션 아래에서 **방화벽 및 가상 네트워크** 를 선택합니다.
* "액세스 허용"에서 **선택한 네트워크** 를 선택 합니다. 그런 다음 **예외** 아래에서 **"신뢰할 수 있는 Microsoft 서비스가이 저장소 계정에 액세스할 수 있도록 허용"** 옆의 상자를 선택 합니다. 
* 이미 선택되어 있는 경우에는 변경할 필요가 없습니다.  
* [Nsg 흐름 로그 개요 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) 에서 대상 nsg를 찾고, 위의 저장소 계정을 선택 하 여 Nsg 흐름 로그를 사용 하도록 설정 합니다.

몇 분 후에 스토리지 로그를 확인할 수 있습니다. 그러면 업데이트된 타임스탬프 또는 새로 만든 JSON 파일이 표시됩니다.

### <a name="how-do-i-use-nsg-flow-logs-with-a-storage-account-behind-a-service-endpoint"></a>서비스 끝점 뒤의 저장소 계정으로 NSG 흐름 로그를 사용 어떻게 할까요??

NSG 흐름 로그는 추가 구성이 필요 없이 서비스 끝점과 호환 됩니다. 가상 네트워크에서 [서비스 끝점을 사용 하도록 설정 하는 방법에 대 한 자습서](../virtual-network/tutorial-restrict-network-access-to-resources.md#enable-a-service-endpoint) 를 참조 하세요.


### <a name="what-is-the-difference-between-flow-logs-versions-1--2"></a>흐름 로그 버전 1 & 2의 차이점은 무엇 인가요?
흐름 로그 버전 2에는 전송 되는 바이트와 패킷에 대 한 정보를 저장 하 & *흐름 상태의* 개념이 도입 되었습니다. [자세히 알아봅니다](./network-watcher-nsg-flow-logging-overview.md#log-format).

## <a name="next-steps"></a>다음 단계
 - Network Watcher 시작 하는 데 도움이 되는 몇 가지 자습서는 [설명서 개요 페이지](./index.yml) 를 참조 하세요.