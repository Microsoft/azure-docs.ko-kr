---
title: Azure 트래픽 분석 | Microsoft Docs
description: 트래픽 분석을 사용하여 Azure 네트워크 보안 그룹 흐름 로그를 분석하는 방법을 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2018
ms.author: damendo
ms.reviewer: vinigam
ms.openlocfilehash: a2a65c6fcca4a037408c6b7e780708623aebed2b
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212241"
---
# <a name="traffic-analytics"></a>트래픽 분석

트래픽 분석은 클라우드 네트워크의 사용자 및 애플리케이션 작업에 대한 가시성을 제공하는 클라우드 기반 솔루션입니다. 트래픽 분석은 Network Watcher NSG(네트워크 보안 그룹) 흐름 로그를 분석하여 Azure 클라우드 내 트래픽 흐름에 대한 정보를 제공합니다. 트래픽 분석을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- Azure 구독에서 이루어지는 네트워크 작업을 시각화하고 핫스폿을 식별합니다.
- 열린 포트, 인터넷 액세스를 시도하는 애플리케이션, 불량 네트워크에 연결하는 VM(가상 머신) 같은 정보를 통해 네트워크에 대한 위협을 식별하고 네트워크를 보호합니다.
- Azure 지역 및 인터넷의 트래픽 흐름 패턴을 파악하여 네트워크 성능 및 용량에 맞게 네트워크 배포를 최적화합니다.
- 네트워크에서 연결 실패의 원인이 되는 네트워크 구성 오류를 정확히 파악합니다.

> [!NOTE]
> 이제 트래픽 분석는 10 분의 더 높은 빈도로 NSG 흐름 로그 데이터 수집을 지원 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="why-traffic-analytics"></a>트래픽 분석이란?

보안, 규정 준수 및 성능이 저하되지 않도록 네트워크를 모니터링하고 관리하고 파악하기 위해 필요합니다. 네트워크를 보호하고 최적화하는 데 있어서 가장 중요한 것은 사용자가 자신의 정확히 아는 것입니다. 네트워크의 현재 상태, 연결 중인 사용자 및 연결되는 위치, 인터넷에 대해 열려 있는 포트, 예상되는 네트워크 동작, 비정상적인 네트워크 동작, 갑작스런 트래픽 증가에 대해 알아야 하는 경우가 종종 있습니다.

클라우드 네트워크는 온-프레미스 엔터프라이즈 네트워크와는 다르게, 네트워크 인터페이스로 들어가거나 나올 때 IP 네트워크 트래픽을 수집하는 기능을 제공하는 netflow 또는 그에 상응하는 프로토콜 케이블 라우터와 스위치가 있습니다. 트래픽 흐름 데이터를 분석하면 네트워크 트래픽 흐름 및 볼륨을 분석할 수 있습니다.

Azure 가상 네트워크에는 NSG 흐름 로그가 있으며, 이 로그는 개별 네트워크 인터페이스, VM 또는 서브넷에 연결된 네트워크 보안 그룹을 통과하는 송수신 IP 트래픽에 대한 정보를 제공합니다. 트래픽 분석은 원시 NSG 흐름 로그를 분석하고 보안, 토폴로지 및 지리 인텔리전스를 삽입하여 환경의 트래픽 흐름에 대한 정보를 제공합니다. 트래픽 분석에서는 가장 통신이 많은 호스트, 가장 통신이 많은 애플리케이션 프로토콜, 가장 대화가 많은 호스트 쌍, 허용된/차단된 트래픽, 인바운드/아웃바운드 트래픽, 열려 있는 인터넷 포트, 가장 제한이 많은 규칙, Azure 데이터 센터당 트래픽 분산, 가상 네트워크, 서브넷, 불량 네트워크 등과 같은 정보를 제공합니다.

## <a name="key-components"></a>핵심 구성 요소

