---
title: 연결 모니터 (미리 보기) | Microsoft Docs
description: 연결 모니터 (미리 보기)를 사용 하 여 분산 환경에서 네트워크 통신을 모니터링 하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
Customer intent: I need to monitor communication between a VM and another VM. If the communication fails, I need to know why, so that I can resolve the problem.
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 43c49cce1dd53edd5c2b13b01a31f94752579dff
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169327"
---
# <a name="unified-connectivity-monitoring-with-connection-monitor-preview"></a>연결 모니터를 사용 하 여 통합 연결 모니터링 (미리 보기)

연결 모니터 (미리 보기)는 하이브리드 및 Azure 클라우드 배포에 대 한 Azure Network Watcher의 통합 종단 간 연결 모니터링 기능을 제공 합니다. Azure Network Watcher는 Azure 배포에 대 한 연결 관련 메트릭을 모니터링 하 고, 진단 하 고, 볼 수 있는 도구를 제공 합니다.

주요 사용 사례:

- 다중 계층 응용 프로그램에서 데이터베이스 서버 VM과 통신 하는 프런트 엔드 웹 서버 VM이 있습니다. 두 Vm 간의 네트워크 연결을 확인 하려고 합니다.
- 미국 동부 지역의 Vm에서 미국 중부 지역의 Vm을 ping 하 고 지역 간 네트워크 대기 시간을 비교 하려고 합니다.
- 시애틀과 같은 도시에 여러 온-프레미스 사무실 사이트가 있습니다. Office 365 Url에 연결 하는 중입니다. 시애틀 및 Ashburn의 Office 365 Url을 사용 하 여 사용자가 경험 하는 대기 시간을 비교 하려고 합니다.
- Azure Storage 끝점에 연결 해야 하는 하이브리드 응용 프로그램을 설정 했습니다. 동일한 Azure Storage 끝점에 연결 하는 동시에 온-프레미스 사이트와 Azure 응용 프로그램 간의 대기 시간을 비교 하려고 합니다.
- 클라우드 응용 프로그램을 호스트 하는 Azure Vm에서 온-프레미스 설정으로의 연결을 확인 하려고 합니다.

이 미리 보기 단계에서 솔루션은 Network Watcher의 [연결 모니터](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) 및 네트워크 성능 모니터 (NPM) [서비스 연결 모니터](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity)의 두 가지 주요 기능을 함께 제공 합니다.

하이라이트가

* Azure 및 하이브리드 모니터링 요구 사항에 대 한 통합 된 직관적인 환경
* 영역 간, 교차 작업 영역 연결 모니터링
* 검색 빈도가 높고 네트워크 성능에 대 한 가시성 향상
* 하이브리드 배포에 대 한 더 빠른 경고
* HTTP, TCP 및 ICMP 기반 연결 확인 지원
* Azure 및 비 Azure 테스트에 대 한 메트릭 및 Log Analytics 지원

![연결 모니터](./media/connection-monitor-2-preview/hero-graphic.png)

아래에 설명 된 단계에 따라 연결 모니터 (미리 보기)를 사용 하 여 모니터링을 시작 합니다.

## <a name="step-1-install-monitoring-agents"></a>1 단계: 모니터링 에이전트 설치

연결 모니터는 경량 실행 파일을 사용 하 여 연결 확인을 실행 합니다.  Azure 및 온-프레미스 환경에서 연결 확인을 지원 합니다. 사용할 특정 실행 파일은 VM이 Azure 또는 온-프레미스에서 호스트 되는지에 따라 달라 집니다.

### <a name="agents-for-azure-virtual-machines"></a>Azure virtual machines 용 에이전트

연결 모니터가 Azure Vm을 모니터링 원본으로 인식 하도록 하려면 Network Watcher 에이전트 가상 컴퓨터 확장 (Network Watcher 확장명이 라고도 함)을 설치 해야 합니다. Network Watcher 에이전트 확장은 Azure virtual machines에서 종단 간 모니터링 및 기타 고급 기능을 트리거하기 위한 요구 사항입니다. [VM을 만들고 Network Watcher 확장](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)[을](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)설치할 수 있습니다.  또한 [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) 및 [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows) 용 Network Watcher 확장을 별도로 설치, 구성 및 문제 해결할 수 있습니다.

NSG 또는 방화벽 규칙이 원본 및 대상 간의 통신을 차단 하는 경우 연결 모니터는 문제를 감지 하 고 토폴로지에 진단 메시지로 표시 합니다. 연결 모니터링을 사용 하도록 설정 하려면 NSG 및 방화벽 규칙이 원본 및 대상 간에 TCP 또는 ICMP를 통한 패킷을 허용 하는지 확인 합니다.

### <a name="agents-for-on-premise-machines"></a>온-프레미스 컴퓨터에 대 한 에이전트

연결 모니터에서 온-프레미스 컴퓨터를 모니터링 원본으로 인식 하려면 컴퓨터에 Log Analytics 에이전트를 설치 하 고 네트워크 성능 모니터링 솔루션을 사용 하도록 설정 해야 합니다. 이러한 에이전트는 Log Analytics 작업 영역에 연결 되며, 모니터링을 시작 하기 전에 작업 영역 ID 및 기본 키를 설정 해야 합니다.

