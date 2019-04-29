---
title: Azure Monitor의 메트릭 | Microsoft Docs
description: 모니터링 데이터를 거의 실시간 시나리오를 지원할 수 있는 간단한는 Azure Monitor의 메트릭에 대해 설명 합니다.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: c00f703c5cfa606eaeb6ea0dea5fe5d754d3de5d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108087"
---
# <a name="metrics-in-azure-monitor"></a>Azure Monitor의 메트릭

> [!NOTE]
> Azure Monitor 데이터 플랫폼은 두 가지 기본 데이터 형식을 기반으로 합니다. 메트릭 및 로그 합니다. 이 문서에는 메트릭을 설명합니다. 가리킵니다 [Azure Monitor의 로그](data-platform-logs.md) 로그와 자세한 설명은 [Azure Monitor 데이터 platforn](data-platform.md) 두 비교 합니다.

Azure Monitor의 메트릭은 간단 하 고 거의 실시간 시나리오에 특히 유용 있도록를 지원할 수 있는 문제의 경고 하 고 빠르게 검색 합니다. 이 문서에서는 메트릭 구조화는 방식과,를 사용 하 여 수행할 수 있는 작업에 대해 설명 하 고 메트릭에서 데이터를 저장 하는 다양 한 데이터 소스를 식별 합니다.

## <a name="what-are-metrics"></a>메트릭이 무엇인가요?
메트릭은 특정 시간에 시스템의 일부 측면을 설명하는 숫자 값입니다. 메트릭은 정기적으로 수집 및 자주 샘플링 될 수 있습니다 비교적 간단한 논리를 사용 하 여 경고를 신속 하 게 발생 하기 때문에 경고 하는 데 유용 합니다.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>Azure Monitor 메트릭으로 합니까?
다음 표에서 Azure Monitor에서 메트릭 데이터를 사용할 수 있는 다양 한 방법을 나열 합니다.

