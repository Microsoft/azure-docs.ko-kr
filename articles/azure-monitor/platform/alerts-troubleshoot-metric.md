---
title: Azure 메트릭 경고 문제 해결
description: Azure Monitor 메트릭 경고 및 가능한 해결 방법에 대 한 일반적인 문제
author: harelbr
ms.author: harelbr
ms.topic: reference
ms.date: 08/13/2020
ms.subservice: alerts
ms.openlocfilehash: 1c1fe613721ea2bc2d4b9665d6da73b094487efc
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88190672"
---
# <a name="troubleshooting-problems-in-azure-monitor-metric-alerts"></a>Azure Monitor 메트릭 경고 문제 해결 

이 문서에서는 Azure Monitor [메트릭 경고](alerts-metric-overview.md) 의 일반적인 문제 및 문제를 해결 하는 방법에 대해 설명 합니다.

Azure Monitor 경고는 모니터링 데이터에서 중요 한 조건이 발견 될 때 사용자에 게 사전에 알려 줍니다. 시스템 사용자가 문제를 알아채기 전에 경고를 통해 문제를 식별하여 해결할 수 있습니다. 경고에 대 한 자세한 내용은 [Microsoft Azure의 경고 개요](alerts-overview.md)를 참조 하세요.

## <a name="metric-alert-should-have-fired-but-didnt"></a>메트릭 경고가 발생 했지만 

메트릭 경고가 발생 한 것으로 예상 되었지만 실행 되지 않았고 Azure Portal에서 찾을 수 없는 경우 다음 단계를 수행 합니다.

1. **구성** -메트릭 경고 규칙 구성을 검토 하 여 제대로 구성 되었는지 확인 합니다.
    - **집계 유형**, **집계 세분성 (기간)** 및 **임계값** 또는 **민감도** 가 예상 대로 구성 되었는지 확인 합니다.
    - 동적 임계값을 사용 하는 경고 규칙의 경우 **위반 수가** 경고를 필터링 하 고 **데이터를 무시할** 수 있으므로 임계값을 계산 하는 방법에 영향을 줄 수 있으므로 고급 설정이 구성 되어 있는지 확인 합니다.

       > [!NOTE] 
       > 동적 임계값은 활성 상태가 되기 전에 3 일 이상 및 30 개의 메트릭 샘플이 필요 합니다.