Windows 컴퓨터용 Log Analytics 에이전트를 설치 하려면 [이 링크](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows) 에 설명 된 지침을 따르세요.

경로에 방화벽이 나 NVA (가상 네트워크 어플라이언스)가 있는 경우 대상에 연결할 수 있는지 확인 합니다.

## <a name="step-2-enable-network-watcher-on-your-subscription"></a>2 단계: 구독에서 Network Watcher 사용

VNET을 사용 하는 모든 구독은 Network Watcher 사용 하도록 설정 됩니다. 구독에서 가상 네트워크를 만들 때 해당 Virtual Network의 지역 및 구독에서 Network Watcher 자동으로 사용 하도록 설정 됩니다. Network Watcher를 자동으로 사용 하도록 설정 하는 경우 리소스에 영향을 주지 않습니다. Network Watcher 구독에서 명시적으로 사용 하지 않도록 설정 되어 있는지 확인 합니다. 자세한 내용은 [Network Watcher 사용](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)을 참조 하세요.

## <a name="step-3-create-connection-monitor"></a>3 단계: 연결 모니터 만들기 

_연결 모니터_ 는 일정 한 간격으로 통신을 모니터링 하 고 원본 에이전트와 대상 끝점 간의 연결 가능성, 대기 시간 및 네트워크 토폴로지 변경 내용을 알려줍니다. 소스는 모니터링 에이전트가 설치 된 Azure Vm 또는 온-프레미스 컴퓨터 일 수 있습니다. 대상 끝점은 Office 365 Url, Dynamics 365 Url, 사용자 지정 Url, Azure VM 리소스 Id, IPv4, IPv6, FQDN 또는 임의의 도메인 이름일 수 있습니다.

### <a name="accessing-connection-monitor-preview"></a>연결 모니터 액세스 (미리 보기)

1. Azure Portal 홈 페이지에서 Network Watcher을 방문 하세요.
2. Network Watcher의 왼쪽 창에 있는 모니터링 섹션에서 "연결 모니터 (미리 보기)" 탭을 클릭 합니다.
3. 연결 모니터 (미리 보기) 환경을 사용 하 여 만든 모든 연결 모니터를 볼 수 있습니다. 연결 모니터 탭의 클래식 환경을 사용 하 여 만든 모든 연결 모니터는 연결 모니터 탭에서 볼 수 있습니다.

    ![연결 모니터 만들기](./media/connection-monitor-2-preview/cm-resource-view.png)


### <a name="creating-a-connection-monitor"></a>연결 모니터 만들기

연결 모니터 (미리 보기)를 사용 하 여 만든 연결 모니터는 온-프레미스 및 Azure Vm을 원본으로 추가 하 고 끝점에 대 한 연결을 모니터링 하는 기능을 제공 합니다 .이를 통해 Azure 또는 다른 모든 URL/IP 범위에서

연결 모니터의 엔터티는 다음과 같습니다.

* 연결 모니터 리소스-지역 특정 Azure 리소스입니다. 아래에 언급 된 모든 엔터티는 연결 모니터 리소스의 속성입니다.
* 끝점-연결 확인에 참여 하는 모든 원본 및 대상은 끝점으로 호출 됩니다. 끝점의 예-Azure Vm, 온-프레미스 에이전트, Url, Ip
* 테스트 구성 – 각 테스트 구성은 프로토콜 마다 다릅니다. 선택한 프로토콜에 따라 포트, 임계값, 테스트 빈도 및 기타 매개 변수를 정의할 수 있습니다.
* 테스트 그룹 – 각 테스트 그룹에는 소스 끝점, 대상 끝점 및 테스트 구성이 포함 됩니다. 각 연결 모니터에는 두 개 이상의 테스트 그룹이 포함 될 수 있습니다.
* 테스트 – 소스 끝점, 대상 끝점 및 테스트 구성의 조합으로 하나의 테스트를 수행 합니다. 테스트는 모니터링 데이터 (% 및 RTT 검사 실패)를 사용할 수 있는 가장 낮은 수준입니다.

 ![연결 모니터 만들기](./media/connection-monitor-2-preview/cm-tg-2.png)

#### <a name="from-portal"></a>포털에서

연결 모니터를 만들려면 아래에 설명 된 단계를 수행 합니다.

1. 연결 모니터 (미리 보기) 대시보드에서 왼쪽 위 모서리에서 "만들기"를 클릭 합니다.
2. 기본 탭에서 연결 모니터에 대 한 정보를 입력 합니다.
   1. 연결 모니터 이름 – 연결 모니터의 이름입니다. Azure 리소스에 대 한 표준 명명 규칙이 여기에 적용 됩니다.
   2. 구독 – 연결 모니터에 대 한 구독을 선택 합니다.
   3. 지역 – 연결 모니터 리소스에 사용할 지역을 선택 합니다. 이 지역에 생성 된 원본 Vm만 선택할 수 있습니다.
   4. 작업 영역 구성-기본 확인란을 클릭 하 여 연결 모니터에서 만든 기본 작업 영역을 사용 하 여 모니터링 데이터를 저장할 수 있습니다. 사용자 지정 작업 영역을 선택 하려면이 확인란의 선택을 취소 합니다. 구독 및 지역을 선택 하 여 모니터링 데이터를 저장할 작업 영역을 선택 합니다.
   5. "다음: 테스트 그룹"을 클릭 하 여 테스트 그룹을 추가 합니다.

      ![연결 모니터 만들기](./media/connection-monitor-2-preview/create-cm-basics.png)

