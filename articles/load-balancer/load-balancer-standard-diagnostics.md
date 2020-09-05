---
title: 메트릭, 경고 및 리소스 상태와 진단-Azure 표준 Load Balancer
description: 사용 가능한 메트릭, 경고 및 리소스 상태 정보를 사용 하 여 Azure 표준 Load Balancer를 진단 합니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: allensu
ms.openlocfilehash: 97541a4f8d86b90bf6045fc2a9e5abbe86aee5cd
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717339"
---
# <a name="standard-load-balancer-diagnostics-with-metrics-alerts-and-resource-health"></a>메트릭, 경고 및 리소스 상태를 사용하는 표준 Load Balancer 진단

Azure 표준 Load Balancer는 다음과 같은 진단 기능을 제공 합니다.

* **다차원 메트릭 및 경고**: 표준 부하 분산 장치 구성에 대 한 [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) 를 통해 다차원 진단 기능을 제공 합니다. 표준 부하 분산 장치 리소스를 모니터링 하 고 관리 하 고 문제를 해결할 수 있습니다.

* **리소스 상태**: Load Balancer의 Resource Health 상태는 모니터의 Resource Health 페이지에서 사용할 수 있습니다. 이 자동 검사는 Load Balancer 리소스의 현재 가용성을 알려 줍니다.

이 문서에서는 이러한 기능에 대한 간단한 설명을 제공하고 표준 Load Balancer에 이러한 기능을 사용하는 방법을 제공합니다. 

## <a name="multi-dimensional-metrics"></a><a name = "MultiDimensionalMetrics"></a>다차원 메트릭

Azure Load Balancer는 Azure Portal의 Azure 메트릭을 통해 다차원 메트릭을 제공 하 고 부하 분산 장치 리소스에 대 한 실시간 진단 정보를 얻을 수 있습니다. 

다양한 표준 Load Balancer 구성에서는 다음 메트릭을 제공합니다.

| 메트릭 | 리소스 종류 | Description | 권장 집계 |
| --- | --- | --- | --- |
| 데이터 경로 가용성 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 지역 내에서 부하 분산 장치 프런트 엔드로, 마지막으로 VM을 지원하는 SDN 스택으로 데이터 경로를 연속적으로 실행합니다. 정상 인스턴스가 남아 있는 한 측정은 애플리케이션 부하가 분산된 트래픽과 동일한 경로를 따릅니다. 고객이 사용하는 데이터 경로의 유효성도 검사합니다. 측정은 애플리케이션에 표시되지 않으며 다른 작업을 방해하지 않습니다.| 평균 |
| 상태 프로브 상태 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 구성 설정에 따라 애플리케이션 엔드포인트의 상태를 모니터링하는 분산된 상태 검색 서비스를 사용합니다. 이 메트릭은 부하 분산 장치 풀에서 각 인스턴스 엔드포인트의 집계 또는 엔드포인트당 필터링된 보기를 제공합니다. 상태 프로브 구성에 표시된 대로 Load Balancer에서 애플리케이션의 상태를 보는 방법을 확인할 수 있습니다. |  평균 |
| SYN(동기화) 패킷 | 공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 TCP(Transmission Control Protocol) 연결을 종료하거나 TCP 또는 UDP 패킷 흐름을 조작하지 않습니다. 흐름 및 해당 핸드셰이크는 항상 원본과 VM 인스턴스 사이에 있습니다. TCP 프로토콜 시나리오의 문제를 잘 해결하기 위해 SYN 패킷 카운터를 사용하여 TCP 연결 시도 횟수를 파악할 수 있습니다. 메트릭은 수신된 TCP SYN 패킷 수를 보고합니다.| 평균 |
| SNAT 연결 | 공용 부하 분산 장치 |표준 Load Balancer는 공용 IP 주소 프런트 엔드로 위장되는 아웃바운드 흐름 수를 보고합니다. SNAT(원본 네트워크 주소 변환) 포트는 소모성 리소스입니다. 이 메트릭은 애플리케이션이 아웃바운드에서 시작된 흐름에 대해 SNAT에 얼마나 의존하는지를 나타낼 수 있습니다. 성공 및 실패한 아웃바운드 SNAT 흐름에 대한 카운터가 보고되고 아웃바운드 흐름의 상태를 이해하고 문제를 해결하는 데 사용할 수 있습니다.| 평균 |
| 할당 된 SNAT 포트 | 공용 부하 분산 장치 | 표준 Load Balancer는 백 엔드 인스턴스당 할당 된 SNAT 포트 수를 보고 합니다. | 평균적. |
| 사용 되는 SNAT 포트 | 공용 부하 분산 장치 | 표준 Load Balancer는 백 엔드 인스턴스당 활용 되는 SNAT 포트 수를 보고 합니다. | 평균 | 
| 바이트 카운터 |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 데이터를 보고합니다. 바이트가 백 엔드 인스턴스에서 균등하게 배포되지 않을 수 있습니다. 이는 Azure의 Load Balancer 알고리즘이 흐름을 기반으로 하기 때문에 예상 됩니다. | 평균 |
| 패킷 카운터 |  공용 및 내부 부하 분산 장치 | 표준 Load Balancer는 프런트 엔드당 처리된 패킷을 보고합니다.| 평균 |

  >[!NOTE]
  >NVA 또는 방화벽을 통해 내부 부하 분산 장치에서 트래픽 분산을 사용 하는 경우 Syn 패킷, 바이트 카운터 및 패킷 카운터 메트릭을 사용할 수 없으며 0으로 표시 됩니다. 
  
