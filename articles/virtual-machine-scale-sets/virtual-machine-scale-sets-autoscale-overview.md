---
title: Azure 가상 머신 확장 집합을 사용한 자동 크기 조정 개요 | Microsoft Docs
description: 성능이나 확정된 일정에 따라 Azure 가상 머신 확장 집합의 크기를 자동으로 조정할 수 있는 다양한 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d29a3385-179e-4331-a315-daa7ea5701df
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 610f3073594f73f04a68865593be6bfb4188d4f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883673"
---
# <a name="overview-of-autoscale-with-azure-virtual-machine-scale-sets"></a>Azure 가상 머신 확장 집합을 사용한 자동 크기 조정 개요
Azure 가상 머신 확장 집합은 애플리케이션을 실행하는 VM 인스턴스의 수를 자동으로 늘리거나 줄입니다. 자동화되고 탄력적인 이 동작은 관리 오버헤드를 줄여 애플리케이션의 성능을 모니터링하고 최적화합니다. 긍정적인 고객 만족을 위해 허용되는 성능을 정의하는 규칙을 만듭니다. 정의된 임계값이 충족되면 자동 크기 조정 규칙에 따라 확장 집합의 용량을 조정하는 작업을 수행합니다. 확정된 시간에 확장 집합의 용량을 자동으로 늘리거나 줄이도록 이벤트를 예약할 수도 있습니다. 이 문서에서는 사용 가능한 성능 메트릭과 자동 크기 조정에서 수행할 수 있는 작업에 대해 간략히 설명합니다.


## <a name="benefits-of-autoscale"></a>자동 크기 조정의 이점
애플리케이션에 대한 요구가 증가하면 확장 집합의 VM 인스턴스에 대한 로드도 증가합니다. 증가된 로드가 단순한 요구가 아닌 일관된 요구인 경우 확장 집합의 VM 인스턴스 수를 늘리도록 자동 크기 조정 규칙을 구성할 수 있습니다.

이러한 VM 인스턴스가 만들어지고 애플리케이션이 배포되면 확장 집합에서 부하 분산 장치를 통해 트래픽을 분산하기 시작합니다. 모니터링할 메트릭(예: CPU 또는 메모리), 애플리케이션 로드가 지정된 임계값을 충족해야 하는 기간 및 확장 집합에 추가할 VM 인스턴스 수를 제어합니다.

저녁이나 주말에는 애플리케이션에 대한 요구가 줄어들 수 있습니다. 이 감소된 로드가 일정 기간 동안 일관성 있게 유지될 경우 확장 집합의 VM 인스턴스 수를 줄이도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이 규모 감축 작업은 현재 요구를 충족하는 데 필요한 인스턴스 수만 실행하므로 확장 집합을 실행하는 데 드는 비용을 줄여줍니다.


## <a name="use-host-based-metrics"></a>호스트 기반 메트릭 사용
VM 인스턴스에서 기본 제공 호스트 메트릭을 사용할 수 있는 자동 크기 조정 규칙을 만들 수 있습니다. 호스트 메트릭을 사용하면 추가 에이전트 및 데이터 컬렉션을 설치하거나 구성할 필요 없이 확장 집합의 VM 인스턴스 성능에 대한 가시성을 얻을 수 있습니다. 이러한 메트릭을 사용하는 자동 크기 조정 규칙은 CPU 사용량, 메모리 요구 또는 디스크 액세스에 대응하여 VM 인스턴스 수를 확장하거나 축소할 수 있습니다.

호스트 기반 메트릭을 사용하는 자동 크기 조정 규칙은 다음 도구 중 하나로 만들 수 있습니다.

- [Azure Portal](virtual-machine-scale-sets-autoscale-portal.md)
- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure 템플릿](tutorial-autoscale-template.md)