|  |  |
|:---|:---|
| 분석 | 사용 하 여 [메트릭 탐색기](metrics-charts.md) 차트에서 수집 된 메트릭을 분석 하 고 다른 리소스의 메트릭을 비교 합니다. |
| 시각화 | 메트릭 탐색기에서 차트를 고정 된 [Azure 대시보드에](../learn/tutorial-app-dashboards.md)합니다.<br>만들기는 [통합 문서](../app/usage-workbooks.md) 여러 대화형 보고서에서 데이터 집합으로 결합 합니다. 쿼리 결과 내보냅니다 [Grafana](grafana-plugin.md) 해당 대시보드를 활용 하 여 다른 데이터 원본으로 결합 합니다. |
| 경고 | 구성 된 [메트릭 경고 규칙](alerts-metric.md) 알림을 보내거나를 사용 하는 [작업을 자동화](action-groups.md) 메트릭 값을 임계값을 초과 하는 경우. |
| 자동화 |  사용 하 여 [자동 크기 조정](autoscale-overview.md) 늘리거나 줄이려면 임계값을 초과 하는 메트릭 값을 기반으로 하는 리소스입니다. |
| 내보내기 | [로그에 메트릭 라우팅](diagnostic-logs-stream-log-store.md) Azure Monitor 로그에서 데이터와 함께 Azure Monitor 메트릭 데이터를 분석 하 고 93 일 이상에 대 한 메트릭 값을 저장 합니다.<br>메트릭을 Stream을 [Event Hub](stream-monitoring-data-event-hubs.md) 외부 시스템으로 라우팅하도록 합니다. |
| 장치 | 메트릭 값을 사용 하 여 명령줄에서 액세스 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/az.applicationinsights)<br>메트릭 값을 사용 하 여 사용자 지정 응용 프로그램에서 액세스할 [REST API](rest-api-walkthrough.md)합니다.<br>사용 하 여 명령줄에서 메트릭 값에 액세스할 [CLI](/cli/azure/monitor/metrics)합니다. |
| 보관 | 규정 준수, 감사 또는 오프라인 보고의 목적으로 리소스의 성능 또는 상태 기록을 [보관](..//learn/tutorial-archive-data.md)합니다. |

## <a name="how-is-data-in-azure-monitor-metrics-structured"></a>Azure Monitor 메트릭 구성에 대 한 데이터 요금은 어떻게 되나요?
Azure Monitor 메트릭에 의해 수집 된 데이터는 타임 스탬프 데이터 분석을 위해 최적화 된 시계열 데이터베이스에 저장 됩니다. 메트릭 값의 각 집합은 다음 속성을 사용 하 여 시계열:

* 값을 수집 된 시간
* 와 연결 된 리소스 값
* 메트릭에 대 한 범주 처럼 작동 하는 네임 스페이스
* 메트릭 이름
* 값 자체
* 에 설명 된 대로 몇 가지 메트릭을 여러 차원이 있을 [다차원 메트릭](#multi-dimensional-metrics)합니다. 사용자 지정 메트릭에는 최대 10개의 차원이 있을 수 있습니다.

## <a name="multi-dimensional-metrics"></a>다차원 메트릭
메트릭 데이터 과제 중 하나는 종종 제한적으로 수집 된 값에 대 한 컨텍스트를 제공 하는 정보입니다. Azure Monitor는 다차원 메트릭 사용 하 여이 문제를 해결합니다. 메트릭의 차원은 메트릭 값을 설명하도록 추가 데이터를 전송하는 이름 값 쌍입니다. 예를 들어, 메트릭을 _사용 가능한 디스크 공간_ 라는 차원이 있을 수 있습니다 _드라이브_ 값을 사용 하 여 _c:_ 를 _d:_ 를 볼 수 있는 하거나 모든 사용 가능한 디스크 공간 드라이브 또는 각 드라이브 개별적으로 합니다.

아래 예제에서는 _네트워크 처리량_이라는 가상의 메트릭에 대한 두 개의 데이터 세트를 보여줍니다. 첫 번째 데이터 세트에는 차원이 없습니다. 두 번째 데이터 세트에는 두 가지 차원, 즉 _IP 주소_와 _방향_(Direction)의 값이 표시됩니다.

### <a name="network-throughput"></a>네트워크 처리량

| 타임 스탬프     | 메트릭 값 |
| ------------- |:-------------|
| 8/9/2017 8:14 | 1,331.8Kbps |
| 8/9/2017 8:15 | 1,141.4Kbps |
| 8/9/2017 8:16 | 1,110.2Kbps |

이 비차원 메트릭은 "지정된 시간에 내 네트워크 처리량은 무엇이었나요?"와 같은 기본적인 질문에만 응답할 수 있습니다.

### <a name="network-throughput--two-dimensions-ip-and-direction"></a>네트워크 처리량 +2 차원("IP" 및 "방향")

| 타임 스탬프     | 차원 "IP"   | 차원 "방향" | 메트릭 값|
| ------------- |:-----------------|:------------------- |:-----------|
| 8/9/2017 8:14 | IP="192.168.5.2" | 방향="전송"    | 646.5Kbps |
| 8/9/2017 8:14 | IP="192.168.5.2" | 방향="수신" | 420.1Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="전송"    | 150.0Kbps |
| 8/9/2017 8:14 | IP="10.24.2.15"  | 방향="수신" | 115.2Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 방향="전송"    | 515.2Kbps |
| 8/9/2017 8:15 | IP="192.168.5.2" | 방향="수신" | 371.1Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="전송"    | 155.0Kbps |
| 8/9/2017 8:15 | IP="10.24.2.15"  | 방향="수신" | 100.1Kbps |

이 메트릭은 "각 IP 주소에 대한 네트워크 처리량은 무엇이었나요?", "전송된 데이터 및 수신된 데이터의 양은 얼마인가요?"와 같은 질문에 대답할 수 있습니다. 다차원 메트릭은 비차원 메트릭에 대해 추가 분석 및 진단 값을 가집니다.

## <a name="interacting-with-azure-monitor-metrics"></a>Azure Monitor 메트릭 상호 작용
사용 하 여 [메트릭 탐색기](metrics-charts.md) 를 대화형으로 메트릭 데이터베이스의 데이터를 분석 하 고 시간이 지남에 따라 여러 가지 메트릭의 값을 차트입니다. 다른 시각화 요소를 사용 하 여 보려는 대시보드에 차트를 고정할 수 있습니다. [Azure 모니터링 REST API](rest-api-walkthrough.md)를 사용하여 메트릭을 검색할 수도 있습니다.

![메트릭 탐색기](media/data-platform/metrics-explorer.png)

## <a name="sources-of-azure-monitor-metrics"></a>Azure Monitor 메트릭 원본
Azure Monitor에서 수집되는 메트릭의 세 가지 기본 원본이 있습니다. 이러한 메트릭은 Azure Monitor 메트릭 데이터베이스에 수집 되 면 평가할 수 있습니다 함께 해당 원본에 관계 없이 합니다.

**플랫폼 메트릭**은 Azure 리소스에서 생성되고 해당 상태 및 성능에 대한 가시성을 제공합니다. 각 리소스 유형은 필요한 구성 없이 [고유 메트릭 집합](metrics-supported.md)을 만듭니다. 메트릭 정의에 달리 지정 되지 않은 플랫폼 메트릭은 1 분 빈도로 Azure 리소스에서 수집 됩니다. 

**게스트 OS 메트릭** 가상 머신의 게스트 운영 체제에서 수집 됩니다. 사용 하 여 Windows 가상 머신에 대 한 게스트 OS 메트릭 사용 [Windows 진단 확장 (WAD)](../platform/diagnostics-extension-overview.md) 및 사용 하 여 Linux 가상 머신의 [InfluxData Telegraf 에이전트](https://www.influxdata.com/time-series-platform/telegraf/)합니다.

**애플리케이션 메트릭**은 모니터링된 애플리케이션에 대한 Application Insights에 의해 생성되며 성능 문제를 감지하고 애플리케이션이 사용되는 추세를 추적하는 데 도움이 됩니다. _서버 응답 시간_ 및 _브라우저 예외_와 같은 값을 포함합니다.

**사용자 지정 메트릭** 메트릭이 자동으로 사용할 수 있는 표준 메트릭 외에도 정의 합니다. 할 수 있습니다 [응용 프로그램에서 사용자 지정 메트릭을 정의](../app/api-custom-events-metrics.md) Application Insights에 의해 모니터링 되는 또는 사용 하 여 Azure 서비스에 대 한 사용자 지정 메트릭을 만들 합니다 [사용자 지정 메트릭 API](metrics-store-custom-rest-api.md)합니다.

## <a name="retention-of-metrics"></a>메트릭의 보존
Azure에서 대부분의 리소스에 대 한 메트릭은 93 일 동안 저장 됩니다. 일부의 예외가 있습니다.
  * **클래식 게스트 OS 메트릭**합니다. 클래식 게스트 OS 메트릭 14 일간 유지 됩니다. 장기 보존을 위해 사용 하 여 수집 되는 새 게스트 OS 메트릭을 사용 하 여 권장 [Windows 진단 확장 (WAD)](../platform/diagnostics-extension-overview.md) 및 사용 하 여 Linux 가상 머신의 [InfluxData Telegraf 에이전트](https://www.influxdata.com/time-series-platform/telegraf/)합니다.
  * **Application Insights 로그 기반 메트릭**합니다. 내부를 들여다보면 [로그 기반 메트릭](../app/pre-aggregated-metrics-log-metrics.md) 로그 쿼리로 변환 합니다. 해당 보존 기본 로그에서 이벤트의 보존을 찾습니다. Application Insights 리소스에 대 한 로그는 90 일 동안 저장 됩니다. 

> [!NOTE]
> 할 수 있습니다 [플랫폼 메트릭을 Azure Monitor 리소스에 대 한 Log Analytics 작업 영역으로 보낼](diagnostic-logs-stream-log-store.md) 장기 추세에 대 한 합니다.

## <a name="next-steps"></a>다음 단계

- 에 대 한 자세한 정보는 [Azure Monitor 데이터 플랫폼](data-platform.md)합니다.
- 에 대 한 자세한 [Azure Monitor의 데이터를 로그](data-platform-logs.md)합니다.
- Azure의 다양한 리소스에 [사용 가능한 모니터링 데이터](data-sources.md)에 대해 알아봅니다.