- **NSG(네트워크 보안 그룹)** : Azure Virtual Network에 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷, 개별 VM(클래식) 또는 VM(Resource Manager)에 연결된 개별 NIC(네트워크 인터페이스)와 연결될 수 있습니다. 자세한 내용은 [네트워크 보안 그룹 개요](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조하세요.
- **NSG(네트워크 보안 그룹) 흐름 로그**: 네트워크 보안 그룹을 통한 송/수신 IP 트래픽에 대한 정보를 볼 수 있습니다. NSG 흐름 로그는 json 형식으로 작성되고 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP 주소, 원본/대상 포트, 프로토콜)를 보여줍니다. NSG 흐름 로그에 대한 자세한 내용은 [NSG 흐름 로그](network-watcher-nsg-flow-logging-overview.md)를 참조하세요.
- **Log Analytics**: 모니터링 데이터를 수집하고 중앙 리포지토리에 데이터를 저장하는 Azure 서비스입니다. 이 데이터에는 이벤트, 성능 데이터 또는 Azure API를 통해 제공되는 사용자 지정 데이터가 포함될 수 있습니다. 수집된 데이터는 경고, 분석 및 내보내기에 사용할 수 있습니다. 네트워크 성능 모니터 및 트래픽 분석과 같은 모니터링 응용 프로그램은 Azure Monitor 로그를 기반으로 사용 하 여 빌드됩니다. 자세한 내용은 [Azure Monitor 로그](../log-analytics/log-analytics-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조 하세요.
- **Log Analytics 작업 영역**: Azure 계정과 관련 된 데이터가 저장 되는 Azure Monitor 로그 인스턴스입니다. Log Analytics 작업 영역에 대 한 자세한 내용은 [Log Analytics 작업 영역 만들기](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조 하세요.
- **Network Watcher**: Azure에서 네트워크 시나리오 수준으로 상태를 모니터링하고 진단할 수 있는 지역 서비스입니다. Network Watcher에서 NSG 흐름 로그를 켜고 끌 수 있습니다. 자세한 내용은 [Network Watcher](network-watcher-monitoring-overview.md)를 참조하세요.

## <a name="how-traffic-analytics-works"></a>트래픽 분석의 작동 원리

트래픽 분석은 동일한 원본 IP 주소, 대상 IP 주소, 대상 포트 및 프로토콜 사이에서 공통적인 흐름을 집계하여 원시 NSG 흐름 로그를 검사하고 감소된 로그를 캡처합니다. 예를 들어 호스트 1(IP 주소: 10.10.10.10)은 포트(예: 80) 및 프로토콜(예: http)을 사용하여 1시간에 100회 호스트 2(IP 주소: 10.10.20.10)와 통신합니다. 축소된 로그에는 100개의 항목 대신 호스트 1과 호스트2가 포트 *80* 및 프로토콜 *HTTP*를 사용하여 1시간 동안 100회 통신했다는 항목 하나가 있습니다. 축소된 로그는 지리, 보안 및 토폴로지 정보를 통해 강화된 다음, Log Analytics 작업 영역에 저장됩니다. 다음 그림은 데이터 흐름을 보여줍니다.

![NSG 흐름 로그 처리의 데이터 흐름](./media/traffic-analytics/data-flow-for-nsg-flow-log-processing.png)

## <a name="supported-regions-nsg"></a>지원 되는 지역: NSG 

지원되는 다음 지역 중 하나에서 NSG용 트래픽 분석을 사용할 수 있습니다.

* 캐나다 중부
* 미국 중서부
* 미국 동부
* 미국 동부 2
* 미국 중북부
* 미국 중남부
* 미국 중부
* 미국 서부
* 미국 서부 2
* 프랑스 중부
* 서유럽
* 북유럽
* 브라질 남부
* 영국 서부
* 영국 남부
* 오스트레일리아 동부
* 오스트레일리아 남동부
* 동아시아
* 동남아시아
* 한국 중부
* 인도 중부
* 인도 남부
* 일본 동부 
* 일본 서부
* US Gov 버지니아
* 중국 동부 2

## <a name="supported-regions-log-analytics-workspaces"></a>지원 되는 지역: Log Analytics 작업 영역

Log Analytics 작업 영역이 다음 지역에 있어야 합니다.
* 캐나다 중부
* 미국 중서부
* 미국 동부
* 미국 동부 2
* 미국 중북부
* 미국 중남부
* 미국 중부
* 미국 서부
* 미국 서부 2
* 미국 중부
* 프랑스 중부
* 서유럽
* 북유럽
* 브라질 남부
* 영국 서부
* 영국 남부
* 오스트레일리아 동부
* 오스트레일리아 남동부
* 동아시아
* 동남아시아
* 한국 중부
* 인도 중부
* 일본 동부
* US Gov 버지니아
* 중국 동부 2

## <a name="prerequisites"></a>사전 요구 사항

### <a name="user-access-requirements"></a>사용자 액세스 요구 사항

계정은 다음 Azure [기본 제공 역할](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 중 하나의 구성원이어야 합니다.

|배포 모델   | 역할                   |
|---------          |---------               |
|리소스 관리자   | 소유자                  |
|                   | 참가자            |
|                   | 판독기                 |
|                   | 네트워크 참가자    |

계정이 기본 제공 역할 중 하나에 할당되지 않은 경우 구독 수준에서 다음 작업이 할당된 [사용자 지정 역할](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)에 할당되어야 합니다.

- "Microsoft.Network/applicationGateways/read"
- "Microsoft.Network/connections/read"
- "Microsoft.Network/loadBalancers/read"
- "Microsoft.Network/localNetworkGateways/read"
- "Microsoft.Network/networkInterfaces/read"
- "Microsoft.Network/networkSecurityGroups/read"
- "Microsoft.Network/publicIPAddresses/read"
- "Microsoft.Network/routeTables/read"
- "Microsoft.Network/virtualNetworkGateways/read"
- "Microsoft.Network/virtualNetworks/read"

사용자 액세스 권한을 확인하는 방법에 대한 정보는 [트래픽 분석 FAQ](traffic-analytics-faq.md)를 참조하세요.

### <a name="enable-network-watcher"></a>Network Watcher 사용

트래픽을 분석하려면 기존 network watcher가 있거나 트래픽을 분석할 NSG가 있는 각 지역에서 [network watcher를 사용하도록 설정](network-watcher-create.md)해야 합니다. [지원되는 지역](#supported-regions-nsg)에 호스트된 NSG에 대해 트래픽 분석을 사용하도록 설정할 수 있습니다.

### <a name="select-a-network-security-group"></a>네트워크 보안 그룹 선택

NSG 흐름 로깅을 활성화하려면 흐름을 기록할 네트워크 보안 그룹이 있어야 합니다. 네트워크 보안 그룹이 없는 경우 [네트워크 보안 그룹 만들기](../virtual-network/manage-network-security-group.md#create-a-network-security-group)를 참조하여 새로 만듭니다.

Azure Portal에서 **네트워크 감시자**로 이동한 다음, **nsg 흐름 로그**를 선택 합니다. 다음 그림과 같이 NSG 흐름 로그를 사용하도록 설정할 네트워크 보안 그룹을 선택합니다.

![NSG 흐름 로그를 사용하도록 설정해야 하는 NSG 선택](./media/traffic-analytics/selection-of-nsgs-that-require-enablement-of-nsg-flow-logging.png)

[지원되는 지역](#supported-regions-nsg)이 아닌 지역에 호스트되는 NSG에 대해 트래픽 분석을 설정하려고 시도하면 "찾을 수 없음" 오류가 발생합니다.

## <a name="enable-flow-log-settings"></a>흐름 로그 설정 사용

흐름 로그 설정을 사용하려면 다음 작업을 수행해야 합니다.

아직 Azure Insights 공급자를 구독에 등록하지 않은 경우 지금 등록합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
```

NSG 흐름을 저장할 Azure Storage 계정이 아직 없으면 지금 스토리지 계정을 만듭니다. 다음에 나오는 명령을 사용하여 스토리지 계정을 만들 수 있습니다. 명령은 실행하기 전에 `<replace-with-your-unique-storage-account-name>`을 모든 Azure 위치에서 고유한 이름으로 바꿉니다. 이름은 3-24자 사이여야 하고 숫자와 소문자만 사용할 수 있습니다. 필요한 경우 리소스 그룹 이름을 변경할 수도 있습니다.

```azurepowershell-interactive
New-AzStorageAccount `
  -Location westcentralus `
  -Name <replace-with-your-unique-storage-account-name> `
  -ResourceGroupName myResourceGroup `
  -SkuName Standard_LRS `
  -Kind StorageV2
```

그림에 나와 있는 것처럼 다음 옵션을 선택합니다.

1. *상태*에 대해 **켜기**를 선택합니다.
2. **흐름 로그 버전**에 대해 *버전 2* 를 선택 합니다. 버전 2에는 흐름 세션 통계(바이트 및 패킷)가 포함됩니다.
3. 흐름 로그를 저장할 기존 스토리지 계정을 선택합니다. 데이터를 무기한 저장하려면 값을 *0*으로 설정합니다. 스토리지 계정에 대한 Azure Storage 요금이 발생합니다. 저장소에 "Data Lake Storage Gen2 계층 구조 네임 스페이스 사용"이 true로 설정 되어 있지 않은지 확인 합니다.
4. **보존**을 데이터를 저장하려는 일 수로 설정합니다.
> [!IMPORTANT]
> 현재는 Network Watcher에 대한 [NSG(네트워크 보안 그룹) 흐름 로그](network-watcher-nsg-flow-logging-overview.md)가 보존 정책 설정에 따라 Blob 스토리지에서 자동으로 삭제되지 않는 문제가 있습니다. 0이 아닌 기존 보존 정책이 있는 경우 요금이 발생하지 않도록 보존 기간이 지난 스토리지 blob을 주기적으로 삭제하는 것이 좋습니다. NSG 흐름 로그 스토리지 blob을 삭제하는 방법에 대한 자세한 내용은 [NSG 흐름 로그 스토리지 blob 삭제](network-watcher-delete-nsg-flow-log-blobs.md)를 참조하세요.

5. *트래픽 분석 상태*를 **켜기**로 선택합니다.
6. 처리 간격을 선택 합니다. 선택한 내용에 따라 흐름 로그는 저장소 계정에서 수집 되 고 트래픽 분석에 의해 처리 됩니다. 1 시간 마다 또는 10 분 마다 처리 간격을 선택할 수 있습니다. 
7. 기존 Log Analytics(OMS) 작업 영역을 선택하거나 **새 작업 영역 만들기**를 클릭하여 새로 만듭니다. Log Analytics 작업 영역은 트래픽 분석에서 집계 및 인덱싱된 데이터를 저장하는 데 사용되며, 이 데이터는 분석을 생성하는 데 사용됩니다. 기존 작업 영역을 선택하는 경우 해당 작업 영역이 [지원되는 지역](#supported-regions-log-analytics-workspaces) 중 하나에 있어야 하고 새 쿼리 언어로 업그레이드되어야 합니다. 기존 작업 영역을 업그레이드하지 않으려면 또는 지원되는 지역에 작업 영역이 없으면 새로 만듭니다. 쿼리 언어에 대한 자세한 내용은 [새 로그 검색으로 Azure Log Analytics 업그레이드](../log-analytics/log-analytics-log-search-upgrade.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)를 참조하세요.

> [!NOTE]
>트래픽 분석 솔루션 및 NSG를 호스팅하는 로그 분석 작업 영역이 같은 지역에 있어야 하는 것은 아닙니다. 예를 들어 서유럽의 작업 영역에 트래픽 분석이 있고, 미국 동부 및 미국 서부에 NSG가 있을 수 있습니다. 여러 NSG를 동일한 작업 영역에 구성해도 됩니다.

8. **저장**을 선택합니다.

    ![스토리지 계정 선택, Log Analytics 작업 영역 및 트래픽 분석 사용](./media/traffic-analytics/ta-customprocessinginterval.png)

트래픽 분석을 사용할 다른 NSG에 대해 이전 단계를 반복합니다. 흐름 로그의 데이터는 작업 영역으로 전송되므로 작업 영역이 있는 지역에 데이터를 스토리지하는 것을 현지 법률 및 규정에서 허용하는지 확인해야 합니다. 다른 NSGs에 대해 서로 다른 처리 간격을 설정한 경우 데이터는 다른 간격으로 수집 됩니다. 예를 들어 중요 한 Vnet에 대해 10 분의 처리 간격을 사용 하도록 설정 하 고, 중요 하지 않은 Vnet에 1 시간을 사용 하도록 선택할 수 있습니다.

Azure PowerShell에서 [AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) PowerShell cmdlet을 사용 하 여 트래픽 분석을 구성할 수도 있습니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

## <a name="view-traffic-analytics"></a>트래픽 분석 보기

포털의 왼쪽에서 **모든 서비스**를 선택한 다음, *필터* 상자에 **모니터**를 입력합니다. 검색 결과에 **모니터**가 표시되면 모니터를 선택합니다. 트래픽 분석 및 그 기능을 탐색하려면 **Network Watcher**, **트래픽 분석**을 차례로 선택합니다.

![트래픽 분석 대시보드에 액세스](./media/traffic-analytics/accessing-the-traffic-analytics-dashboard.png)

대시보드가 처음으로 표시될 때까지 최대 30분이 걸릴 수 있습니다. 트래픽 분석이 의미 있는 정보를 얻기에 충분한 정보를 수집해야만 보고서를 생성할 수 있기 때문입니다.

## <a name="usage-scenarios"></a>사용 시나리오

트래픽 분석을 완전히 구성한 후 얻을 수 있는 정보는 다음과 같습니다.

### <a name="find-traffic-hotspots"></a>트래픽 핫스폿 찾기

**검색**

- 최대 악성 트래픽을 트래버스하고 상당한 흐름을 차단하는 대부분의 트래픽을 전송하거나 수신하는 호스트, 서브넷 및 가상 네트워크는 무엇인가요?
    - 호스트, 서브넷 및 가상 네트워크에 대한 비교 차트를 확인합니다. 어떤 호스트, 서브넷 및 가상 네트워크가 트래픽을 가장 많이 전송 또는 수신하는지 파악하면 가장 많은 트래픽을 처리하는 호스트를 식별하고, 트래픽 배포가 적절하게 수행되는지 여부를 확인하는 데 도움이 될 수 있습니다.
    - 트래픽 볼륨이 호스트에 적절한지 평가할 수 있습니다. 트래픽 볼륨이 정상 동작인지 아니면 추가 조사가 필요한지 여부
- 인바운드/아웃바운드 트래픽의 양
    -   호스트가 아웃바운드 트래픽보다 인바운드 트래픽을 더 많이 받을 것으로 또는 그 반대일 것으로 예상되는지 여부
- 차단된 트래픽 통계
    - 호스트가 상당한 양의 무해 트래픽을 차단하는 이유 이 동작을 수행하려면 더 많은 조사와 구성의 최적화가 필요합니다.
- 허용된/차단된 악성 트래픽 통계
  - 호스트가 악성 트래픽을 수신하는 이유 및 악성 원본의 흐름이 허용되는 이유 이 동작을 수행하려면 더 많은 조사와 구성의 최적화가 필요합니다.

    다음 그림과 같이 **호스트** 아래에서 **모두 보기**를 선택합니다.

    ![트래픽이 가장 많은 호스트의 세부 정보를 보여주는 대시보드](media/traffic-analytics/dashboard-showcasing-host-with-most-traffic-details.png)

- 다음 그림은 통신 양이 가장 많은 상위 5개 호스트와 흐름 관련 세부 정보(호스트에 대해 허용된 인바운드/아웃바운드 흐름 및 거부된 인바운드/아웃바운드 흐름)에 대한 시간 추세를 보여줍니다.

    ![통신 양이 가장 많은 상위 5개 호스트 추세](media/traffic-analytics/top-five-most-talking-host-trend.png)

**검색**

- 가장 대화가 많은 호스트 쌍
    - 프런트 엔드 또는 백 엔드 통신과 같은 예상된 동작 또는 백 엔드 인터넷 트래픽과 같은 비정상 동작.
- 허용된/차단된 트래픽 통계
    - 호스트가 상당한 양의 트래픽을 허용 또는 차단하는 이유
- 가장 대화가 많은 호스트 쌍 중에서 가장 많이 사용되는 애플리케이션 프로토콜:
    - 이러한 애플리케이션이 이 네트워크에서 허용되는지 여부
    - 애플리케이션이 올바르게 구성되었는지 여부 통신에 적절한 프로토콜을 사용하는지 여부 다음 그림처럼 **자주 하는 대화** 아래에서 **모두 보기**를 선택합니다.

        ![빈도가 가장 높은 대화를 보여주는 대시보드](./media/traffic-analytics/dashboard-showcasing-most-frequent-conversation.png)

- 다음 그림은 상위 5개 대화 및 흐름 관련 세부 정보(예: 대화 쌍에 대해 허용된/거부된 인바운드 및 아웃바운드 흐름)에 대한 시간 추세를 보여줍니다.

    ![대화가 많은 상위 5개의 세부 정보 및 추세](./media/traffic-analytics/top-five-chatty-conversation-details-and-trend.png)

**검색**

- 환경에서 가장 많이 사용되는 애플리케이션 프로토콜 및 애플리케이션 프로토콜을 가장 많이 사용하는 호스트 쌍
    - 이러한 애플리케이션이 이 네트워크에서 허용되는지 여부
    - 애플리케이션이 올바르게 구성되었는지 여부 통신에 적절한 프로토콜을 사용하는지 여부 예상되는 동작은 80 및 443 같은 공통 포트입니다. 표준 통신의 겨우 비정상적인 포트가 표시되는 경우 구성 변경이 필요할 수 있습니다. 다음 그림처럼 **애플리케이션 포트** 아래에서 **모두 보기**를 선택합니다.

        ![상위 애플리케이션 프로토콜을 표시하는 대시보드](./media/traffic-analytics/dashboard-showcasing-top-application-protocols.png)

- 다음 그림은 상위 5개 L7 프로토콜 및 L7 프로토콜의 흐름 관련 세부 정보(예: 허용된 흐름 및 거부된 흐름)에 대한 시간 추세를 보여줍니다.

    ![상위 5개 계층 7 프로토콜 세부 정보 및 추세](./media/traffic-analytics/top-five-layer-seven-protocols-details-and-trend.png)

    ![로그 검색의 애플리케이션 프로토콜에 대한 흐름 세부 정보](./media/traffic-analytics/flow-details-for-application-protocol-in-log-search.png)

**검색**

- 환경 내 VPN 게이트웨이의 용량 사용률 추세.
    - 각 VPN SKU는 일정량의 대역폭을 허용합니다. VPN 게이트웨이 사용률이 낮습니까?
    - 게이트웨이가 용량 제한에 근접했습니까? 그 다음 상위 SKU로 업그레이드해야 합니까?
- 가장 대화가 많은 호스트는 어떤 것입니까? 어떤 VPN 게이트웨이, 어떤 포트를 통해 대화가 이루어집니까?
    - 이 패턴이 정상입니까? 다음 그림처럼 **VPN 게이트웨이** 아래에서 **모두 보기**를 선택합니다.

        ![상위 활성 VPN 연결을 보여주는 대시보드](./media/traffic-analytics/dashboard-showcasing-top-active-vpn-connections.png)

- 다음 그림은 Azure VPN Gateway의 용량 활용도에 대한 시간 추세 및 흐름 관련 세부 정보(예: 허용되는 흐름 및 포트)를 보여줍니다.

    ![VPN 게이트웨이 사용량 추세 및 흐름 세부 정보](./media/traffic-analytics/vpn-gateway-utilization-trend-and-flow-details.png)

### <a name="visualize-traffic-distribution-by-geography"></a>지리별로 트래픽 분포 시각화

**검색**

- 데이터 센터 상위 트래픽 소스, 데이터 센터와의 상위 불량 네트워크 대화, 상위 대화 애플리케이션 프로토콜 같은 데이터 센터별 트래픽 분포.
  - 데이터 센터에서 더 많은 부하가 목격되는 경우 효율적인 트래픽 분포를 계획할 수 있습니다.
  - 불량 네트워크가 데이터 센터에서 대화하는 경우 해당 네트워크를 차단하도록 NSG 규칙을 수정합니다.

    다음 그림처럼 **환경** 아래에서 **맵 보기**를 선택합니다.

    ![트래픽 분포를 보여주는 대시보드](./media/traffic-analytics/dashboard-showcasing-traffic-distribution.png)

- 지역 지도에는 데이터 센터 (배포 됨/배포 안 함/활성/비활성/트래픽 분석 사용/트래픽 분석 사용 안 함) 및 활성 배포에 대 한 무해/악성 트래픽을 기여 하는 국가/지역 등의 매개 변수를 선택할 수 있는 최상위 리본 메뉴가 표시 됩니다.

    ![활성 배포를 표시하는 지역 지도 보기](./media/traffic-analytics/geo-map-view-showcasing-active-deployment.png)

- 지리 맵은 파란색 (무해 한 트래픽) 및 빨간색 (악성 트래픽) 색으로 통신 하는 국가/지역 및 대륙의 데이터 센터에 대 한 트래픽 분포를 보여 줍니다.

    ![국가/지역 및 대륙에 대 한 트래픽 분포를 보여주는 지역 지도 보기](./media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png)

    ![로그 검색의 트래픽 분포에 대한 흐름 세부 정보](./media/traffic-analytics/flow-details-for-traffic-distribution-in-log-search.png)

### <a name="visualize-traffic-distribution-by-virtual-networks"></a>가상 네트워크별로 트래픽 분포 시각화

**검색**

- 가상 네트워크, 토폴로지, 가상 네트워크로 가는 상위 트래픽 소스, 가상 네트워크와 대화하는 상위 불량 네트워크, 대화하는 상위 애플리케이션 프로토콜별 트래픽 분포.
  - 어떤 가상 네트워크가 어떤 가상 네트워크와 대화하는지 파악. 대화가 예상되지 않는 경우 수정할 수 있습니다.
  - 불량 네트워크가 가상 네트워크와 대화하는 경우 불량 네트워크를 차단하도록 NSG 규칙을 수정할 수 있습니다.
 
    다음 그림처럼 **환경** 아래에서 **VNet 보기**를 선택합니다.

    ![가상 네트워크 분포를 표시하는 대시보드](./media/traffic-analytics/dashboard-showcasing-virtual-network-distribution.png)

- 가상 네트워크 토폴로지는 가상 네트워크(상호 가상 네트워크 연결/활성/비활성), 외부 연결, 활성 흐름, 가상 네트워크의 악의적 흐름 등과 같은 매개 변수를 선택할 수 있는 상단 리본을 표시합니다.
- 구독, 작업 영역, 리소스 그룹 및 시간 간격에 따라 가상 네트워크 토폴로지를 필터링할 수 있습니다. 흐름을 이해 하는 데 도움이 되는 추가 필터는 흐름 유형 (InterVNet, IntraVNET 등), 흐름 방향 (인바운드, 아웃 바운드), 흐름 상태 (허용 됨, 차단 됨), Vnet (대상 및 연결), 연결 유형 (피어 링 또는 게이트웨이-P2S 및 S2S) 및 NSG입니다. 이러한 필터를 사용하여 자세히 살펴볼 VNet에 초점을 맞춥니다.
- 가상 네트워크 토폴로지는 흐름(허용된/차단된/인바운드/아웃바운드/무해/악성), 애플리케이션 프로토콜, 네트워크 보안 그룹과 관련하여 가상 네트워크의 트래픽 분포를 보여주며, 다음은 그 예입니다.

    ![트래픽 분포 및 흐름 세부 정보를 표시하는 가상 네트워크 토폴로지](./media/traffic-analytics/virtual-network-topology-showcasing-traffic-distribution-and-flow-details.png)
    
    ![최상위 필터와 추가 필터를 보여 주는 가상 네트워크 토폴로지](./media/traffic-analytics/virtual-network-filters.png)

    ![로그 검색의 가상 네트워크 트래픽 분포에 대한 흐름 세부 정보](./media/traffic-analytics/flow-details-for-virtual-network-traffic-distribution-in-log-search.png)

**검색**

- 서브넷, 토폴로지, 서브넷으로 가는 상위 트래픽 소스, 서브넷과 대화하는 상위 불량 네트워크, 대화하는 상위 애플리케이션 프로토콜별 트래픽 분포.
    - 어떤 서브넷이 어떤 서브넷과 대화하는지 파악. 예기치 않은 대화가 발견되면 구성을 수정할 수 있습니다.
    - 불량 네트워크가 서브넷과 대화하는 경우 불량 네트워크를 차단하도록 NSG 규칙을 구성하여 수정할 수 있습니다.
- 서브넷 토폴로지는 활성/비활성 서브넷, 외부 연결, 활성 흐름, 서브넷의 악의적인 흐름 등과 같은 매개 변수를 선택할 수 있는 상단 리본을 보여줍니다.
- 서브넷 토폴로지는 흐름(허용된/차단된/인바운드/아웃바운드/무해/악성), 애플리케이션 프로토콜, NSG와 관련하여 가상 네트워크의 트래픽 분포를 보여주며, 다음은 그 예입니다.

    ![흐름과 관련하여 가상 네트워크 서브넷의 트래픽 분포를 보여주는 서브넷 토폴로지](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-virtual-subnet-with-regards-to-flows.png)

**검색**

애플리케이션 게이트웨이 및 Load Balancer, 토폴로지, 상위 트래픽 소스, 애플리케이션 게이트웨이 및 Load Balancer와 대화하는 상위 불량 네트워크, 대화하는 상위 애플리케이션 프로토콜별 트래픽 분포. 
    
 - 애플리케이션 게이트웨이 또는 Load Balancer와 대화하는 서브넷을 알고 있음. 예기치 않은 대화가 발견되면 구성을 수정할 수 있습니다.
 - 불량 네트워크가 애플리케이션 게이트웨이 또는 Load Balancer와 대화하는 경우 불량 네트워크를 차단하도록 NSG 규칙을 구성하여 수정할 수 있습니다. 

    ![subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows](./media/traffic-analytics/subnet-topology-showcasing-traffic-distribution-to-a-application-gateway-subnet-with-regards-to-flows.png)

### <a name="view-ports-and-virtual-machines-receiving-traffic-from-the-internet"></a>인터넷에서 트래픽을 수신하는 포트 및 가상 머신 보기

**검색**

- 인터넷을 통해 대화하는 열린 포트
  - 예상치 않은 포트가 열려 있는 것이 발견되면 구성을 수정할 수 있습니다.

    ![인터넷으로 트래픽을 전송하고 수신하는 포트를 보여주는 대시보드](./media/traffic-analytics/dashboard-showcasing-ports-receiving-and-sending-traffic-to-the-internet.png)

    ![Azure 대상 포트 및 호스트 세부 정보](./media/traffic-analytics/details-of-azure-destination-ports-and-hosts.png)

**검색**

환경 내 악성 트래픽의 존재 여부 악성 트래픽의 출처 악성 트래픽의 목적지

![로그 검색의 악성 트래픽 흐름 세부 정보](./media/traffic-analytics/malicious-traffic-flows-detail-in-log-search.png)


### <a name="visualize-the-trends-in-nsgnsg-rules-hits"></a>NSG/NSG 규칙 적중의 추세 시각화

**검색**

- 흐름 분포와 비교 차트에서 대부분 적중하는 NSG/NSG 규칙은 무엇인가요?
- NSG/NSG 규칙당 최상위 원본 및 대상 대화 쌍

    ![NSG 적중 통계를 보여주는 대시보드](./media/traffic-analytics/dashboard-showcasing-nsg-hits-statistics.png)

- 다음 그림은 NSG 규칙에 대한 시간 추세와 네트워크 보안 그룹에 대한 원본-대상 흐름 세부 정보를 보여줍니다.

  - 악성 흐름을 트래버스하는 NSG 및 NSG 규칙과 클라우드 환경에 액세스하는 상위 악성 IP 주소를 빠르게 검색합니다.
  - 상당한 네트워크 트래픽을 허용/차단하는 NSG/NSG 규칙 식별
  - NSG 또는 NSG 규칙의 세부적인 검사에 대한 상위 필터 선택

    ![NSG 규칙 적중 횟수와 상위 NSG 규칙에 대한 시간 추세를 표시](./media/traffic-analytics/showcasing-time-trending-for-nsg-rule-hits-and-top-nsg-rules.png)

    ![로그 검색의 상위 NSG 규칙 통계 세부 정보](./media/traffic-analytics/top-nsg-rules-statistics-details-in-log-search.png)

## <a name="frequently-asked-questions"></a>질문과 대답

자주 묻는 질문에 대한 답변은 [트래픽 분석 FAQ](traffic-analytics-faq.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- 흐름 로그를 사용하도록 설정하는 방법을 알아보려면 [NSG 흐름 로깅을 사용하도록 설정](network-watcher-nsg-flow-logging-portal.md)을 참조하세요.
- 트래픽 분석의 스키마 및 처리 세부 정보를 이해 하려면 [트래픽 분석 스키마](traffic-analytics-schema.md)를 참조 하세요.