3. 테스트 그룹 탭에서 "+ 테스트 그룹"을 클릭 하 여 테스트 그룹을 추가 합니다. _연결 모니터에서 테스트 그룹 만들기_ 를 사용 하 여 테스트 그룹을 추가 합니다. "검토 + 만들기"를 클릭 하 여 연결 모니터를 검토 합니다.

   ![연결 모니터 만들기](./media/connection-monitor-2-preview/create-tg.png)

4. "검토 + 만들기" 탭에서 연결 모니터를 만들기 전에 기본 정보 및 테스트 그룹을 검토 합니다. "검토 + 만들기" 보기에서 연결 모니터를 편집 하려면 다음을 수행 합니다.
   1. 기본 세부 정보를 편집 하려면 이미지 2의 상자 1에 지정 된 대로 연필 아이콘을 사용 합니다.
   2. 개별 테스트 그룹을 편집 하려면 편집할 테스트 그룹을 클릭 하 여 편집 모드에서 테스트 그룹을 엽니다.
   3. 현재 비용/월에는 미리 보기 중의 비용이 표시 됩니다. 현재 연결 모니터 사용에 대 한 요금이 청구 되지 않으므로이 열에는 0이 표시 됩니다. 실제 비용/월은 일반 공급 이후 요금이 부과 되는 가격을 나타냅니다. 참고로, log analytics 수집 요금은 미리 보기 중에도 적용 됩니다.

5. "검토 + 만들기" 탭에서 "만들기" 단추를 클릭 하 여 연결 모니터를 만듭니다.

   ![연결 모니터 만들기](./media/connection-monitor-2-preview/review-create-cm.png)

6.  연결 모니터 (미리 보기)는 백그라운드에서 연결 모니터 리소스를 만듭니다.

#### <a name="from-armclient"></a>Armclient에서

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "[https://](https://apac01.safelinks.protection.outlook.com/?url=https%3A%2F%2Fbrazilus.management.azure.com&amp;data=02%7C01%7CManasi.Sant%40microsoft.com%7Cd900da4ed7f24366842108d68022159b%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636837281231186904&amp;sdata=qHL8zWjkobY9MatRpAVbODwboKSQAqqEFOMnjmfyOnU%3D&amp;reserved=0)management.azure.com"

$SUB = "subscriptions/\&lt;subscription id 1\&gt;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_centraluseuap"

$body =

"{

location: 'eastus',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourcegroups/\&lt;resource group\&gt;/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '\&lt;FQDN of your on-premise agent'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

},

 {

name: 'vm2',

resourceId: '/subscriptions/\&lt;subscription id\&gt;/resourceGroups/\&lt;resource group\&gt;/providers/Microsoft.Compute/virtualMachines/\&lt;vm-name\&gt;'

   },