더 자세한 성능 메트릭을 사용하는 자동 크기 조정 규칙을 만들려면 [VM 인스턴스에서 Azure 진단 확장을 설치 및 구성](#in-guest-vm-metrics-with-the-azure-diagnostics-extension)하거나 [App Insights를 사용하도록 애플리케이션을 구성](#application-level-metrics-with-app-insights)할 수 있습니다.

호스트 기반 메트릭, Azure 진단 확장이 있는 게스트 내 VM 메트릭 및 App Insights를 사용하는 자동 크기 조정 규칙에서 사용할 수 있는 구성 설정은 다음과 같습니다.

### <a name="metric-sources"></a>메트릭 원본
자동 크기 조정 규칙은 다음 원본 중 하나의 메트릭을 사용할 수 있습니다.

| 메트릭 원본        | 사용 사례                                                                                                                     |
|----------------------|------------------------------------------------------------------------------------------------------------------------------|
| 현재 확장 집합    | 추가 에이전트를 설치하거나 구성할 필요가 없는 호스트 기반 메트릭의 경우                                  |
| Storage 계정      | Azure 진단 확장에서 Azure 저장소에 성능 메트릭을 기록한 다음 자동 크기 조정 규칙을 트리거하는 데 이 메트릭을 사용합니다. |
| Service Bus 큐    | 애플리케이션 또는 다른 구성 요소에서 Azure Service Bus 큐에 있는 메시지를 전송하여 규칙을 트리거 할 수 있습니다.                   |
| Application Insights | 애플리케이션에 설치되어 앱에서 메트릭을 직접 스트림하는 계측 패키지입니다.                         |


### <a name="autoscale-rule-criteria"></a>자동 크기 조정 규칙 조건
자동 크기 조정 규칙을 만들 때 사용할 수 있는 호스트 기반 메트릭은 다음과 같습니다. Azure 진단 확장 또는 App Insights를 사용하면 자동 크기 조정 규칙에서 모니터링하고 사용할 메트릭을 정의합니다.

| 메트릭 이름               |
|---------------------------|
| 백분율 CPU            |
| 네트워크 인                |
| 네트워크 아웃               |
| 디스크 읽기 바이트           |
| 디스크 쓰기 바이트          |
| 디스크 읽기 작업/초  |
| 디스크 쓰기 작업/초 |
| 남은 CPU 크레딧     |
| 사용된 CPU 크레딧      |

지정된 메트릭을 모니터링하는 자동 크기 조정 규칙을 만들면 규칙에서 다음 메트릭 집계 작업 중 하나를 확인합니다.

| 집계 유형 |
|------------------|
| 평균          |
| 최소          |
| 최대          |
| 합계            |
| 마지막             |
| 카운트            |

다음 연산자 중 하나를 사용하여 메트릭과 정의된 임계값을 비교하면 자동 크기 조정 규칙이 트리거됩니다.

| 연산자                 |
|--------------------------|
| 초과             |
| 다음보다 크거나 같음 |
| 다음보다 적음                |
| 다음보다 작거나 같음    |
| 다음과 같음                 |
| 다음과 같지 않음             |


### <a name="actions-when-rules-trigger"></a>규칙 트리거 시 작업
자동 크기 조정 규칙이 트리거되면 다음 방법 중 하나를 사용하여 확장 집합의 크기를 자동으로 조정할 수 있습니다.

| 크기 조정 작업     | 사용 사례                                                                                                                               |
|---------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| 다음을 기준으로 개수 늘이기   | 만들 VM 인스턴스의 고정된 수입니다. VM 수가 적은 확장 집합에 유용합니다.                                           |
| 다음을 기준으로 백분율 늘이기 | 백분율 기준에 따라 VM 인스턴스가 증가합니다. 고정된 증가에 따라 성능이 눈에 띄게 향상되지 않는 대규모 확장 집합에 적합합니다. |
| 다음으로 개수 늘이기   | 원하는 최대 크기에 도달하는 데 필요한 만큼의 VM 인스턴스를 만듭니다.                                                            |
| 다음을 기준으로 개수 줄이기   | 제거할 VM 인스턴스의 고정된 수입니다. VM 수가 적은 확장 집합에 유용합니다.                                           |
| 다음을 기준으로 백분율 줄이기 | 백분율 기준에 따라 VM 인스턴스가 감소합니다. 고정된 증가에 따라 리소스 사용량 및 비용이 눈에 띄게 줄어들지 않는 대규모 확장 집합에 적합합니다. |
| 다음으로 개수 줄이기   | 원하는 최소 크기에 도달하는 데 필요한 만큼의 VM 인스턴스를 제거합니다.                                                            |


## <a name="in-guest-vm-metrics-with-the-azure-diagnostics-extension"></a>Azure 진단 확장이 있는 게스트 내 VM 메트릭
Azure 진단 확장은 VM 인스턴스 내부에서 실행되는 에이전트입니다. 에이전트는 성능 메트릭을 모니터링하고 Azure 저장소에 저장합니다. 이러한 성능 메트릭에는 디스크에 대한 *AverageReadTime* 또는 CPU에 대한 *PercentIdleTime*과 같은 VM 상태에 대한 자세한 정보가 포함됩니다. CPU 사용률이나 메모리 사용량 외에도 VM 성능에 대한 자세한 인식에 따라 자동 크기 조정 규칙을 만들 수 있습니다.

Azure 진단 확장을 사용하려면 VM 인스턴스에 대한 Azure 스토리지 계정을 만들고, Azure 진단 에이전트를 설치한 다음, 특정 성능 카운터를 스토리지 계정에 스트림하도록 VM을 구성해야 합니다.

자세한 내용은 [Linux VM](../virtual-machines/extensions/diagnostics-linux.md) 또는 [Windows VM](../virtual-machines/extensions/diagnostics-windows.md)에서 Azure 진단 확장을 사용하도록 설정하는 방법에 대한 문서를 참조하세요.


## <a name="application-level-metrics-with-app-insights"></a>App Insights를 사용한 애플리케이션 수준 메트릭
애플리케이션 성능에 대한 가시성을 높이려면Application Insights를 사용할 수 있습니다. 앱을 모니터링하고 원격 분석을 Azure로 보내는 작은 계측 패키지를 애플리케이션에 설치합니다. 애플리케이션의 응답 시간, 페이지 로드 성능 및 세션 수와 같은 메트릭을 모니터링할 수 있습니다. 고객 만족에 영향을 줄 수 있는 실행 가능한 정보에 따라 규칙을 트리거할 때 이러한 애플리케이션 메트릭을 사용하여 세분화되고 포함된 수준의 자동 크기 조정 규칙을 만들 수 있습니다.

App Insights에 대한 자세한 내용은 [Application Insights란?](../azure-monitor/app/app-insights-overview.md)을 참조하세요.


## <a name="scheduled-autoscale"></a>예약된 자동 크기 조정
일정에 따라 자동 크기 조정 규칙을 만들 수도 있습니다. 이러한 일정 기반 규칙을 사용하면 고정된 시간에 VM 인스턴스 수를 자동으로 조정할 수 있습니다. 성능 기반 규칙을 사용하면 자동 크기 조정 규칙이 트리거되고 새 VM 인스턴스가 프로비전되기 전에 애플리케이션의 성능에 영향을 줄 수 있습니다. 이러한 요구를 예상할 수 있는 경우 VM 인스턴스가 추가로 프로비전되어 추가적인 고객 사용 및 애플리케이션 요구에 대비할 수 있습니다.

다음 예제는 일정 기반 자동 크기 규칙의 사용에 도움이 되는 시나리오입니다.

- 고객의 요구가 증가하는 경우 근무일의 시작 시 VM 인스턴스의 수를 자동으로 확장합니다. 근무일이 끝나면 애플리케이션을 많이 사용하지 않는 밤 동안의 리소스 비용을 최소화하기 위해 VM 인스턴스의 수를 자동으로 축소합니다.
- 부서에서 월 또는 회계 주기의 특정 부분에서 애플리케이션을 많이 사용하는 경우 VM 인스턴스의 수를 자동으로 조정하여 추가 요구를 수용합니다.
- 마케팅 이벤트, 판촉 또는 휴일 판매가 있는 경우 예상되는 고객 수요에 대비하여 VM 인스턴스의 수를 자동으로 조정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계
다음 도구 중 하나를 사용하여 호스트 기반 메트릭을 사용하는 자동 크기 조정 규칙을 만들 수 있습니다.

- [Azure PowerShell](tutorial-autoscale-powershell.md)
- [Azure CLI](tutorial-autoscale-cli.md)
- [Azure 템플릿](tutorial-autoscale-template.md)

이 개요에서는 자동 크기 조정 규칙을 사용하여 수평으로 크기를 조정하고, 확장 집합의 VM 인스턴스 *수*를 늘리거나 줄이는 방법에 대해 자세히 설명했습니다. 또한 수직으로 크기를 조정하여 VM 인스턴스의 *크기*를 늘리거나 줄일 수도 있습니다. 자세한 내용은 [가상 머신 확장 집합을 사용하여 수직으로 자동 크기 조정](virtual-machine-scale-sets-vertical-scale-reprovision.md)을 참조하세요.

VM 인스턴스 관리 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 가상 머신 확장 집합 관리](virtual-machine-scale-sets-windows-manage.md)를 참조하세요.

자동 크기 조정 규칙이 트리거될 때 경고를 생성하는 방법에 대한 자세한 내용은 [Azure Monitor에서 자동 크기 조정 작업을 사용하여 메일 및 webhook 경고 알림 보내기](../azure-monitor/platform/autoscale-webhook-email.md)를 참조하세요. [Azure Monitor에서 감사 로그를 사용하여 이메일 및 webhook 경고 알림을 보낼](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md) 수도 있습니다.
