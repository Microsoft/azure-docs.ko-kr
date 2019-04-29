---
title: Azure 표준 Load Balancer 진단
titlesuffix: Azure Load Balancer
description: Azure 표준 Load Balancer 진단을 위해 사용 가능한 메트릭 및 상태 정보 사용
services: load-balancer
documentationcenter: na
author: KumudD
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2018
ms.author: Kumud
ms.openlocfilehash: 77c3c595994092ff2ca68f3cefa5eb3c8a54bcd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735240"
---
# <a name="metrics-and-health-diagnostics-for-standard-load-balancer"></a>표준 Load Balancer에 대한 메트릭 및 상태 진단

Azure 표준 Load Balancer는 리소스에 다음과 같은 진단 기능을 제공합니다.
* **다차원 메트릭**: [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)를 통해 공용 및 내부 부하 분산 장치 구성에 대한 새로운 다차원 진단 기능을 제공합니다. 부하 분산 장치 리소스를 모니터링 및 관리하고 관련 문제를 해결할 수 있습니다.

* **리소스 상태**: Azure Portal의 Load Balancer 페이지와 Resource Health 페이지(모니터 아래)에는 표준 Load Balancer의 공용 부하 분산 장치 구성에 대한 Resource Health 섹션이 표시됩니다.

이 문서에서는 이러한 기능에 대한 간단한 설명을 제공하고 표준 Load Balancer에 이러한 기능을 사용하는 방법을 제공합니다.

## <a name = "MultiDimensionalMetrics"></a>다차원 메트릭

Azure Load Balancer는 Azure Portal에서 새 Azure 메트릭(미리 보기)을 통해 새로운 다차원 메트릭을 제공하고, 부하 분산 장치 리소스를 실시간으로 진단 분석하도록 도와줍니다. 

다양한 표준 Load Balancer 구성에서는 다음 메트릭을 제공합니다.

| 메트릭 | 리소스 종류 | 설명 | 권장 집계 |
| --- | --- | --- | --- |
| VIP 가용성(데이터 경로 가용성) | 공용 부하 분산 장치 | 표준 Load Balancer는 지역 내에서 부하 분산 장치 프런트 엔드로, 마지막으로 VM을 지원하는 SDN 스택으로 데이터 경로를 연속적으로 실행합니다. 정상 인스턴스가 남아 있는 한 측정은 애플리케이션 부하가 분산된 트래픽과 동일한 경로를 따릅니다. 고객이 사용하는 데이터 경로의 유효성도 검사합니다. 측정은 애플리케이션에 표시되지 않으며 다른 작업을 방해하지 않습니다.| 평균 |
| DIP 가용성(상태 프로브 상태) |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 구성 설정에 따라 애플리케이션 엔드포인트의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 부하 분산 장치 풀에서 각 인스턴스 엔드포인트의 집계 또는 엔드포인트당 필터링된 보기를 제공합니다. 상태 프로브 구성에 표시된 대로 Load Balancer에서 애플리케이션의 상태를 보는 방법을 확인할 수 있습니다. |  평균 |
| SYN(동기화) 패킷 |  공용 부하 분산 장치 | 표준 Load Balancer는 TCP(Transmission Control Protocol) 연결을 종료하거나 TCP 또는 UDP 패킷 흐름을 조작하지 않습니다. 흐름 및 해당 핸드셰이크는 항상 원본과 VM 인스턴스 사이에 있습니다. TCP 프로토콜 시나리오의 문제를 잘 해결하기 위해 SYN 패킷 카운터를 사용하여 TCP 연결 시도 횟수를 파악할 수 있습니다. 메트릭은 수신된 TCP SYN 패킷 수를 보고합니다.| 평균 |
| SNAT 연결 |  공용 Load Balancer |표준 Load Balancer는 공용 IP 주소 프런트 엔드로 위장되는 아웃바운드 흐름 수를 보고합니다. SNAT(원본 네트워크 주소 변환) 포트는 소모성 리소스입니다. 이 메트릭은 애플리케이션이 아웃바운드에서 시작된 흐름에 대해 SNAT에 얼마나 의존하는지를 나타낼 수 있습니다. 성공 및 실패한 아웃바운드 SNAT 흐름에 대한 카운터가 보고되고 아웃바운드 흐름의 상태를 이해하고 문제를 해결하는 데 사용할 수 있습니다.| 평균 |
| 바이트 카운터 |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 데이터를 보고합니다.| 평균 |
| 패킷 카운터 |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 패킷을 보고합니다.| 평균 |

### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 메트릭 보기