### <a name="view-your-load-balancer-metrics-in-the-azure-portal"></a>Azure Portal에서 부하 분산 장치 메트릭 보기

Azure Portal는 특정 리소스에 대 한 부하 분산 장치 리소스 페이지 및 Azure Monitor 페이지에서 사용할 수 있는 메트릭 페이지를 통해 부하 분산 장치 메트릭을 노출 합니다. 

표준 Load Balancer 리소스에 대한 메트릭을 보려면:
1. 메트릭 페이지로 이동 하 고 다음 중 하나를 수행 합니다.
   * 부하 분산 장치 리소스 페이지의 드롭다운 목록에서 메트릭 유형을 선택합니다.
   * Azure Monitor 페이지에서 부하 분산 장치 리소스를 선택합니다.
2. 적절 한 메트릭 집계 유형을 설정 합니다.
3. 필요에 따라 필요한 필터링 및 그룹화를 구성합니다.
4. 필요에 따라 시간 범위와 집계를 구성 합니다. 기본적으로 시간은 UTC로 표시 됩니다.

  >[!NOTE] 
  >시간 집계는 데이터가 분당 한 번 샘플링 될 때 특정 메트릭을 해석할 때 중요 합니다. 시간 집계를 5 분으로 설정 하 고 메트릭 집계 유형 Sum을 SNAT 할당과 같은 메트릭에 사용 하면 그래프가 할당 된 총 SNAT 포트의 5 배를 표시 합니다. 

![표준 Load Balancer에 대 한 메트릭](./media/load-balancer-standard-diagnostics/lbmetrics1anew.png)

*그림: 표준 Load Balancer에 대 한 데이터 경로 가용성 메트릭*

### <a name="retrieve-multi-dimensional-metrics-programmatically-via-apis"></a>API를 통해 프로그래밍 방식으로 다차원 메트릭 검색

