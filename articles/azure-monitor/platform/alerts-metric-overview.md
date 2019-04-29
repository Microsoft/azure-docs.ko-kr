---
title: Azure Monitor에서 메트릭 경고가 작동하는 방식을 이해합니다.
description: 메트릭 경고로 수행할 수 있는 작업과 Azure Monitor에서 메트릭 경고가 작동하는 방식에 대한 개요를 제공합니다.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 59973d9530bf1c3ab3e77290b25e50860f9de0ca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712868"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Azure Monitor에서 메트릭 경고가 작동하는 방식 이해

Azure Monitor에서 메트릭 경고는 다차원 메트릭을 기반으로 작동합니다. 이 메트릭은 [플랫폼 메트릭](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [사용자 지정 메트릭](../../azure-monitor/platform/metrics-custom-overview.md), [메트릭으로 변환되는 Azure Monitor의 주요 로그](../../azure-monitor/platform/alerts-metric-logs.md) 및 Application Insights 메트릭일 수 있습니다. 메트릭 경고는 주기적으로 하나 이상의 메트릭 시계열에서 조건이 참인지 평가하고 평가에 부합하면 사용자에게 알립니다. 메트릭 경고는 상태를 저장합니다. 즉 상태가 변경될 때만 알림을 보냅니다.

## <a name="how-do-metric-alerts-work"></a>메트릭 경고 작동 방식

모니터링할 대상 리소스, 메트릭 이름, 조건 형식(정적 또는 동적) 및 조건(연산자 및 임계값/민감도), 경고 규칙 실행 시에 트리거할 작업 그룹을 지정하여 메트릭 경고 규칙을 정의할 수 있습니다. 조건 형식은 임계값을 결정하는 방법에 영향을 줍니다. [동적 임계값 조건 형식 및 민감도 옵션에 대해 자세히 알아보세요](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>정적 조건 형식이 있는 경고 규칙

다음과 같이 간단한 정적 임계값 메트릭 경고 규칙을 만들었다고 가정해 보겠습니다.

- 대상 리소스(모니터링할 Azure 리소스): myVM
- 메트릭: 백분율 CPU
- 조건 형식: 공용
- 시간 집계(원시 메트릭 값에 대해 실행되는 통계. 지원되는 시간 집계는 최소, 최대, 평균, 총계): 평균
- 기간(확인할 메트릭 값에 대한 되돌아 보기 시간 범위): 지난 5분
- 빈도(메트릭 경고에서 조건이 충족되는지 확인하는 빈도): 1분
- 연산자: 다음보다 큼
- 임계값: 70

경고 규칙이 만들어진 시점 이후 모니터는 1분 간격으로 실행되며 지난 5분 간의 메트릭 값을 살펴 이 평균이 70을 초과하는지 확인합니다. 조건에 부합하면, 즉 지난 5분의 평균 백분율 CPU가 70을 초과하면 경고 규칙에서 활성화된 알림을 생성합니다. 경고 규칙과 연결된 작업 그룹에서 이메일이나 웹후크 작업을 구성한 경우 모두 활성화된 알림을 받게 됩니다.

### <a name="alert-rule-with-dynamic-condition-type"></a>동적 조건 형식이 있는 경고 규칙

다음과 같이 간단한 동적 임계값 메트릭 경고 규칙을 만들었다고 가정해 보겠습니다.

- 대상 리소스(모니터링할 Azure 리소스): myVM
- 메트릭: 백분율 CPU
- 조건 형식: 동적
- 시간 집계(원시 메트릭 값에 대해 실행되는 통계. 지원되는 시간 집계는 최소, 최대, 평균, 총계): 평균
- 기간(확인할 메트릭 값에 대한 되돌아 보기 시간 범위): 지난 5분
- 빈도(메트릭 경고에서 조건이 충족되는지 확인하는 빈도): 1분
- 연산자: 다음보다 큼
- 민감도: 중간
- 되돌아 보기 기간: 4
- 위반 수: 4

경고 규칙이 만들어지면 동적 임계값 기계 학습 알고리즘에서 사용 가능한 기록 데이터를 가져오고 메트릭 계열 동작 패턴에 가장 적합한 임계값을 계산하며, 새 데이터를 기반으로 하여 지속적으로 학습하여 임계값을 더 정확하게 만듭니다.

경고 규칙이 만들어지는 시점부터 모니터는 1분마다 실행되고, 5분 기간으로 그룹화된 지난 20분 동안의 메트릭 값을 파악하며, 각 4개 기간에 대한 기간 값의 평균이 예상 임계값을 초과하는지 확인합니다. 지난 20분(4개의 5분 단위 기간)의 평균 CPU 백분율이 예상된 동작에서 네 번 벗어나는 조건이 충족되면 경고 규칙에서 활성화된 알림을 실행합니다. 경고 규칙과 연결된 작업 그룹에서 이메일이나 웹후크 작업을 구성한 경우 모두 활성화된 알림을 받게 됩니다.

### <a name="view-and-resolution-of-fired-alerts"></a>실행된 경고 보기 및 해결

위의 경고 규칙 실행 예는 Azure Portal의 **모든 경고** 블레이드에서도 확인할 수 있습니다.

후속 검사에서 "myVM"의 사용량이 임계값을 계속 초과하면 해당 조건이 해결될 때까지 경고 규칙이 다시 실행되지 않습니다.

일정 시간 후 "myVM"의 사용량이 정상 상태, 즉 임계값 미만으로 떨어집니다. 경고 규칙은 해결 알림을 보내기 위해 두 번 이상 조건을 모니터링합니다. 조건이 유동적인 경우 소음을 줄이기 위해 세 번 연속 경고 조건에 부합하지 않으면 경고 규칙에서 해결/비활성화 메시지를 보냅니다.

해결됨 알림은 웹후크 또는 이메일을 통해 전송되므로 Azure Portal의 경고 인스턴스 상태(모니터 상태라고도 함)도 해결됨으로 설정됩니다.

### <a name="using-dimensions"></a>차원 사용

Azure Monitor의 메트릭 경고는 하나의 규칙을 사용하여 여러 차원의 값 조합을 모니터링하는 기능도 지원합니다. 예제를 통해 여러 차원 값 조합을 사용할 수 있는 이유를 살펴 보겠습니다.

웹 사이트에 대해 App Service 계획은 있다고 가정합니다. 웹 사이트/앱을 실행하는 여러 인스턴스의 CPU 사용량을 모니터링하려 합니다. 이 작업은 다음과 같은 메트릭 경고 규칙을 사용하여 수행할 수 있습니다.

- 대상 리소스: myAppServicePlan
- 메트릭: 백분율 CPU
- 조건 형식: 공용
- 차원
  - 인스턴스 = InstanceName1, InstanceName2
- 시간 집계: 평균
- 기간: 지난 5분
- 빈도: 1분
- 연산자: GreaterThan
- 임계값: 70

이전과 같이 이 규칙은 최근 5분 동안 평균 CPU 사용량이 70%를 초과하는지 모니터링합니다. 그러나 같은 규칙으로 웹 사이트에서 실행되는 두 인스턴스를 모니터링할 수 있습니다. 각 인스턴스는 개별적으로 모니터링되며 알림도 개별적으로 받게 됩니다.

수요가 큰 웹앱이 있고 다른 인스턴스를 더 추가해야 한다고 가정합니다. 위 규칙에서는 두 인스턴스만 모니터링합니다. 그러나 다음과 같이 규칙을 만들 수 있습니다.

- 대상 리소스: myAppServicePlan
- 메트릭: 백분율 CPU
- 조건 형식: 공용
- 차원
  - 인스턴스 = *
- 시간 집계: 평균
- 기간: 지난 5분
- 빈도: 1분
- 연산자: GreaterThan
- 임계값: 70

이 규칙은 자동으로 인스턴스의 모든 값을 모니터링합니다. 즉 메트릭 경고 규칙을 다시 수정하지 않고도 그대로 인스턴스를 모니터링할 수 있습니다.

여러 차원을 모니터링하는 경우 동적 임계값 경고 규칙은 한 번에 수백 개의 메트릭 계열에 대한 맞춤형 임계값을 만들 수 있습니다. 동적 임계값으로 인해 관리할 경고 규칙이 줄어들고 경고 규칙의 관리 및 만들기에 걸리는 시간이 크게 절약할 수 있습니다.

많은 인스턴스가 있는 웹앱이 있고 가장 적합한 임계값을 알지 못한다고 가정합니다. 위의 규칙에서는 항상 70%의 임계값을 사용합니다. 그러나 다음과 같이 규칙을 만들 수 있습니다.

- 대상 리소스: myAppServicePlan
- 메트릭: 백분율 CPU
- 조건 형식: 동적
- 차원
  - 인스턴스 = *
- 시간 집계: 평균
- 기간: 지난 5분
- 빈도: 1분
- 연산자: GreaterThan
- 민감도: 중간
- 되돌아 보기 기간: 1
- 위반 수: 1

이 규칙은 지난 5분 동안의 평균 CPU 사용량이 각 인스턴스에 대해 예상된 동작을 초과하는지 모니터링합니다. 메트릭 경고 규칙을 다시 수정할 필요 없이 동일한 규칙에 따라 발생하는 인스턴스를 모니터링할 수 있습니다. 각 인스턴스에서 메트릭 계열 동작 패턴에 맞는 임계값을 가져오고, 새 데이터에 따라 지속적으로 변경되어 임계값을 더 정확하게 만듭니다. 이전과 마찬가지로 각 인스턴스는 개별적으로 모니터링되며 알림도 개별적으로 받게 됩니다.

되돌아 보기 기간과 위반 수를 늘리면 필터링 경고에서 크게 벗어나는 중요한 위반에 대한 정의만 경고하도록 허용할 수 있습니다. [동적 임계값 고급 옵션에 대해 자세히 알아보세요](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Azure Monitor에서 메트릭 경고를 사용한 모니터링 축소

지금까지 단일 메트릭 경고를 사용하여 단일 Azure 리소스와 관련된 하나 이상의 메트릭 시계열을 모니터링하는 방법을 확인했습니다. 많은 리소스에 동일한 경고 규칙을 적용하려는 경우가 많습니다. Azure Monitor는 하나의 경고 규칙으로 여러 리소스를 모니터링할 수도 있습니다. 이 기능은 현재 가상 머신에서만 지원됩니다. 또한 단일 메트릭 경고가 한 Azure 지역의 리소스를 모니터링할 수 있습니다.

다음 세 가지 방법 중 하나로 단일 메트릭 경고를 통해 모니터링 범위를 지정할 수 있습니다.

- 구독 내 한 Azure 지역에 있는 가상 머신 목록으로
- 구독 내 하나 이상의 리소스 그룹에 있는 모든 가상 머신(한 Azure 지역에 있는)
- 구독의 모든 가상 머신(한 Azure 지역에 있는)

여러 리소스를 모니터링하는 메트릭 경고 규칙을 만드는 작업은 단일 리소스를 모니터링하는 [다른 메트릭 경고를 만드는](alerts-metric.md) 것과 같습니다. 차이점은 모니터링하려는 모든 리소스를 사용자가 선택한다는 것입니다. [Azure Resource Manager 템플릿](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources)을 통해 이러한 규칙을 만들 수도 있습니다. 각 가상 머신에 대한 개별 알림을 받게 됩니다.

## <a name="typical-latency"></a>일반적인 대기 시간

메트릭 경고의 경우, 일반적으로 경고 규칙 빈도를 1분으로 설정하면 5분 안에 알림을 받게 됩니다. 알림 시스템에 부하가 높으면 대기 시간이 더 길어집니다.

## <a name="supported-resource-types-for-metric-alerts"></a>메트릭 경고에 대한 지원되는 리소스 종류

지원되는 리소스 종류의 전체 목록은 이 [문서](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)에서 확인할 수 있습니다.

현재는 클래식 메트릭 경고를 사용하는데 메트릭 경고가 사용 중인 모든 리소스 종류를 지원하는지를 확인하려는 경우 아래 표를 참조하세요. 이 표에는 클래식 메트릭 경고가 지원하는 리소스 종류, 그리고 이러한 리소스 종류가 현재 메트릭 경고에서 지원되는지 여부가 나와 있습니다.

|클래식 메트릭 경고가 지원하는 리소스 종류 | 메트릭 경고의 지원 여부 |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | 예 |
| Microsoft.Batch/batchAccounts| 예|
|Microsoft.Cache/redis| 예 |
|Microsoft.ClassicCompute/virtualMachines | 아닙니다. |
|Microsoft.ClassicCompute/domainNames/slots/roles | 아닙니다.|
|Microsoft.CognitiveServices/accounts | 아닙니다. |
|Microsoft.Compute/virtualMachines | 예|
|Microsoft.Compute/virtualMachineScaleSets| 예|
|Microsoft.ClassicStorage/storageAccounts| 아닙니다. |
|Microsoft.DataFactory/datafactories | 예|
|Microsoft.DBforMySQL/servers| 예|
|Microsoft.DBforPostgreSQL/servers| 예|
|Microsoft.Devices/IotHubs | 아닙니다.|
|Microsoft.DocumentDB/databaseAccounts| 예|
|Microsoft.EventHub/namespaces | 예|
|Microsoft.Logic/workflows | 예|
|Microsoft.Network/loadBalancers |예|
|Microsoft.Network/publicIPAddresses| 예|
|Microsoft.Network/applicationGateways| 예|
|Microsoft.Network/expressRouteCircuits| 예|
|Microsoft.Network/trafficManagerProfiles | 예|
|Microsoft.Search/searchServices | 예|
|Microsoft.ServiceBus/namespaces| 예 |
|Microsoft.Storage/storageAccounts | 예|
|Microsoft.StreamAnalytics/streamingjobs| 예|
|Microsoft.TimeSeriesInsights/environments | 예|
|Microsoft. Web/serverfarms | 예 |
|Microsoft. Web/sites(함수 제외) | 예|
|Microsoft. Web/hostingEnvironments/multiRolePools | 아닙니다.|
|Microsoft. Web/hostingEnvironments/workerPools| 아닙니다. |
|Microsoft.SQL/Servers | 아닙니다. |

## <a name="next-steps"></a>다음 단계

- [Azure에서 메트릭 경고를 생성, 표시 및 관리하는 방법에 대해 알아봅니다](alerts-metric.md).
- [Azure Resource Manager 템플릿을 사용한 메트릭 경고 배포 방법 알아보기](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [작업 그룹에 대해 자세히 알아보기](action-groups.md)
- [동적 임계값 조건 형식에 대해 자세히 알아봅니다](alerts-dynamic-thresholds.md).