Azure Portal은 특정 리소스에 대한 부하 분산 장치 리소스 페이지 및 Azure Monitor 페이지에서 사용할 수 있는 메트릭(미리 보기) 페이지를 통해 부하 분산 장치 메트릭을 표시합니다. 

표준 Load Balancer 리소스에 대한 메트릭을 보려면:
1. 메트릭(미리 보기) 페이지로 이동하고 중 하나를 수행합니다.
   * 부하 분산 장치 리소스 페이지의 드롭다운 목록에서 메트릭 유형을 선택합니다.
   * Azure Monitor 페이지에서 부하 분산 장치 리소스를 선택합니다.
2. 적절한 집계 유형을 설정합니다.
3. 필요에 따라 필요한 필터링 및 그룹화를 구성합니다.

![표준 Load Balancer에 대한 메트릭 미리 보기](./media/load-balancer-standard-diagnostics/LBMetrics1.png)

*그림: 표준 Load Balancer에 대한 DIP 가용성 및 상태 프로브 상태 메트릭*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API를 통해 프로그래밍 방식으로 다차원 메트릭 검색

다차원 메트릭 정의 및 값을 검색하기 위한 API 지침은 [Azure Monitoring REST API 연습](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)을 참조하세요.

### <a name = "DiagnosticScenarios"></a>일반적인 진단 시나리오 및 권장 보기

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-vip"></a>이 데이터 경로가 작동되며 부하 분산 장치 VIP에 사용할 수 있나요?

VIP 가용성 메트릭은 지역 내에서 VM이 있는 Compute 호스트로의 데이터 경로 상태를 설명합니다. 메트릭은 Azure 인프라의 상태를 반영한 것입니다. 이 메트릭으로 다음을 수행할 수 있습니다.
- 서비스의 외부 가용성 모니터링
- 서비스가 배포된 플랫폼이 정상 상태인지 여부 또는 게스트 OS 또는 애플리케이션 인스턴스가 정상 상태인지 여부를 심층 분석 및 이해
- 이벤트가 서비스와 관련된 것인지 또는 기본 데이터 평면과 관련된 것인지를 구분합니다. 이 메트릭을 상태 프로브 상태("DIP 가용성")와 혼동하지 마세요.

표준 Load Balancer 리소스에 대한 VIP 가용성을 가져오려면
1. 올바른 부하 분산 장치 리소스를 선택했는지 확인합니다. 
2. **메트릭** 드롭다운 목록에서 **VIP 가용성**을 선택합니다. 
3. **집계** 드롭다운 목록에서 **Avg**를 선택합니다. 
4. 또한 필요한 프런트 엔드 IP 주소 또는 프렌트 엔드 포트를 갖는 차원으로 VIP 주소 또는 VIP 포트에 대한 필터를 추가한 다음, 선택한 차원에 따라 그룹화합니다.