다차원 메트릭 정의 및 값을 검색하기 위한 API 지침은 [Azure Monitoring REST API 연습](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough#retrieve-metric-definitions-multi-dimensional-api)을 참조하세요. 이러한 메트릭은 ' 모든 메트릭 ' 옵션을 통해 저장소 계정에 쓸 수 있습니다. 

### <a name="configure-alerts-for-multi-dimensional-metrics"></a>다차원 메트릭에 대 한 경고 구성 ###

Azure 표준 Load Balancer는 다차원 메트릭에 대해 쉽게 구성할 수 있는 경고를 지원 합니다. 특정 메트릭에 대 한 사용자 지정 임계값을 구성 하 여 다양 한 심각도 수준으로 경고를 트리거하여 touchless 리소스 모니터링 환경을 강화 합니다.

경고를 구성하려면
1. 부하 분산 장치에 대 한 경고 하위 블레이드로 이동 합니다.
1. 새 경고 규칙 만들기
    1.  경고 조건 구성
    1.  필드 자동 복구를 위한 작업 그룹 추가
    1.  직관적인 반응을 가능 하 게 하는 경고 심각도, 이름 및 설명 할당

  >[!NOTE]
  >경고 조건 구성 창에는 신호 기록에 대 한 시계열이 표시 됩니다. 이 시계열을 백 엔드 IP와 같은 차원으로 필터링 하는 옵션이 있습니다. 그러면 시간 계열 그래프가 필터링 되지만 경고 자체는 필터링 **되지** 않습니다. 특정 백 엔드 IP 주소에 대해서는 경고를 구성할 수 없습니다.

### <a name="common-diagnostic-scenarios-and-recommended-views"></a><a name = "DiagnosticScenarios"></a>일반적인 진단 시나리오 및 권장 보기

#### <a name="is-the-data-path-up-and-available-for-my-load-balancer-frontend"></a>데이터 경로는 Load Balancer 프런트 엔드에 사용할 수 있나요?
<details><summary>Expand</summary>

데이터 경로 가용성 메트릭은 Vm이 있는 계산 호스트에 대 한 지역 내 데이터 경로의 상태를 설명 합니다. 메트릭은 Azure 인프라의 상태를 반영한 것입니다. 이 메트릭으로 다음을 수행할 수 있습니다.
- 서비스의 외부 가용성 모니터링
- 서비스가 배포된 플랫폼이 정상 상태인지 여부 또는 게스트 OS 또는 애플리케이션 인스턴스가 정상 상태인지 여부를 심층 분석 및 이해
- 이벤트가 서비스와 관련된 것인지 또는 기본 데이터 평면과 관련된 것인지를 구분합니다. 상태 프로브 상태 ("백 엔드 인스턴스 가용성")와이 메트릭을 혼동 하지 마십시오.

표준 Load Balancer 리소스에 대 한 데이터 경로 가용성을 얻으려면 다음을 수행 합니다.
1. 올바른 부하 분산 장치 리소스를 선택했는지 확인합니다. 
2. **메트릭** 드롭다운 목록에서 **데이터 경로 가용성**을 선택 합니다. 
3. **집계** 드롭다운 목록에서 **Avg**를 선택합니다. 
4. 또한 프런트 엔드 IP 주소 또는 프런트 엔드 포트의 필터를 필요한 프런트 엔드 IP 주소 또는 프런트 엔드 포트를 사용 하는 차원으로 추가 하 고 선택한 차원에 따라 그룹화 합니다.

![VIP 검색](./media/load-balancer-standard-diagnostics/LBMetrics-VIPProbing.png)

*그림: Load Balancer 프런트 엔드 검색 세부 정보*

이 메트릭은 활성, 대역 내 측정을 통해 생성됩니다. 지역 내의 검색 서비스는 측정에 대한 트래픽을 발생합니다. 서비스는 공용 프런트 엔드로 배포를 만드는 즉시 활성화되고, 프런트 엔드를 제거할 때까지 계속됩니다. 

배포의 프런트 엔드 및 규칙과 일치하는 패킷이 주기적으로 생성됩니다. 원본에서 백 엔드 풀의 VM이 있는 호스트까지 해당 지역을 트래버스합니다. 부하 분산 장치 인프라는 다른 모든 트래픽의 경우와 동일하게 부하 분산 및 변환 작업을 수행합니다. 이 프로브는 대역 내의 부하 분산된 엔드포인트에 있습니다. 프로브가 백 엔드 풀의 정상 VM이 있는 Compute 호스트에 도착하면, 컴퓨팅 호스트는 검색 서비스에 대한 응답을 생성합니다. VM에는 이 트래픽이 표시되지 않습니다.

데이터 경로 가용성은 다음과 같은 이유로 실패 합니다.
- 배포의 백 엔드 풀에 정상 상태의 VM이 남아 있지 않습니다. 
- 인프라 중단이 발생했습니다.

진단 목적으로 [상태 프로브 상태와 함께 데이터 경로 가용성 메트릭을](#vipavailabilityandhealthprobes)사용할 수 있습니다.

대부분의 시나리오에서 집계로 **Average**를 사용합니다.
</details>

#### <a name="are-the-backend-instances-for-my-load-balancer-responding-to-probes"></a>내 Load Balancer에 대 한 백 엔드 인스턴스가 프로브에 응답 하나요?
<details>
  <summary>Expand</summary>
상태 프로브 상태 메트릭은 부하 분산 장치의 상태 프로브를 구성할 때 사용자가 구성한 애플리케이션 배포의 상태를 설명합니다. 부하 분산 장치는 상태 프로브의 상태를 사용하여 새 흐름을 보낼 위치를 결정합니다. 상태 프로브는 Azure 인프라 주소에서 시작되며, VM의 게스트 OS 내에서 볼 수 있습니다.

표준 Load Balancer 리소스에 대 한 상태 프로브 상태를 가져오려면:
1. **Avg** 집계 유형으로 **상태 프로브 상태** 메트릭을 선택 합니다. 
2. 필요한 프런트 엔드 IP 주소 또는 포트 (또는 둘 다)에 필터를 적용 합니다.

다음과 같은 이유로 상태 프로브가 실패합니다.
- 수신 또는 응답하지 않거나 잘못된 프로토콜을 사용하는 포트로 상태 프로브를 구성하는 경우. 서비스가 DSR(Direct Server Return 또는 부동 IP) 규칙을 사용하는 경우 서비스가 프런트 엔드 IP 주소로 구성된 루프백이 아니라 NIC의 IP 구성에서 수신 대기하고 있는지 확인해야 합니다.
- 프로브가 네트워크 보안 그룹, VM의 게스트 OS 방화벽 또는 애플리케이션 계층 필터에서 허용되지 않는 경우.

대부분의 시나리오에서 집계로 **Average**를 사용합니다.
</details>

#### <a name="how-do-i-check-my-outbound-connection-statistics"></a>내 아웃바운드 연결 통계는 어떻게 확인할 수 있나요? 
<details>
  <summary>Expand</summary>
SNAT 연결 메트릭은 [아웃바운드 흐름](https://aka.ms/lboutbound)에 대한 성공 및 실패 연결의 볼륨을 설명합니다.

실패한 연결 볼륨이 0보다 크면 SNAT 포트가 고갈된 것을 나타냅니다. 추가로 조사하여 이러한 실패의 원인을 확인해야 합니다. SNAT 포트 고갈은 [아웃바운드 흐름](https://aka.ms/lboutbound) 설정 실패를 나타냅니다. 아웃바운드 연결에 대한 문서를 검토하여 시나리오 및 작동 메커니즘을 이해하고, SNAT 포트 고갈 방지를 위한 완화 및 설계 방법을 알아봅니다. 

SNAT 연결 통계를 가져오려면:
1. **SNAT 연결** 메트릭 유형을 선택하고 집계로 **Sum**을 선택합니다. 
2. 다른 줄로 표시된 성공 및 실패 SNAT 연결 수를 **연결 상태**별로 그룹화합니다. 

![SNAT 연결](./media/load-balancer-standard-diagnostics/LBMetrics-SNATConnection.png)

*그림: Load Balancer SNAT 연결 수*
</details>


#### <a name="how-do-i-check-my-snat-port-usage-and-allocation"></a>내 SNAT 포트 사용 및 할당을 확인 어떻게 할까요??
<details>
  <summary>Expand</summary>
사용 된 SNAT 포트 메트릭은 아웃 바운드 흐름을 유지 하기 위해 사용 되는 SNAT 포트 수를 추적 합니다. 이는 부하 분산 장치 뒤에 있고 공용 IP 주소가 없는 가상 머신 확장 집합 또는 인터넷 원본과 백 엔드 VM 간에 설정 된 고유한 흐름 수를 나타냅니다. 할당 된 SNAT 포트 메트릭과 함께 사용 하는 SNAT 포트 수를 비교 하 여 서비스가 발생 하 고 있는지 또는 SNAT 고갈 및 아웃 바운드 흐름 오류로 인해 발생 하는 위험이 있는지 확인할 수 있습니다. 

메트릭이 [아웃 바운드 흐름](https://aka.ms/lboutbound) 오류 위험을 나타내는 경우이 문서를 참조 하 고 서비스 상태를 보장 하기 위해이를 완화 하는 단계를 수행 합니다.

SNAT 포트 사용 및 할당을 보려면:
1. 원하는 데이터가 표시 되도록 그래프의 시간 집계를 1 분으로 설정 합니다.
1. **사용 된 Snat 포트** 및/또는 **할당 된 snat 포트** 를 메트릭 유형 및 **평균** 으로 집계로 선택 합니다.
    * 기본적으로 이러한 메트릭은 TCP 및 UDP를 통해 집계 된 Load Balancer에 매핑된 모든 프런트 엔드 공용 Ip에 해당 하는 각 백 엔드 VM 또는 VMSS에 할당 되거나 사용 되는 평균 SNAT 포트 수입니다.
    * 에서 사용 하거나 부하 분산 장치에 할당 된 총 SNAT 포트를 보려면 메트릭 집계 **합계** 를 사용 합니다.
1. 특정 **프로토콜 유형**, **백 엔드 ip**집합 및/또는 **프런트 엔드 ip**를 필터링 합니다.
1. 백 엔드 또는 프런트 엔드 인스턴스당 상태를 모니터링 하려면 분할을 적용 합니다. 
    * 참고 분할을 사용 하면 단일 메트릭이 한 번에 표시 될 수 있습니다. 
1. 예를 들어 컴퓨터당 TCP 흐름에 대 한 SNAT 사용량을 모니터링 하려면 **평균**으로 집계 하 고 **백 엔드 ip** 로 분할 하 여 **프로토콜 유형별로**필터링 합니다. 

![SNAT 할당 및 사용량](./media/load-balancer-standard-diagnostics/snat-usage-and-allocation.png)

*그림: 백 엔드 Vm 집합의 평균 TCP SNAT 포트 할당 및 사용량*

![백 엔드 인스턴스당 SNAT 사용](./media/load-balancer-standard-diagnostics/snat-usage-split.png)

*그림: 백 엔드 인스턴스당 TCP SNAT 포트 사용*
</details>

#### <a name="how-do-i-check-inboundoutbound-connection-attempts-for-my-service"></a>내 서비스에 대한 인바운드/아웃바운드 연결 시도를 확인하려면 어떻게 해야 하나요?
<details>
  <summary>Expand</summary>
SYN 패킷 메트릭은 특정 프런트 엔드와 관련해서 도착했거나 전송된 ([아웃바운드 흐름](https://aka.ms/lboutbound) 관련) TCP SYN 패킷 볼륨을 설명합니다. 이 메트릭을 사용하여 서비스에 대한 TCP 연결 시도를 이해할 수 있습니다.

대부분의 시나리오에서 집계로 **Total**을 사용합니다.

![SYN 연결](./media/load-balancer-standard-diagnostics/LBMetrics-SYNCount.png)

*그림: Load Balancer SYN 수*
</details>


#### <a name="how-do-i-check-my-network-bandwidth-consumption"></a>내 네트워크 대역폭 소비를 어떻게 확인할 수 있나요? 
<details>
  <summary>Expand</summary>
바이트 및 패킷 카운터 메트릭은 프런트 엔드 기준으로 서비스에서 전송 또는 수신된 바이트 및 패킷 볼륨을 설명합니다.

대부분의 시나리오에서 집계로 **Total**을 사용합니다.

바이트 또는 패킷 수 통계를 가져오려면:
1. 집계가 **Avg**인 **바이트 수** 및/또는 **패킷 수** 메트릭 유형을 선택합니다. 
2. 다음 중 하나를 수행합니다.
   * 특정 프런트 엔드 IP, 프런트 엔드 포트, 백 엔드 IP 또는 백 엔드 포트에 필터를 적용합니다.
   * 필터링 없이 부하 분산 장치 리소스에 대한 전체 통계를 가져옵니다.

![바이트 수](./media/load-balancer-standard-diagnostics/LBMetrics-ByteCount.png)

*그림: Load Balancer 바이트 수*
</details>

#### <a name="how-do-i-diagnose-my-load-balancer-deployment"></a><a name = "vipavailabilityandhealthprobes"></a>내 부하 분산 장치 배포를 진단하려면 어떻게 해야 하나요?
<details>
  <summary>Expand</summary>
단일 차트에서 데이터 경로 가용성 및 상태 프로브 상태 메트릭의 조합을 사용 하 여 문제를 찾을 수 있는 위치를 식별 하 고 문제를 해결할 수 있습니다. Azure가 제대로 작동하고 있는지 확신하고, 이 정보를 토대로 구성 또는 애플리케이션이 근본 원인이라는 결론을 얻을 수 있습니다.

상태 프로브 메트릭을 사용하여 Azure에서 사용자가 제공한 구성을 기준으로 배포의 상태를 확인하는 방법을 이해할 수 있습니다. 상태 프로브를 확인하는 것은 항상 모니터링 또는 원인 파악에 도움이 되는 첫 단계입니다.

추가 단계를 수행 하 고 데이터 경로 가용성 메트릭을 사용 하 여 Azure에서 특정 배포를 담당 하는 기본 데이터 평면의 상태를 보는 방법에 대 한 통찰력을 얻을 수 있습니다. 두 메트릭에 함께 사용하면 이 예제에 설명된 대로 오류 위치를 격리시킬 수 있습니다.

![데이터 경로 가용성 및 상태 프로브 상태 메트릭 결합](./media/load-balancer-standard-diagnostics/lbmetrics-dipnvipavailability-2bnew.png)

*그림: 데이터 경로 가용성 및 상태 프로브 상태 메트릭 결합*

차트에는 다음 정보가 표시됩니다.
- Vm을 호스트 하는 인프라를 사용할 수 없으며 차트 시작 부분에 0%가 있습니다. 나중에 인프라가 정상적으로 작동 하 고 Vm에 연결할 수 있으며 백 엔드에 둘 이상의 VM이 배치 되었습니다. 이 정보는 데이터 경로 가용성에 대 한 파란색 추적으로 표시 되며,이는 나중에 100%입니다. 
- 자주색 추적으로 표시 되는 상태 프로브 상태는 차트의 시작 부분에서 0%입니다. 녹색의 원으로 표시 된 영역에는 상태 프로브 상태가 정상 상태가 되 고, 고객의 배포에서 새 흐름을 허용할 수 있는 위치가 표시 됩니다.

이 차트를 통해 고객은 다른 문제 발생 여부에 관계없이, 추측을 하거나 지원을 요청하지 않고 직접 배포 문제를 해결할 수 있습니다. 잘못된 구성 또는 실패한 애플리케이션 때문에 상태 프로브가 실패했으므로 서비스는 사용할 수 없었습니다.
</details>

## <a name="resource-health-status"></a><a name = "ResourceHealth"></a>Resource Health 상태

표준 Load Balancer **리소스에 대한 상태는****모니터 > 서비스 상태** 아래의 기존 를 통해 표시됩니다.

공용 표준 Load Balancer 리소스의 상태를 보려면:
1. **모니터**  >  **Service Health**를 선택 합니다.

   ![모니터 페이지](./media/load-balancer-standard-diagnostics/LBHealth1.png)

   *그림: Azure Monitor의 Service Health 링크*

2. **Resource Health**를 선택한 다음, **구독 ID** 및 **리소스 유형 = Load Balancer**를 선택했는지 확인합니다.

   ![Resource Health 상태](./media/load-balancer-standard-diagnostics/LBHealth3.png)

   *그림: 상태 보기에 대한 리소스 선택*

3. 목록에서 Load Balancer 리소스를 선택하여 기록 상태를 확인합니다.

    ![Load Balancer 상태](./media/load-balancer-standard-diagnostics/LBHealth4.png)

   *그림: Load Balancer 리소스 상태 보기*
 
일반 리소스 상태 설명은 [RHC 설명서](https://docs.microsoft.com/azure/service-health/resource-health-overview)에서 확인할 수 있습니다. Azure Load Balancer에 대 한 특정 상태는 아래 표에 나와 있습니다. 

| Resource Health 상태 | Description |
| --- | --- |
| 사용 가능 | 표준 부하 분산 장치 리소스가 정상 상태이 고 사용할 수 있습니다. |
| 성능 저하됨 | 표준 부하 분산 장치에는 성능에 영향을 주는 플랫폼 또는 사용자 시작 이벤트가 있습니다. 데이터 경로 가용성 메트릭이 90% 미만으로 보고 되었지만 최소 2 분 동안 25% 이상 상태를 초과 했습니다. 성능이 심각 하 게 저하 될 수 있습니다. [데이터 경로 가용성 가이드 문제 해결]을 참조 하 여 가용성에 영향을 주는 사용자 시작 이벤트가 있는지 여부를 확인 합니다.
| Unavailable | 표준 부하 분산 장치 리소스가 정상이 아닙니다. 데이터 경로 가용성 메트릭이 2 분 이상 25% 상태를 보고 했습니다. 인바운드 연결의 성능에 큰 영향을 주거나 가용성이 부족 합니다. 사용할 수 없는 사용자 또는 플랫폼 이벤트가 있을 수 있습니다. 사용자의 가용성에 영향을 주는 사용자 시작 이벤트가 있는지 확인 하려면 [데이터 경로 가용성 가이드 문제 해결 가이드]를 따르세요. |
| Unknown | 표준 부하 분산 장치 리소스에 대 한 리소스 상태가 아직 업데이트 되지 않았거나 지난 10 분 동안 데이터 경로 가용성 정보를 수신 하지 않았습니다. 이 상태는 일시적 이어야 하며, 데이터가 수신 되는 즉시 올바른 상태를 반영 합니다. |

## <a name="next-steps"></a>다음 단계

- [Standard Load Balancer](load-balancer-standard-overview.md)에 대해 자세히 알아보세요.
- [Load Balancer 아웃바운드 연결](https://aka.ms/lboutbound)에 대해 자세히 알아봅니다.
- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)에 대해 알아봅니다.
- [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) 및 [REST API를 통해 메트릭을 검색하는 방법](/rest/api/monitor/metrics/list)에 대해 자세히 알아봅니다.