2. 발생 **했지만 알림 없음** -발생 한 경고를 찾을 수 있는지 확인 하기 위해 [발생 한 경고 목록을](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) 검토 합니다. 목록에서 경고를 볼 수 있지만 일부 작업 또는 알림에 문제가 있는 경우 [여기](./alerts-troubleshoot.md#action-or-notification-on-my-alert-did-not-work-as-expected)에서 자세한 정보를 참조 하세요.

3. **이미 활성** -경고를 발생 시킬 것으로 예상 되는 메트릭 시계열에 이미 경고가 발생 했는지 확인 합니다. 메트릭 경고는 상태 저장 경고입니다. 즉, 특정 메트릭 시계열에서 경고가 발생하면 문제가 더 이상 관찰되지 않을 때까지 해당 시계열에 대한 추가 경고가 발생하지 않습니다. 이 디자인 선택에서는 노이즈가 줄어듭니다. 세 번의 연속 평가에 대해 경고 조건이 충족 되지 않으면 경고가 자동으로 해결 됩니다.

4. **사용 된 차원** - [메트릭에 대해 일부 차원 값](./alerts-metric-overview.md#using-dimensions)을 선택한 경우 경고 규칙은 임계값 위반에 대해 각 개별 메트릭 시계열 시계열 (차원 값의 조합에 의해 정의 됨)을 모니터링 합니다. 또한 차원이 선택 되지 않은 집계 메트릭 시계열을 모니터링 하려면 차원을 선택 하지 않고 메트릭에 대 한 경고 규칙을 추가로 구성 합니다.

5. **집계 및 시간 세분성** - [메트릭 차트](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)를 사용 하 여 메트릭을 시각화 하는 경우 다음을 확인 합니다.
    * 메트릭 차트에서 선택한 **집계가** 경고 규칙의 **집계 유형과** 동일 합니다.
    * 선택한 **시간 세분성** 이 경고 규칙의 **집계 세분성 (기간)** 과 동일 하 고 ' 자동 '으로 설정 되지 않습니다.

## <a name="metric-alert-fired-when-it-shouldnt-have"></a>메트릭 경고가 없을 때 발생 합니다.

메트릭 경고가 발생 해서는 안 되는 것으로 판단 되 면 다음 단계를 통해 문제를 해결할 수 있습니다.

1. 발생 한 경고 [목록을](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/alertsV2) 검토 하 여 발생 한 경고를 찾은 후 클릭 하 여 세부 정보를 확인 합니다. **이 경고가 발생 한 이유** 에서 제공 된 정보를 검토 하 여 경고가 트리거된 시점의 메트릭 차트, **메트릭 값**및 **임계값** 을 확인 하십시오.

    > [!NOTE] 
    > 동적 임계값 조건 유형을 사용 하 고 사용 된 임계값이 올바르지 않은 것으로 생각 되는 경우 찡그린 얼굴 보내기 아이콘을 사용 하 여 피드백을 제공 하세요. 이 피드백은 기계 학습 알고리즘 연구에 영향을 주므로 향후 검색을 개선 하는 데 도움이 됩니다.

2. 메트릭에 대해 여러 차원 값을 선택한 경우 차원 값의 조합으로 정의 된 메트릭 시계열 중 **하나라도** 임계값을 위반 하면 경고가 트리거됩니다. 메트릭 경고의 차원 사용에 대한 자세한 내용은 [여기](./alerts-metric-overview.md#using-dimensions)를 참조하세요.

3. 경고 규칙 구성을 검토 하 여 제대로 구성 되었는지 확인 합니다.
    - **집계 유형**, **집계 세분성 (기간)** 및 **임계값** 또는 **민감도** 가 예상 대로 구성 되었는지 확인 합니다.
    - 동적 임계값을 사용 하는 경고 규칙의 경우 **위반 수가** 경고를 필터링 하 고 **데이터를 무시할** 수 있으므로 임계값을 계산 하는 방법에 영향을 줄 수 있으므로 고급 설정이 구성 되어 있는지 확인 합니다.

   > [!NOTE]
   > 동적 임계값은 활성 상태가 되기 전에 3 일 이상 및 30 개의 메트릭 샘플이 필요 합니다.

4. [메트릭 차트](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/metrics)를 사용 하 여 메트릭을 시각화 하는 경우 다음을 확인 합니다.
    - 메트릭 차트에서 선택한 **집계가** 경고 규칙의 **집계 유형과** 동일 합니다.
    - 선택한 **시간 세분성** 이 경고 규칙의 **집계 세분성 (기간)** 과 동일 하 고 ' 자동 '으로 설정 되지 않습니다.

5. 같은 조건을 모니터링 하는 경고가 이미 발생 한 상태에서 경고가 발생 한 경우 (확인 되지 않음) *autoMitigate* 속성을 **false** 로 설정 하 여 경고 규칙을 구성 했는지 확인 합니다 .이 속성은 REST/PowerShell/CLI를 통해서만 구성할 수 있으므로 경고 규칙을 배포 하는 데 사용 되는 스크립트를 확인 합니다. 이 경우 경고 규칙은 발생 된 경고를 자동으로 해결 하지 않으며 발생 한 경고를 다시 발생 시키기 전에 해결 해야 합니다.


## <a name="cant-find-the-metric-to-alert-on---virtual-machines-guest-metrics"></a>가상 컴퓨터 게스트 메트릭에 대해 경고 하는 메트릭을 찾을 수 없습니다.

가상 컴퓨터의 게스트 운영 체제 메트릭에 대해 경고 하려면 (예: 메모리, 디스크 공간) 메트릭을 Azure Monitor 하기 위해이 데이터를 수집 하는 데 필요한 에이전트를 설치 했는지 확인 합니다.
- [Windows VM의 경우](./collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM의 경우](./collect-custom-metrics-linux-telegraf.md)

가상 컴퓨터의 게스트 운영 체제에서 데이터를 수집 하는 방법에 대 한 자세한 내용은 [여기](../insights/monitor-vm-azure.md#guest-operating-system)를 참조 하세요.
    
> [!NOTE] 
> Log Analytics 작업 영역으로 전송 되도록 게스트 메트릭을 구성한 경우 메트릭은 Log Analytics 작업 영역 리소스 아래에 표시 되 고이를 모니터링 하는 경고 규칙을 만든 후에 **만** 데이터를 표시 하기 시작 합니다. 이렇게 하려면 [로그에 대한 메트릭 경고를 구성](./alerts-metric-logs.md#configuring-metric-alert-for-logs)하는 단계를 수행합니다.

## <a name="cant-find-the-metric-to-alert-on"></a>경고에 대 한 메트릭을 찾을 수 없음

특정 메트릭에 대 한 경고를 찾고 리소스에 대 한 메트릭을 볼 수 없는 경우에는 해당 [리소스 유형이 메트릭 경고에 대해 지원 되는지 확인](./alerts-metric-near-real-time.md)합니다.
리소스에 대 한 일부 메트릭을 볼 수 있지만 특정 메트릭을 찾을 수 없는 경우 [해당 메트릭을 사용할 수 있는지 여부를 확인](./metrics-supported.md)하 고, 해당 메트릭을 사용할 수 있는지 확인 하려면 메트릭 설명을 참조 하 여 리소스의 특정 버전이 나 버전 에서만 사용할 수 있는지 확인 합니다.

## <a name="cant-find-the-metric-dimension-to-alert-on"></a>경고에 대 한 메트릭 차원을 찾을 수 없습니다.

[메트릭의 특정 차원 값](./alerts-metric-overview.md#using-dimensions)에 대 한 경고를 표시 하지만 이러한 값을 찾을 수 없는 경우 다음을 참고 하세요.

1. **차원 값** 목록에 차원 값이 표시되는 데 몇 분 정도 걸릴 수 있습니다.
1. 표시되는 차원 값은 지난 3일 동안 수집된 메트릭 데이터를 기반으로 합니다.
1. 차원 값을 아직 내보내지 않은 경우 ' + ' 기호를 클릭 하 여 사용자 지정 값을 추가 합니다.
1. 이후의 값을 포함 하 여 차원의 가능한 모든 값에 대해 경고 하려면 ' Select * ' 확인란을 선택 합니다.

## <a name="metric-alert-rules-still-defined-on-a-deleted-resource"></a>삭제 된 리소스에 대 한 메트릭 경고 규칙이 계속 정의 되어 있습니다. 

Azure 리소스를 삭제하면 연결된 메트릭 경고 규칙이 자동으로 삭제되지 않습니다. 삭제 된 리소스와 연결 된 경고 규칙을 삭제 하려면 다음을 수행 합니다.

1. 삭제된 리소스가 정의된 리소스 그룹을 엽니다.
1. 리소스를 표시하는 목록에서 **숨겨진 유형 표시** 확인란을 선택합니다.
1. 유형 == **microsoft metricalerts/** 별로 목록을 필터링합니다.
1. 관련 경고 규칙을 선택 하 고 **삭제** 를 선택 합니다.

## <a name="make-metric-alerts-occur-every-time-my-condition-is-met"></a>내 조건이 충족 될 때마다 메트릭 경고가 발생 하도록 설정

메트릭 경고는 기본적으로 상태 저장 이므로 지정 된 시계열에 이미 발생 한 경고가 있으면 추가 경고가 발생 하지 않습니다. 특정 메트릭 경고 규칙의 상태 비저장을 설정 하 고 경고 조건이 충족 되는 모든 평가에 대해 경고를 표시 하려면 프로그래밍 방식으로 (예: [리소스 관리자](./alerts-metric-create-templates.md), [PowerShell](/powershell/module/az.monitor/?view=azps-3.6.1), [REST](/rest/api/monitor/metricalerts/createorupdate), [CLI](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)를 통해) 경고 규칙을 만들고 *autoMitigate* 속성을 ' f a l s e '로 설정 합니다.

> [!NOTE] 
> 메트릭 경고 규칙 상태 비저장을 설정 하면 발생 한 경고가 해결 되지 않습니다. 따라서 조건이 더 이상 충족 되지 않은 경우에도 발생 한 경고는 30 일의 보존 기간까지 발생 한 상태로 유지 됩니다.

## <a name="define-an-alert-rule-on-a-custom-metric-that-isnt-emitted-yet"></a>아직 내보내지 않은 사용자 지정 메트릭에 대 한 경고 규칙을 정의 합니다.

메트릭 경고 규칙을 만들 때 메트릭 [정의 API](/rest/api/monitor/metricdefinitions/list) 에 대 한 메트릭 이름 유효성을 검사 하 여 존재 하는지 확인 합니다. 경우에 따라 사용자 지정 메트릭에 대 한 경고 규칙을 만들 수도 있습니다. 예를 들어 리소스 관리자 템플릿을 사용 하 여 사용자 지정 메트릭을 내보내는 Application Insights 리소스를 만들고 해당 메트릭을 모니터링 하는 경고 규칙을 사용할 수 있습니다.

사용자 지정 메트릭의 정의 유효성을 검사 하는 동안 배포가 실패 하지 않도록 하려면 경고 규칙의 조건 섹션에서 *skipMetricValidation* 매개 변수를 사용할 수 있습니다. 그러면 메트릭 유효성 검사가 생략 됩니다. 리소스 관리자 템플릿에서이 매개 변수를 사용 하는 방법은 아래 예제를 참조 하세요. 자세한 내용은 [메트릭 경고 규칙을 만들기 위한 전체 리소스 관리자 템플릿 예제](./alerts-metric-create-templates.md)를 참조 하세요.

```json
"criteria": {
    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
        "allOf": [
            {
                    "name" : "condition1",
                        "metricName": "myCustomMetric",
                "metricNamespace": "myCustomMetricNamespace",
                        "dimensions":[],
                        "operator": "GreaterThan",
                        "threshold" : 10,
                        "timeAggregation": "Average",
                    "skipMetricValidation": true
        }
              ]
        }
```

## <a name="export-the-azure-resource-manager-template-of-a-metric-alert-rule-via-the-azure-portal"></a>Azure Portal를 통해 메트릭 경고 규칙의 Azure Resource Manager 템플릿을 내보냅니다.

메트릭 경고 규칙의 리소스 관리자 템플릿을 내보내면 해당 JSON 구문과 속성을 이해 하 고 향후 배포를 자동화 하는 데 사용할 수 있습니다.
1. 포털에서 **리소스 그룹** 섹션으로 이동 하 고 규칙을 포함 하는 리소스 그룹을 선택 합니다.
2. 개요 섹션에서 **숨겨진 형식 표시** 확인란을 선택 합니다.
3. **유형** 필터에서 *microsoft metricalerts/* 를 선택 합니다.
4. 관련 경고 규칙을 선택 하 여 세부 정보를 확인 합니다.
5. **설정**아래에서 **템플릿 내보내기**를 선택 합니다.

## <a name="metric-alert-rules-quota-too-small"></a>메트릭 경고 규칙 할당량이 너무 작음

구독 당 허용 되는 메트릭 경고 규칙 수는 할당량 한 [도](../service-limits.md)에 따라 결정 됩니다.

할당량 한도에 도달 하면 다음 단계를 통해 문제를 해결할 수 있습니다.
1. 더 이상 사용 되지 않는 메트릭 경고 규칙을 삭제 하거나 사용 하지 않도록 설정 하세요.

2. 여러 리소스를 모니터링하는 메트릭 경고 규칙을 사용하도록 전환합니다. 이 기능을 사용 하면 단일 경고 규칙이 할당량에 대해 계산 된 하나의 경고 규칙만 사용 하 여 여러 리소스를 모니터링할 수 있습니다. 이 기능과 지원되는 리소스 유형에 대한 자세한 내용은 [여기](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor)를 참조하세요.

3. 할당량 제한을 늘려야 하는 경우 지원 요청을 열고 다음 정보를 제공 합니다.

    - 할당량 제한을 늘려야 하는 구독 Id입니다.
    - 할당량 증가에 대 한 리소스 유형: **메트릭 경고** 또는 **메트릭 경고 (클래식)**
    - 요청된 할당량 한도

## <a name="check-total-number-of-metric-alert-rules"></a>메트릭 경고 규칙의 총 수를 확인 합니다.

메트릭 경고 규칙의 현재 사용량을 확인 하려면 다음 단계를 수행 합니다.

### <a name="from-the-azure-portal"></a>Azure Portal에서

1. **경고** 화면을 열고 **경고 규칙 관리**를 클릭합니다.
2. **구독** 드롭다운 컨트롤을 사용 하 여 관련 구독으로 필터링
3. 특정 리소스 그룹, 리소스 유형 또는 리소스로 필터링 하지 않도록 합니다.
4. **신호 형식** 드롭다운 컨트롤에서 **메트릭** 을 선택 합니다.
5. **상태** 드롭다운 컨트롤이 **사용** 으로 설정 되어 있는지 확인 합니다.
6. 메트릭 경고 규칙의 총 수가 경고 규칙 목록 위에 표시 됩니다.

### <a name="from-api"></a>API에서

- PowerShell - [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2?view=azps-3.7.0)
- REST API - [구독별 목록](/rest/api/monitor/metricalerts/listbysubscription)
- Azure CLI - [az monitor metrics alert list](/cli/azure/monitor/metrics/alert?view=azure-cli-latest#az-monitor-metrics-alert-list)

## <a name="managing-alert-rules-using-resource-manager-templates-rest-api-powershell-or-azure-cli"></a>리소스 관리자 템플릿, REST API, PowerShell 또는 Azure CLI를 사용 하 여 경고 규칙 관리

리소스 관리자 템플릿, REST API, PowerShell 또는 Azure CLI (명령줄 인터페이스)를 사용 하 여 메트릭 경고를 생성, 업데이트, 검색 또는 삭제 하는 데 문제가 발생 하는 경우 다음 단계를 사용 하 여 문제를 해결할 수 있습니다.

### <a name="resource-manager-templates"></a>리소스 관리자 템플릿

- [일반적인 Azure 배포 오류](../../azure-resource-manager/templates/common-deployment-errors.md) 목록을 검토하고 그에 따라 문제를 해결합니다.
- 모든 매개 변수를 올바르게 전달 하 고 있는지 확인 하려면 [메트릭 경고 Azure Resource Manager 템플릿 예제](./alerts-metric-create-templates.md) 를 참조 하세요.

### <a name="rest-api"></a>REST API

[REST API 가이드](/rest/api/monitor/metricalerts/) 를 검토 하 여 모든 매개 변수를 올바르게 전달 하 고 있는지 확인 합니다.

### <a name="powershell"></a>PowerShell

메트릭 경고에 대 한 올바른 PowerShell cmdlet을 사용 하 고 있는지 확인 합니다.

- 메트릭 경고에 대한 PowerShell cmdlet은 [Az.Monitor 모듈](/powershell/module/az.monitor/?view=azps-3.6.1)에서 사용할 수 있습니다.
- 새 (비 클래식) 메트릭 경고 (예: [AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2?view=azps-3.6.1))에 대해 ' V2 '로 끝나는 cmdlet을 사용 해야 합니다.

### <a name="azure-cli"></a>Azure CLI

메트릭 경고에 대 한 올바른 CLI 명령을 사용 하 고 있는지 확인 합니다.

- 메트릭 경고에 대한 CLI 명령은 `az monitor metrics alert`로 시작합니다. [Azure CLI 참조](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)를 검토하여 구문에 대해 알아봅니다.
- [메트릭 경고 CLI를 사용하는 방법을 보여주는 샘플](./alerts-metric.md#with-azure-cli)을 볼 수 있습니다.
- 사용자 지정 메트릭에 대해 경고하려면 다음과 같이 메트릭 이름 앞에 관련 메트릭 네임스페이스를 접두사로 지정해야 합니다. NAMESPACE.METRIC

### <a name="general"></a>일반

- 오류를 수신 하는 경우 `Metric not found` :

   - 플랫폼 메트릭의 경우: **메트릭 표시 이름이** 아니라 [지원 되는 Azure Monitor 메트릭 페이지](./metrics-supported.md)의 **메트릭** 이름을 사용 하 고 있는지 확인 하세요.

   - 사용자 지정 메트릭의 경우: 메트릭이 이미 내보내지고 있는지 확인 하 고 (아직 존재 하지 않는 사용자 지정 메트릭에는 경고 규칙을 만들 수 없음) 사용자 지정 메트릭의 네임 스페이스를 제공 하 고 있는지 확인 합니다 ( [여기](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-a-custom-metric)리소스 관리자 템플릿 예제 참조).

- [로그에 대 한 메트릭 경고](./alerts-metric-logs.md)를 만드는 경우 적절 한 종속성이 포함 되어 있는지 확인 합니다. [샘플 템플릿](./alerts-metric-logs.md#resource-template-for-metric-alerts-for-logs)을 참조하세요.

- 여러 조건을 포함 하는 경고 규칙을 만드는 경우 다음 제약 조건을 확인 합니다.

   - 각 조건 내에서 차원당 하나의 값만 선택할 수 있습니다.
   - "\*"를 차원 값으로 사용할 수 없습니다.
   - 다른 조건에서 구성 된 메트릭이 동일한 차원을 지 원하는 경우 구성 된 차원 값은 이러한 모든 메트릭에 대해 동일한 방식으로 명시적으로 설정 되어야 합니다 ( [여기](./alerts-metric-create-templates.md#template-for-a-static-threshold-metric-alert-that-monitors-multiple-criteria)리소스 관리자 템플릿 예제 참조).


## <a name="no-permissions-to-create-metric-alert-rules"></a>메트릭 경고 규칙을 만들 수 있는 권한 없음

메트릭 경고 규칙을 만들려면 다음과 같은 사용 권한이 있어야 합니다.

- 경고 규칙의 대상 리소스에 대 한 읽기 권한
- 경고 규칙이 생성 되는 리소스 그룹에 대 한 쓰기 권한 (Azure Portal에서 경고 규칙을 만드는 경우 대상 리소스가 있는 동일한 리소스 그룹에서 경고 규칙을 만듦)
- 경고 규칙에 연결 된 모든 작업 그룹에 대 한 읽기 권한 (해당 하는 경우)


## <a name="naming-restrictions-for-metric-alert-rules"></a>메트릭 경고 규칙에 대 한 명명 제한

메트릭 경고 규칙 이름에 대 한 다음 제한 사항을 고려 하세요.

- 메트릭 경고 규칙 이름을 만든 후에는 변경 (이름 변경) 할 수 없습니다.
- 메트릭 경고 규칙 이름은 리소스 그룹 내에서 고유 해야 합니다.
- 메트릭 경고 규칙 이름에는 * # & +:  < >  문자를 사용할 수 없습니다. @ % { } \ / 
- 메트릭 경고 규칙 이름은 문자 ()로 끝날 수 없습니다.


## <a name="restrictions-when-using-dimensions-in-a-metric-alert-rule-with-multiple-conditions"></a>여러 조건이 있는 메트릭 경고 규칙에서 차원을 사용할 때의 제한 사항

메트릭 경고는 다중 차원 메트릭에 대 한 경고를 지원 하 고 여러 조건을 정의 하는 것을 지원 합니다 (경고 규칙 당 최대 5 개 조건).

여러 조건을 포함 하는 경고 규칙에서 차원을 사용 하는 경우 다음 제약 조건을 고려해 야 합니다.
- 각 조건 내에서 차원 당 하나의 값만 선택할 수 있습니다.
- "현재 및 미래 값 모두 선택" (Select) 옵션을 사용할 수 없습니다 \* .
- 서로 다른 조건에서 구성 된 메트릭이 동일한 차원을 지 원하는 경우 구성 된 차원 값은 해당 하는 모든 메트릭에 대해 동일한 방식으로 명시적으로 설정 되어야 합니다 (관련 조건).
예를 들어:
    - 저장소 계정에 정의 된 메트릭 경고 규칙을 고려 하 고 두 가지 조건을 모니터링 합니다.
        * 총 **트랜잭션** > 5
        * 평균 **SuccessE2ELatency** > 250 밀리초
    - 첫 번째 조건을 업데이트 하 고 **Apiname** 차원이 *"getblob"* 과 일치 하는 트랜잭션만 모니터링 하 고 싶습니다.
    - **트랜잭션과** **SuccessE2ELatency** 메트릭은 모두 **apiname** 차원을 지원 하므로 두 조건을 모두 업데이트 해야 하며 둘 다 *"Getblob"* 값을 가진 **apiname** 차원을 지정 해야 합니다.


## <a name="next-steps"></a>다음 단계

- 경고 및 알림에 대 한 일반적인 문제 해결 정보는 [Azure Monitor 경고의 문제 해결](alerts-troubleshoot.md)을 참조 하세요.