![VIP 검색](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*그림: Load Balancer VIP 검색 세부 정보*

이 메트릭은 활성, 대역 내 측정을 통해 생성됩니다. 지역 내의 검색 서비스는 측정에 대한 트래픽을 발생합니다. 서비스는 공용 프런트 엔드로 배포를 만드는 즉시 활성화되고, 프런트 엔드를 제거할 때까지 계속됩니다. 

>[!NOTE]
>내부 프런트 엔드는 현재 지원되지 않습니다. 

배포의 프런트 엔드 및 규칙과 일치하는 패킷이 주기적으로 생성됩니다. 원본에서 백 엔드 풀의 VM이 있는 호스트까지 해당 지역을 트래버스합니다. 부하 분산 장치 인프라는 다른 모든 트래픽의 경우와 동일하게 부하 분산 및 변환 작업을 수행합니다. 이 프로브는 대역 내의 부하 분산된 엔드포인트에 있습니다. 프로브가 백 엔드 풀의 정상 VM이 있는 Compute 호스트에 도착하면, 계산 호스트는 검색 서비스에 대한 응답을 생성합니다. VM에는 이 트래픽이 표시되지 않습니다.

다음과 같은 이유로 VIP 가용성이 실패합니다.
- 배포의 백 엔드 풀에 정상 상태의 VM이 남아 있지 않습니다. 
- 인프라 중단이 발생했습니다.

진단 목적으로 [상태 프로브 상태와 함께 VIP 가용성 메트릭](#vipavailabilityandhealthprobes)을 사용할 수 있습니다.

대부분의 시나리오에서 집계로 **Average**를 사용합니다.

#### <a name="are-the-back-end-instances-for-my-vip-responding-to-probes"></a>VIP에 대한 백 엔드 인스턴스가 프로브에 응답하고 있나요?

상태 프로브 상태 메트릭은 부하 분산 장치의 상태 프로브를 구성할 때 사용자가 구성한 애플리케이션 배포의 상태를 설명합니다. 부하 분산 장치는 상태 프로브의 상태를 사용하여 새 흐름을 보낼 위치를 결정합니다. 상태 프로브는 Azure 인프라 주소에서 시작되며, VM의 게스트 OS 내에서 볼 수 있습니다.

표준 Load Balancer 리소스에 대한 DIP 가용성을 가져오려면:
1. **Avg** 집계 유형을 갖는 **DIP 가용성** 메트릭을 선택합니다. 
2. 필요한 VIP IP 주소 또는 포트(또는 둘 다)에 필터를 적용합니다.

![DIP 가용성](./media/load-balancer-standard-diagnostics/LBMetrics-DIPAvailability.png)

*그림: Load Balancer VIP 가용성*

다음과 같은 이유로 상태 프로브가 실패합니다.
- 수신 또는 응답하지 않거나 잘못된 프로토콜을 사용하는 포트로 상태 프로브를 구성하는 경우. 서비스가 DSR(Direct Server Return 또는 부동 IP) 규칙을 사용하는 경우 서비스가 프런트 엔드 IP 주소로 구성된 루프백이 아니라 NIC의 IP 구성에서 수신 대기하고 있는지 확인해야 합니다.
- 프로브가 네트워크 보안 그룹, VM의 게스트 OS 방화벽 또는 애플리케이션 계층 필터에서 허용되지 않는 경우.

대부분의 시나리오에서 집계로 **Average**를 사용합니다.

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>내 아웃바운드 연결 통계는 어떻게 확인할 수 있나요? 

SNAT 연결 메트릭은 [아웃바운드 흐름](https://aka.ms/lboutbound)에 대한 성공 및 실패 연결의 볼륨을 설명합니다.

실패한 연결 볼륨이 0보다 크면 SNAT 포트가 고갈된 것을 나타냅니다. 추가로 조사하여 이러한 실패의 원인을 확인해야 합니다. SNAT 포트 고갈은 [아웃바운드 흐름](https://aka.ms/lboutbound) 설정 실패를 나타냅니다. 아웃바운드 연결에 대한 문서를 검토하여 시나리오 및 작동 메커니즘을 이해하고, SNAT 포트 고갈 방지를 위한 완화 및 설계 방법을 알아봅니다. 

SNAT 연결 통계를 가져오려면:
1. **SNAT 연결** 메트릭 유형을 선택하고 집계로 **Sum**을 선택합니다. 
2. 다른 줄로 표시된 성공 및 실패 SNAT 연결 수를 **연결 상태**별로 그룹화합니다. 

![SNAT 연결](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*그림: Load Balancer SNAT 연결 수*


#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>내 서비스에 대한 인바운드/아웃바운드 연결 시도를 확인하려면 어떻게 해야 하나요?

SYN 패킷 메트릭은 특정 프런트 엔드와 관련해서 도착했거나 전송된 ([아웃바운드 흐름](https://aka.ms/lboutbound) 관련) TCP SYN 패킷 볼륨을 설명합니다. 이 메트릭을 사용하여 서비스에 대한 TCP 연결 시도를 이해할 수 있습니다.

대부분의 시나리오에서 집계로 **Total**을 사용합니다.

![SYN 연결](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*그림: Load Balancer SYN 수*


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>내 네트워크 대역폭 소비를 어떻게 확인할 수 있나요? 

바이트 및 패킷 카운터 메트릭은 프런트 엔드 기준으로 서비스에서 전송 또는 수신된 바이트 및 패킷 볼륨을 설명합니다.

대부분의 시나리오에서 집계로 **Total**을 사용합니다.

바이트 또는 패킷 수 통계를 가져오려면:
1. 집계가 **Avg**인 **바이트 수** 및/또는 **패킷 수** 메트릭 유형을 선택합니다. 
2. 다음 중 하나를 수행합니다.
   * 특정 프런트 엔드 IP, 프런트 엔드 포트, 백 엔드 IP 또는 백 엔드 포트에 필터를 적용합니다.
   * 필터링 없이 부하 분산 장치 리소스에 대한 전체 통계를 가져옵니다.

![바이트 수](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*그림: Load Balancer 바이트 수*

#### <a name = "vipavailabilityandhealthprobes"></a>내 부하 분산 장치 배포를 진단하려면 어떻게 해야 하나요?

단일 차트에 VIP 가용성 및 상태 프로브 메트릭의 조합을 사용하여 문제를 확인하고 해결할 위치를 식별할 수 있습니다. Azure가 제대로 작동하고 있는지 확신하고, 이 정보를 토대로 구성 또는 애플리케이션이 근본 원인이라는 결론을 얻을 수 있습니다.

상태 프로브 메트릭을 사용하여 Azure에서 사용자가 제공한 구성을 기준으로 배포의 상태를 확인하는 방법을 이해할 수 있습니다. 상태 프로브를 확인하는 것은 항상 모니터링 또는 원인 파악에 도움이 되는 첫 단계입니다.

추가 단계를 수행하고 VIP 가용성 메트릭을 사용하여 Azure가 특정 배포를 담당하는 기본 데이터 평면의 상태를 확인하는 방법을 이해할 수 있습니다. 두 메트릭에 함께 사용하면 이 예제에 설명된 대로 오류 위치를 격리시킬 수 있습니다.

![VIP 진단](./media/load-balancer-standard-diagnostics/LBMetrics-DIPnVIPAvailability.png)

*그림: DIP 및 VIP 가용성 메트릭 조합*

차트에는 다음 정보가 표시됩니다.
- 인프라 자체는 정상이며, VM을 호스트하는 인프라에 연결할 수 있고, 둘 이상의 VM이 백 엔드에 배치되었습니다. 이 정보는 100%인 VIP 가용성에 대해 파란색으로 표시됩니다. 
- 그러나 상태 프로브 상태(DIP 가용성)는 차트 맨 처음에 주황색으로 0%로 표시됩니다. 녹색 원으로 그려진 영역은 상태(DIP 가용성)가 정상 상태인 경우를 강조 표시합니다. 이때 고객의 배포는 새 흐름을 수락할 수 있습니다.

이 차트를 통해 고객은 다른 문제 발생 여부에 관계없이, 추측을 하거나 지원을 요청하지 않고 직접 배포 문제를 해결할 수 있습니다. 잘못된 구성 또는 실패한 애플리케이션 때문에 상태 프로브가 실패했으므로 서비스는 사용할 수 없었습니다.

### <a name = "Limitations"></a>제한 사항

현재 VIP 가용성은 공용 프런트 엔드에 대해서만 사용할 수 있습니다.

## <a name = "ResourceHealth"></a>Resource Health 상태

표준 Load Balancer **리소스에 대한 상태는** **모니터 > 서비스 상태** 아래의 기존 를 통해 표시됩니다.

>[!NOTE]
>Load Balancer에 대한 Resource Health 상태는 현재 표준 Load Balancer의 공용 구성에 대해서만 사용할 수 있습니다. 내부 부하 분산 장치 리소스 또는 Load Balancer 리소스의 기본 SKU는 리소스 상태를 표시하지 않습니다.

공용 표준 Load Balancer 리소스의 상태를 보려면:
1. **모니터** > **Service Health**를 선택합니다.

   ![모니터 페이지](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *그림: Azure Monitor의 Service Health 링크*

2. **Resource Health**를 선택한 다음, **구독 ID** 및 **리소스 유형 = Load Balancer**를 선택했는지 확인합니다.

   ![Resource Health 상태](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *그림: 상태 보기에 대한 리소스 선택*

3. 목록에서 Load Balancer 리소스를 선택하여 기록 상태를 확인합니다.

    ![Load Balancer 상태](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *그림: Load Balancer 리소스 상태 보기*
 
다음 표에는 다양한 리소스 상태와 해당 설명이 나와 있습니다. 

| Resource Health 상태 | 설명 |
| --- | --- |
| 사용 가능 | 공용 표준 부하 분산 장치 리소스는 정상 상태이며 사용 가능합니다. |
| 사용할 수 없음 | 공용 표준 부하 분산 장치 리소스는 정상 상태가 아닙니다. **Azure Monitor** > **메트릭**을 선택하여 상태를 진단합니다.<br>(*사용할 수 없는* 상태는 리소스가 공용 표준 부하 분산 장치와 연결되지 않은 상태를 의미할 수도 있습니다.) |
| 알 수 없음 | 공용 표준 부하 분산 장치의 리소스 상태가 아직 업데이트되지 않은 것입니다.<br>(*알 수 없는* 상태는 리소스가 공용 표준 부하 분산 장치와 연결되지 않은 상태를 의미할 수도 있습니다.)  |

## <a name="next-steps"></a>다음 단계

- [Standard Load Balancer](load-balancer-standard-overview.md)에 대해 자세히 알아보세요.
- [Load Balancer 아웃바운드 연결](https://aka.ms/lboutbound)에 대해 자세히 알아봅니다.
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)에 대해 자세히 알아봅니다.
- [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 및 [REST API를 통해 메트릭을 검색하는 방법](/rest/api/monitor/metrics/list)에 대해 자세히 알아봅니다.