{

name: 'azure portal'

address: '\&lt;URL\&gt;'

   },

 {

    name: 'ip',

     address: '\&lt;IP\&gt;'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: 60,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'https',

    testFrequencySec: 60,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: 30,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: 90,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: 120,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 50,

     roundTripTimeMs: 3.4

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: 45,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: 30,

     roundTripTimeMs: 5.2

    }

   }

  ]

 }

} "
```

배포 명령:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

### <a name="creating-test-groups-in-connection-monitor"></a>연결 모니터에서 테스트 그룹 만들기

연결 모니터의 각 테스트 그룹에는 테스트 구성에 대 한 실패 및 RTT를 확인 하는 네트워크 매개 변수에서 테스트 되는 원본 및 대상이 포함 됩니다.

#### <a name="from-portal"></a>포털에서

연결 모니터에서 테스트 그룹을 만들려면 아래 언급 된 필드의 값을 지정 합니다.

1. 테스트 그룹 사용 안 함 –이 필드를 선택 하면 테스트 그룹에 지정 된 모든 원본 및 대상에 대해 모니터링을 사용 하지 않도록 설정 됩니다. 이 옵션은 기본적으로 선택 취소 되어 있습니다.
2. 이름 – 테스트 그룹의 이름
3. 원본-에이전트가 설치 되어 있는 경우 Azure Vm과 온-프레미스 컴퓨터를 모두 원본으로 지정할 수 있습니다. 원본에 해당 하는 에이전트를 설치 하려면 1 단계를 참조 하세요.
   1. "Azure 에이전트" 탭을 클릭 하 여 Azure 에이전트를 선택 합니다. 연결 모니터를 만들 때 지정한 지역에 바인딩된 Vm만 표시 됩니다. Vm은 기본적으로 속해 있는 구독으로 그룹화 되 고 그룹은 축소 됩니다. 구독 수준에서 계층의 다른 수준으로 드릴 다운할 수 있습니다.

      ```Subscription -\&gt; resource groups -\&gt; VNETs -\&gt; Subnets -\&gt; VMs with agents Y```

      "그룹화 기준" 필드의 값을 변경 하 여 다른 수준에서 트리를 시작할 수도 있습니다. 예: Group by – VNET은 Vnet-\&gt; 계층의 에이전트가 있는 Vm을 표시 합니다. 서브넷-\&gt; 에이전트를 사용 하는 Vm.

      ![소스 추가](./media/connection-monitor-2-preview/add-azure-sources.png)

   2. "비 Azure 에이전트" 탭을 클릭 하 여 온-프레미스 에이전트를 선택 합니다. 기본적으로 지역에서 작업 영역으로 그룹화 된 에이전트가 표시 됩니다. 네트워크 성능 모니터 솔루션이 구성 된 작업 영역만 나열 됩니다. [Azure 마켓플레이스](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview)에서 작업 영역에 네트워크 성능 모니터 솔루션을 추가합니다. [솔루션 갤러리에서 Azure Monitor 솔루션 추가](https://docs.microsoft.com/azure/azure-monitor/insights/solutions) 에 설명 된 프로세스를 사용할 수도 있습니다. 기본적으로 연결 모니터 만들기 보기의 기본 정보 탭에서 선택한 지역이 표시 됩니다. 영역을 변경 하 고 새로 선택한 지역에서 작업 영역에 있는 에이전트를 선택할 수 있습니다. "그룹화 기준" 필드의 값을 서브넷의 그룹으로 변경할 수도 있습니다.

      ![비 Azure 소스](./media/connection-monitor-2-preview/add-non-azure-sources.png)


   3. "검토"를 클릭 하 여 선택한 Azure 및 비 Azure 에이전트를 검토 합니다.

      ![원본 검토](./media/connection-monitor-2-preview/review-sources.png)

   4. 원본 선택을 완료 한 후 "완료"를 클릭 합니다.

4. 대상 – 대상으로 지정 하 여 Azure Vm 또는 끝점 (공용 IP, URL, FQDN)에 대 한 연결을 모니터링할 수 있습니다. 단일 테스트 그룹에서 Azure Vm, O365 Url, D365 Url 또는 사용자 지정 끝점을 추가할 수 있습니다.

   1. "Azure Vm" 탭을 클릭 하 여 대상으로 Azure Vm을 선택 합니다. 기본적으로 연결 모니터 만들기 보기의 기본 정보 탭에서 선택한 것과 동일한 지역에서 구독 계층으로 그룹화 된 Azure Vm이 표시 됩니다. 영역을 변경 하 고 새로 선택한 지역에서 Azure Vm을 선택할 수 있습니다. 구독 수준에서 계층의 다른 수준 (예: Azure 에이전트)으로 드릴 다운할 수 있습니다.

      ![대상 추가](./media/connection-monitor-2-preview/add-azure-dests1.png)<br>

      ![대상 추가 2](./media/connection-monitor-2-preview/add-azure-dests2.png)

   2. "끝점" 탭을 클릭 하 여 끝점을 대상으로 선택 합니다. 끝점 목록은 이름으로 그룹화 된 O365 및 D365 테스트 Url로 채워집니다.  동일한 연결 모니터의 다른 테스트 그룹에서 만든 끝점을 선택할 수도 있습니다. 새 끝점을 추가 하려면 화면의 오른쪽 위 모서리에서 "+ 끝점"을 클릭 하 고 끝점 URL/i p/FQDN 및 이름을 제공 합니다.

      ![끝점 추가](./media/connection-monitor-2-preview/add-endpoints.png)

   3. "검토"를 클릭 하 여 선택한 Azure 및 비 Azure 에이전트를 검토 합니다.
   4. 원본 선택을 완료 한 후 "완료"를 클릭 합니다.

5. 테스트 구성 – 지정 된 테스트 그룹에서 원하는 수의 테스트 구성을 연결할 수 있습니다. 포털은 테스트 그룹당 하나의 테스트 구성으로 제한 하지만 Armclient을 사용 하 여 추가 합니다.
   1. 이름 – 테스트 구성의 이름입니다.
   2. 프로토콜 – TCP, ICMP 또는 HTTP 중에서 선택할 수 있습니다. HTTP를 HTTPS로 변경 하려면 HTTP as protocol 및 443 as port를 선택 합니다.
   3. 네트워크 테스트 구성 만들기 – 프로토콜 필드에서 HTTP를 선택 하는 경우에만이 확인란이 표시 됩니다. 이 필드를 사용 하 여 3 단계 및 4 단계에서 지정한 것과 동일한 원본 및 대상을 사용 하 여 다른 테스트 구성을 만들고 TCP/ICMP 프로토콜을 사용 합니다. 새로 만든 테스트 구성의 이름은 5로 지정 된 "\&l t; 이름으로 지정 됩니다.\&gt;\_networkTestConfig "
   4. 경로 추적 사용 안 함 –이 필드는 TCP 또는 ICMP를 프로토콜로 사용 하는 테스트 그룹에 적용 됩니다.  원본 토폴로지 및 홉 단위 왕복 시간을 검색 하지 않으려면이 필드를 선택 합니다.
   5. 대상 포트 –이 필드를 사용자 지정 하 여 원하는 대상 포트에 넣을 수 있습니다.
   6. 테스트 빈도-이 필드는 원본에서 위에 지정 된 프로토콜 및 포트에 대 한 대상을 ping 하는 빈도를 결정 합니다. 30 초, 1 분, 5 분, 15 분, 30 분 중에서 선택할 수 있습니다. 원본은 선택한 값을 기준으로 대상에 대 한 연결을 테스트 합니다.  예를 들어 30 초를 선택 하는 경우 원본은 30 초 이내에 대상에 대 한 연결을 확인 합니다.
   7. 상태 임계값 – 아래에 언급 된 네트워크 매개 변수에 대 한 임계값을 설정할 수 있습니다.
      1. 원본에서 위에 지정 된 조건에 따라 대상에 대 한 연결을 확인 하는 경우%-검사 의%-% 비율 검사에 실패 했습니다. TCP/ICMP 프로토콜의 경우%에서 실패 확인이 패킷 손실에 동일시 수 있습니다. HTTP 프로토콜의 경우이 필드는 응답을 받지 못한 http 요청 수를 나타냅니다.
      2. RTT (밀리초) – 소스가 위에 지정 된 테스트 구성을 통해 대상에 연결 되는 왕복 시간 (밀리초)입니다.

      ![TG 추가](./media/connection-monitor-2-preview/add-test-config.png)

지정 된 테스트 구성을 사용 하 여 테스트 그룹에 추가 된 모든 원본 및 대상은 개별 테스트로 분류 됩니다. 다음은 그 예입니다.

* 테스트 그룹: TG1
* 원본: 3 (A, B, C)
* 대상: 2 (D, E)
* 테스트 구성: 2 (구성 1, 구성 2)
* 만든 테스트: Total = 12

| **테스트 번호** | **원본** | **대상** | **구성 이름 테스트** |
| --- | --- | --- | --- |
| 1 | A | D | 구성 1 |
| 2 | A | D | 구성 2 |
| 3 | A | E | 구성 1 |
| 4 | A | E | 구성 2 |
| 5 | b | D | 구성 1 |
| 6 | b | D | 구성 2 |
| 7 | b | E | 구성 1 |
| 8 | b | E | 구성 2 |
| 9 | C | D | 구성 1 |
| 10 | C | D | 구성 2 |
| 11 | C | E | 구성 1 |
| 12 | C | E | 구성 2 |

### <a name="scale-limits"></a>크기 제한

* 지역별 구독 당 최대 연결 모니터 수-100
* 연결 모니터 당 최대 테스트 그룹 수-20
* 최대 소스 수 + 연결 모니터 당 대상 수-100
* 연결 모니터 당 최대 테스트 구성 수 – 20 via Armclient. 2-포털을 통해

## <a name="step-4--data-analysis-and-alerts"></a>4 단계: 데이터 분석 및 경고

연결 모니터를 만든 후에는 소스에서 지정 된 테스트 구성에 따라 대상에 대 한 연결을 확인 합니다.

### <a name="checks-in-a-test"></a>테스트 체크 인

테스트 구성에서 사용자가 선택한 프로토콜을 기준으로 연결 모니터 (미리 보기)는 선택한 테스트 빈도로 원본 대상 쌍에 대 한 일련의 검사를 실행 합니다.

HTTP를 선택 하면 서비스에서 응답 코드를 반환 하는 HTTP 응답 수를 계산 하 여 실패 한 검사를 확인 합니다 .%  RTT를 계산 하기 위해 HTTP 호출의 응답을 수신 하는 데 걸린 시간을 측정 합니다.

TCP 또는 ICMP를 선택 하면 서비스에서 패킷%를 계산 하 여 검사 실패%를 확인 합니다. RTT를 계산 하기 위해 전송 된 패킷에 대해 ACK를 수신 하는 데 걸린 시간을 측정 합니다. 네트워크 테스트에 대해 경로 추적 데이터를 사용 하도록 설정한 경우 온-프레미스 네트워크에 대 한 홉 단위 손실 및 대기 시간을 확인할 수 있습니다.

### <a name="states-of-a-test"></a>테스트 상태

테스트에서 검사에 의해 반환 되는 데이터를 기반으로 각 테스트에는 다음과 같은 상태가 있을 수 있습니다.

* Pass = 검사에 대 한 실제 값이 실패 한 경우% 및 RTT가 지정 된 임계값 내에 있습니다.
* Fail =의 실제 값이 실패 한 경우% 또는 RTT 교차 지정 임계값을 확인 합니다. 임계값을 지정 하지 않으면 검사가 실패 한 경우 테스트가 실패로 표시 됩니다 .% = 100%
* 경고-확인에 대 한 조건을 지정 하지 않은 경우%가 지정 되지 않습니다. 이러한 경우 연결 모니터 (미리 보기)는 자동 집합 조건을 임계값으로 사용 하 고, 해당 임계값을 위반 하면 테스트의 상태가 "경고"로 설정 됩니다.

### <a name="data-collection-analysis-and-alerts"></a>데이터 수집, 분석 및 경고

연결 모니터 (미리 보기)에 의해 수집 된 모든 데이터는 연결 모니터를 만들 때 구성 된 Log Analytics 작업 영역에 저장 됩니다. 모니터링 데이터는 Azure Monitor 메트릭에도 제공 됩니다. Log Analytics를 사용 하 여 원하는 기간 동안 모니터링 데이터를 유지할 수 있지만, 기본적으로 30 일 동안 메트릭을 저장 Azure Monitor **.** 그런 다음 [데이터에 대 한 메트릭 기반 경고를 설정할](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/)수 있습니다.

#### <a name="monitoring-dashboards-in-connection-monitor-solution"></a>연결 모니터 솔루션의 모니터링 대시보드

지정 된 구독, 지역, 타임 스탬프, 원본 및 대상 유형 선택에 대 한 액세스 권한이 있는 연결 모니터의 목록이 표시 됩니다.

Network Watcher 서비스에서 연결 모니터 (미리 보기)로 이동 하는 경우 다음을 **통해 보도록**선택할 수 있습니다.

* 연결 모니터 (기본값)-선택한 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 대해 생성 된 모든 연결 모니터의 목록입니다.
* 테스트 그룹 – 선택한 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 대해 만들어진 모든 테스트 그룹의 목록입니다. 이러한 테스트 그룹은 연결 모니터에서 필터링 되지 않습니다.
* 테스트-선택한 구독, 지역, 타임 스탬프, 원본 및 대상 유형에 대해 실행 되는 모든 테스트의 목록입니다. 이러한 테스트는 연결 모니터 또는 테스트 그룹에서 필터링 되지 않습니다.

각 연결 모니터를 테스트 그룹으로 확장 하 고 각 테스트 그룹을 대시보드 내에서 실행 되는 다양 한 개별 테스트로 확장할 수 있습니다. 아래 이미지에서 [1]로 표시 됩니다.

다음을 기준으로이 목록을 필터링 할 수 있습니다.

* 최상위 필터-구독, 지역, 타임 스탬프 원본 및 대상 유형입니다. 아래 이미지에 [2]로 표시 됩니다.
* 상태 기반 필터-연결 모니터/테스트 그룹/테스트 상태에 대 한 두 번째 수준 필터입니다. 아래 이미지에 [3]로 표시 됩니다.
* 검색 필드 – "모두"를 선택 하 여 일반 검색을 수행 합니다. 특정 엔터티를 검색 하려면 드롭다운을 사용 하 여 검색 결과의 범위를 좁힙니다. 아래 이미지에서 [4]로 표시 됩니다.

![테스트 필터링](./media/connection-monitor-2-preview/cm-view.png)

다음은 그 예입니다.

1. 모든 연결 모니터 (미리 보기)에서 원본 IP = 10.192.64.56에 대 한 모든 테스트를 보려면 다음을 수행 합니다.
   1. 뷰를 "테스트"로 변경 합니다.
   2. 검색 필드 = 10.192.64.56
   3. 값 옆의 드롭다운을 사용 하 여 "원본"을 선택 합니다.
2. 원본 IP = 10.192.64.56 인 모든 연결 모니터 (미리 보기)에서 실패 한 테스트만 필터링 하려면
   1. 뷰를 "테스트"로 변경 합니다.
   2. 상태 기반 필터에서 "실패"를 선택 합니다.
   3. 검색 필드 = 10.192.64.56
   4. 값 옆의 드롭다운을 사용 하 여 "원본"을 선택 합니다.
3. Destination이 outlook.office365.com 모든 연결 모니터 (미리 보기)에서 실패 한 테스트만 필터링 하려면
   1. 뷰를 "테스트"로 변경 합니다.
   2. 상태 기반 필터에서 "실패"를 선택 합니다.
   3. 검색 필드 = outlook.office365.com
   4. 값 옆에 있는 드롭다운을 사용 하 여 "대상"을 선택 합니다.

   ![실패한 테스트](./media/connection-monitor-2-preview/tests-view.png)

실패 한 검사 추세를 보려면 다음을 수행 하십시오 .% 및 RTT:

1. 연결 모니터
   1. 자세히 조사 하려는 연결 모니터를 클릭 합니다.
   2. 기본적으로 "테스트 그룹" 별로 모니터링 데이터를 봅니다.

      ![메트릭 보기 기준](./media/connection-monitor-2-preview/cm-drill-landing.png)

   3. 자세히 조사할 테스트 그룹 선택

      ![TG 별 메트릭](./media/connection-monitor-2-preview/cm-drill-select-tg.png)

   4. 이전 단계에서 선택한 테스트 그룹의 실패 한% 또는 RTT 밀리초 검사에 대해 실패 한 테스트 상위 5 개를 확인할 수 있습니다. 각 테스트에 대해 실패 한% 및 RTT 밀리초 확인에 대 한 추세선이 표시 됩니다.
   5. 위의 목록에서 테스트를 선택 하거나, 다른 테스트를 선택 하 여 자세히 조사 하세요.
   6. 선택한 시간 간격 동안 검사 실패%에 대해 임계값 및 실제 값이 표시 됩니다. RTT 밀리초의 경우 임계값, 평균, 최소값 및 최대값을 확인할 수 있습니다.

      ![RTT](./media/connection-monitor-2-preview/cm-drill-charts.png)

  7. 시간 간격을 변경 하 여 더 많은 데이터 보기
  8. B 단계에서 보기를 변경 하 고 소스, 대상 또는 테스트 구성을 기준으로 보기를 선택할 수 있습니다. 그런 다음 실패 한 테스트에 따라 소스를 선택 하 고 실패 한 상위 5 개 테스트를 조사 합니다.  예: 선택한 연결 모니터의 해당 조합 사이에서 실행 되는 모든 테스트를 조사 하려면 보기 기준: 원본 및 대상을 선택 합니다.

      ![RTT2](./media/connection-monitor-2-preview/cm-drill-select-source.png)

2. 테스트 그룹
   1. 자세히 조사할 테스트 그룹을 클릭 합니다.
   2. 기본적으로 "원본 + 대상 + 테스트 구성 (테스트)"을 기준으로 모니터링 데이터를 봅니다.

      ![RTT3](./media/connection-monitor-2-preview/tg-drill.png)

   3. 자세히 조사 하려는 테스트 선택
   4. 선택한 시간 간격 동안 검사 실패%에 대해 임계값 및 실제 값이 표시 됩니다. RTT 밀리초의 경우 임계값, 평균, 최소값 및 최대값을 확인할 수 있습니다. 또한 선택한 테스트와 관련 된 경고가 발생 한 것을 볼 수 있습니다.
   5. 시간 간격을 변경 하 여 더 많은 데이터 보기
   6. B 단계에서 보기를 변경 하 고 소스, 대상 또는 테스트 구성을 기준으로 보기를 선택할 수 있습니다. 그런 다음 엔터티를 선택 하 여 실패 한 상위 5 개 테스트를 조사 합니다.  예: 선택한 연결 모니터의 해당 조합 사이에서 실행 되는 모든 테스트를 조사 하려면 보기 기준: 원본 및 대상을 선택 합니다.

3. 테스트
   1. 자세히 조사 하려는 원본 + 대상 + 테스트 구성을 클릭 합니다.
   2. 선택한 시간 간격 동안 검사 실패%에 대해 임계값 및 실제 값이 표시 됩니다. RTT 밀리초의 경우 임계값, 평균, 최소값 및 최대값을 확인할 수 있습니다. 또한 선택한 테스트와 관련 된 경고가 발생 한 것을 볼 수 있습니다.

      ![Test1](./media/connection-monitor-2-preview/test-drill.png)

   3. "토폴로지"를 클릭 하 여 언제 든 지 네트워크 토폴로지를 볼 수도 있습니다.

      ![Test2](./media/connection-monitor-2-preview/test-topo.png)

   4. Azure 네트워크에 대 한 링크의 모든 홉을 클릭 하 여 연결 모니터에서 식별 한 문제를 볼 수 있습니다. 이 기능은 현재 온-프레미스 네트워크에서 사용할 수 없습니다.

       ![Test3](./media/connection-monitor-2-preview/test-topo-hop.png)

#### <a name="log-queries-in-azure-monitor-log-analytics"></a>Azure Monitor Log Analytics의 로그 쿼리

Log Analytics를 사용 하 여 모니터링 데이터의 사용자 지정 보기를 만들 수 있습니다. UI에 표시 되는 모든 데이터는 Log Analytics에서 채워집니다. 리포지토리의 데이터에 대 한 대화형 분석을 수행 하 고 에이전트 상태 및 기타 Log Analytics 기반 응용 프로그램과 같은 여러 소스의 데이터를 상호 연결할 수 있습니다. 또한 Excel, Power BI 또는 공유할 수 있는 링크로 데이터를 내보낼 수 있습니다.

#### <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

연결 모니터 (미리 보기) 환경 전에 만들어진 연결 모니터의 경우 4 개의 메트릭을 모두 사용할 수 있습니다. 연결 모니터 (미리 보기) 환경을 통해 생성 된 연결 모니터의 경우 데이터는 "(미리 보기)"로 태그가 지정 된 메트릭에 대해서만 사용할 수 있습니다.

리소스 종류-Microsoft. Network/networkWatchers/connectionMonitors

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 형식 | Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | 실패한 프로브 % | 백분율 | 평균 | 실패한 연결 모니터링 프로브 % | 차원 없음 |
| AverageRoundtripMs | 평균 왕복 시간 (밀리초) | 밀리초 | 평균 | 원본과 대상 간에 전송된 연결 모니터링 프로브의 평균 네트워크 왕복 시간(ms) |             차원 없음 |
| ChecksFailedPercent (미리 보기) | % 검사 실패 (미리 보기) | 백분율 | 평균 | 테스트에 대 한% 확인 실패 | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * Sourceresourceid 여야 <br> * SourceType <br> * 프로토콜 <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * 지역 |
| RoundTripTimeMs (미리 보기) | 왕복 시간 (ms) (미리 보기) | 밀리초 | 평균 | 원본 및 대상 간에 전송 되는 검사에 대 한 왕복 시간 (ms)입니다. 이 값은 평균이 아닙니다. | * ConnectionMonitorResourceId <br> * SourceAddress <br> * SourceName <br> * Sourceresourceid 여야 <br> * SourceType <br> * 프로토콜 <br> * DestinationAddress <br> * DestinationName <br> * DestinationResourceId <br> * DestinationType <br> * DestinationPort <br> * TestGroupName <br> * TestConfigurationName <br> * 지역 |

 ![메트릭 모니터링](./media/connection-monitor-2-preview/monitor-metrics.png)

#### <a name="metric-alerts-in-azure-monitor"></a>Azure Monitor에서 메트릭 경고

경고를 만들려면:

1. 연결 모니터 (미리 보기)를 사용 하 여 만든 연결 모니터 리소스 선택
2. 이전 단계에서 선택한 리소스에 대 한 신호 유형으로 "메트릭"이 표시 되는지 확인 합니다.
3. 조건 추가에서 신호 이름을 ChecksFailedPercent (Preview) 또는 RoundTripTimeMs (미리 보기)로, 신호 유형을 메트릭으로 선택 합니다. 예: ChecksFailedPercent (미리 보기) 선택
4. 메트릭 별로 적용 가능한 모든 차원이 나열 됩니다.  차원 이름 및 차원 값을 선택 합니다. 예: 원본 주소를 선택 하 고 1 단계에서 선택한 연결 모니터 리소스에 포함 된 원본에 대 한 IP 주소를 제공 합니다.
5. 경고 논리에서 다음을 선택 합니다.
   1. 조건 형식 – 정적
   2. 조건 및 임계값
   3. 집계 세분성 및 평가 빈도 – 연결 모니터 (미리 보기)는 1 분 마다 데이터를 업데이트 합니다.
6.  작업에서 작업 그룹을 선택 합니다.
7. 경고 세부 정보 제공
8. 경고 규칙 만들기

   ![경고](./media/connection-monitor-2-preview/mdm-alerts.jpg)

## <a name="step-5-diagnose-issues-in-your-network"></a>5 단계: 네트워크에서 문제 진단

연결 모니터는 연결 모니터 리소스 및 네트워크에 해당 하는 문제를 진단 하는 데 도움이 됩니다. 하이브리드 네트워크의 문제는 1 단계에서 설치한 Log Analytics 에이전트가 검색 하 고 Azure의 문제는 Network Watcher 확장에 의해 검색 됩니다.  하이브리드 네트워크의 문제는 진단 페이지에 표시 되며 Azure 네트워크의 문제는 네트워크 토폴로지에서 볼 수 있습니다.

원본으로 온-프레미스 Vm을 사용 하는 네트워크의 경우 다음을 검색 합니다.

* 요청 시간이 초과 되었습니다.
* 끝점이 DNS에서 확인 되지 않음-임시 또는 영구입니다. URL이 잘못 되었습니다.
* 호스트를 찾을 수 없습니다.
* 원본에서 대상에 연결할 수 없습니다. ICMP를 통해 대상에 연결할 수 없습니다.
* 인증서 관련 문제-에이전트를 인증 하는 데 필요한 클라이언트 인증서, 인증서 재배치 목록에 액세스할 수 없음, 끝점의 호스트 이름이 인증서의 주체 또는 주체 대체 이름, 원본 로컬 컴퓨터의 신뢰할 수 있는 인증 기관 저장소에 없는 루트 인증서, 만료 됨/잘못 됨/취소 됨, 호환 되지 않음

Azure Vm을 사용 하는 네트워크의 경우 다음을 검색 합니다.

* 에이전트 문제-에이전트가 중지 됨, DNS 확인 실패, 대상 포트에서 수신 대기 하는 응용 프로그램/수신기 없음, 소켓을 열 수 없음
* VM 상태 문제 – 시작, 중지, 중지, 할당 취소, 할당 취소, 다시 부팅, 할당 되지 않음
* ARP 테이블 항목이 없습니다.
* 로컬 방화벽 문제 (NSG 규칙)로 인해 트래픽이 차단 되었습니다.
* VNET 게이트웨이 – 누락 된 경로, 두 게이트웨이 간의 터널이 연결이 끊어졌거나, 터널에서 찾을 수 없거나 두 번째 게이트웨이가 누락 되었거나, 피어 링 정보를 찾을 수 없음
* MS Edge에 경로가 없습니다.
* 시스템 경로 또는 UDR로 인해 트래픽이 중지 됨
* BGP가 게이트웨이 연결에서 사용 하도록 설정 되지 않음
* Load Balancer에서 DIP 프로브
