---
title: 데이터 컬렉션 규칙을 사용하여 VM 인사이트 게스트 상태에서 모니터링 구성(미리 보기)
description: Resource Manager 템플릿을 사용하여 대규모로 VM 인사이트 게스트 상태에서 기본 모니터링을 수정하는 방법을 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 889a04d68de45a6270ae0c38615d841a526ad86a
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490695"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>데이터 컬렉션 규칙을 사용하여 VM 인사이트 게스트 상태에서 모니터링 구성(미리 보기)
[VM 인사이트 게스트 상태](vminsights-health-overview.md)를 사용하면 일정한 간격으로 샘플링되는 성능 측정 세트에 정의된 대로 가상 머신의 상태를 볼 수 있습니다. 이 문서에서는 데이터 컬렉션 규칙을 사용하여 여러 가상 머신에서 기본 모니터링을 수정하는 방법을 설명합니다.


## <a name="monitors"></a>모니터
가상 머신의 상태는 각 모니터의 [상태 롤업](vminsights-health-overview.md#health-rollup-policy)에 따라 결정됩니다. 다음 표에 표시된 것처럼 VM 인사이트 게스트 상태에는 두 가지 유형의 모니터가 있습니다.

| 모니터 | Description |
|:---|:---|
| 유닛 모니터 | 리소스 또는 애플리케이션의 일부 측면을 측정합니다. 리소스의 성능 또는 가용성을 확인하기 위해 성능 카운터를 검사할 수 있습니다. |
| 집계 모니터 | 여러 모니터를 그룹화하여 집계된 단일 성능 상태를 제공합니다. 집계 모니터에는 하나 이상의 단위 모니터와 기타 집계 모니터가 포함될 수 있습니다. |

VM 인사이트 게스트 상태 및 해당 구성에서 사용하는 모니터 세트는 직접 변경할 수 없습니다. 기본 구성의 동작을 수정하는 경우 [재정의](#overrides)를 만들 수 있습니다. 재정의는 데이터 컬렉션 규칙에 정의되어 있습니다. 필요한 모니터링 구성을 얻기 위해 여러 재정의를 포함하는 여러 데이터 컬렉션 규칙을 만들 수 있습니다.

## <a name="monitor-properties"></a>모니터 속성
다음 표에서는 각 모니터에서 구성할 수 있는 속성에 대해 설명합니다.

| 속성 | 모니터 | Description |
|:---|:---|:---|
| 사용 | 집계<br>단위 | True인 경우 상태 모니터가 계산되어 가상 머신의 상태에 기여합니다. 경고를 사용하도록 설정된 경고를 트리거할 수 있습니다. |
| 경고 | 집계<br>단위 | True인 경우 비정상 상태로 이동할 때 모니터에 대한 경고가 트리거됩니다. False인 경우 모니터의 상태는 여전히 경고를 트리거할 수 있는 가상 머신의 상태에 영향을 줍니다. |
| 경고 | 단위 | 경고 상태에 대한 조건입니다. 없는 경우 모니터가 경고 상태로 전환되지 않습니다. |
| 위험 | 단위 | 위험 상태에 대한 조건입니다. 없는 경우 모니터가 위험 상태로 전환되지 않습니다. |
| 평가 빈도 | 단위 | 성능 상태를 평가하는 빈도입니다. |
| 되돌아보기 | 단위 | 되돌아보기 창의 크기(초)입니다. 자세한 설명은 [monitorConfiguration 요소](#monitorconfiguration-element)를 참조하세요. |
| 평가 유형 | 단위 | 샘플 세트에서 사용할 값을 정의합니다. 자세한 설명은 [monitorConfiguration 요소](#monitorconfiguration-element)를 참조하세요. |
| 최소 샘플 | 단위 | 값을 계산하는 데 사용할 값의 최소 수입니다. |
| 최대 샘플 | 단위 | 값을 계산하는 데 사용할 값의 최대 수입니다. |


## <a name="default-configuration"></a>기본 구성
다음 표에는 각 모니터의 기본 구성이 나열되어 있습니다. 이 기본 구성은 직접 변경할 수 없지만 특정 가상 머신에 대한 모니터 구성을 수정하는 [재정의](#overrides)를 정의할 수 있습니다.


| 모니터 | 사용 | 경고 | 경고 | 위험 | 평가 빈도 | 되돌아보기 | 평가 유형 | 최소 샘플 | 최대 샘플 |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU 사용률  | True | 거짓 | 없음 | \> 90%    | 60초 | 240초 | 최소값 | 2 | 3 |
| 사용 가능한 메모리 | True | 거짓 | 없음 | \< 100MB | 60초 | 240초 | 최대 | 2 | 3 |
| 파일 시스템      | True | 거짓 | 없음 | \< 100MB | 60초 | 120초 | 최대 | 1 | 1 |


## <a name="overrides"></a>재정의
*재정의* 는 모니터의 속성을 하나 이상 변경합니다. 예를 들어 재정의는 기본적으로 사용하도록 설정된 모니터를 사용하지 않도록 설정하거나, 모니터에 대한 경고 조건을 정의하거나, 모니터의 위험 임계값을 수정할 수 있습니다. 

재정의는 [DCR(데이터 컬렉션 규칙)](../agents/data-collection-rule-overview.md)에 정의되어 있습니다. 서로 다른 재정의 세트를 사용하여 여러 개의 DCR을 만들고 여러 가상 머신에 적용할 수 있습니다. [Azure Monitor 에이전트에 대한 데이터 컬렉션 구성(미리 보기)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations)에 설명된 대로 연결을 만들어 가상 머신에 DCR을 적용합니다.


## <a name="multiple-overrides"></a>다중 재정의

단일 모니터에 여러 재정의가 있을 수 있습니다. 재정의에서 다른 속성을 정의하는 경우 결과 구성은 모든 재정의의 조합입니다.

예를 들어 `memory|available` 모니터는 경고 임계값을 지정하거나 경고를 기본적으로 사용하도록 설정하지 않습니다. 이 모니터에 적용되는 다음 재정의를 고려하십시오.

- 재정의 1은 `alertConfiguration.isEnabled` 속성 값을 `true`로 정의합니다.
- 재정의 2는 `monitorConfiguration.warningCondition`을 `< 250`의 임계값 조건으로 정의합니다.

따라서 250Mb 미만의 메모리가 사용 가능하고 심각도 2 경고를 생성하는 경우 경고 상태로 전환되고, 100Mb 미만의 메모리가 사용 가능하고 심각도 1을 생성(또는 이미 있는 경우 기존 경고를 심각도 2에서 1로 변경)하는 경우 위험 상태로 전환되는 모니터가 구성됩니다.

두 재정의가 동일한 모니터에서 동일한 속성을 정의하는 경우 하나의 값이 우선적으로 적용됩니다. 재정의는 해당 [범위](#scopes-element)에 따라 적용되며 가장 일반적인 것에서 가장 구체적인 것까지 적용됩니다. 즉, 가장 구체적인 재정의가 적용될 가능성이 가장 큽니다. 구체적인 순서는 다음과 같습니다.

1. 전역 
2. Subscription
3. Resource group
4. 가상 머신 

동일한 범위 수준에서 여러 재정의가 동일한 모니터의 동일한 속성을 정의하는 경우 DCR에 표시되는 순서대로 적용됩니다. 재정의가 다른 DCR에 있는 경우 DCR 리소스 ID의 알파벳 순서로 적용됩니다.


## <a name="data-collection-rule-configuration"></a>데이터 컬렉션 규칙 구성
다음 섹션에서는 재정의를 정의하는 데이터 컬렉션 규칙의 JSON 요소에 대해 설명합니다. 전체 예제는 [샘플 데이터 컬렉션 규칙](#sample-data-collection-rule)에 나와 있습니다.

## <a name="extensions-structure"></a>확장 구조
게스트 상태는 Azure Monitor 에이전트에 대한 확장으로 구현되므로 재정의는 데이터 컬렉션 규칙의 `extensions` 요소에 정의됩니다. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| 요소 | 필수 | 설명 |
|:---|:---|:---|
| `name` | 예 | 확장에 대한 사용자 정의 문자열입니다. |
| `streams` | 예 | 게스트 상태 데이터가 전송될 스트림의 목록입니다. **Microsoft-HealthStateChange** 를 포함해야 합니다.  |
| `extensionName` | 예 | 확장의 이름입니다. **HealthExtension** 이어야 합니다. |
| `extensionSettings` | 예 | 기본 구성에 적용되는 `healthRuleOverride` 요소의 배열입니다. |


## <a name="extensionsettings-element"></a>extensionSettings 요소
확장에 대한 설정을 포함합니다.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| 요소 | 필수 | 설명 |
|:---|:---|:---|
| `schemaVersion` | 예 | 요소의 예상 스키마를 나타내기 위해 Microsoft에서 정의하는 문자열입니다. 현재 1.0으로 설정되어 있어야 합니다. |
| `contentVersion` | 아니요 | 필요한 경우 다양한 버전의 상태 구성을 추적하기 위해 사용자가 정의한 문자열입니다. |
| `healthRuleOverrides` | 예 | 기본 구성에 적용되는 `healthRuleOverride` 요소의 배열입니다. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides 요소
각각 재정의를 정의하는 하나 이상의 `healthRuleOverride` 요소를 포함합니다.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| 요소 | 필수 | 설명 |
|:---|:---|:---|
| `scopes` | 예 | 이 재정의를 적용할 수 있는 가상 머신을 지정하는 하나 이상의 범위 목록입니다. DCR이 가상 머신과 연결되어 있더라도 재정의를 적용하려면 가상 머신이 범위 내에 있어야 합니다. |
| `monitors` | 예 | 이 재정의를 받을 모니터를 정의하는 하나 이상의 문자열 목록입니다.  |
| `monitorConfiguration` | 아니요 | 상태 및 계산 방법을 포함한 모니터에 대한 구성입니다. |
| `alertConfiguration` | 아니요 | 모니터에 대한 경고 구성입니다. |
| `isEnabled` | 아니요 | 모니터 사용 여부를 제어합니다. 사용하지 않도록 설정된 모니터는 특별히 *사용 안 함* 상태로 전환되고, 다시 활성화하지 않는 한 비활성화됩니다. 생략하는 경우 모니터는 계층의 부모 모니터에서 해당 상태를 상속합니다. |


## <a name="scopes-element"></a>범위 요소
각 재정의에는 재정의를 적용해야 하는 가상 머신을 정의하는 범위가 하나 이상 있습니다. 범위는 구독, 리소스 그룹 또는 단일 가상 머신일 수 있습니다. 재정의가 특정 가상 머신에 연결된 DCR에 있는 경우라도, 가상 머신이 재정의 범위 중 하나 내에 있으면 해당 가상 머신에만 적용됩니다. 이를 통해 더 적은 수의 DCR을 VM 세트에 광범위하게 연결할 수 있지만, DCR 자체 내에서 각 재정의의 할당에 대한 세부적인 제어를 제공합니다. 범위 요소를 사용하여 해당 가상 머신의 다른 하위 집합에 대해 상태 모니터 재정의를 지정하는 반면, 정책을 사용하여 작은 DCR 세트를 만들고 가상 머신 세트에 연결할 수도 있습니다.

다음 표에는 다양한 범위의 예제가 나와 있습니다.

| 범위 | 예제 |
|:---|:---|
| 단일 가상 머신 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| 리소스 그룹의 모든 가상 머신 | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| 구독의 모든 가상 머신 | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| 데이터 컬렉션 규칙이 연결된 모든 가상 머신 | `*` |


## <a name="monitors-element"></a>모니터 요소
이 재정의를 받을 상태 계층 구조의 모니터를 정의하는 하나 이상의 문자열 목록입니다. 각 요소는 이 재정의를 받을 하나 이상의 모니터와 일치하는 모니터 이름 또는 유형 이름일 수 있습니다. 

```json
"monitors": [
    "<monitor name>"
 ],
```



다음 표에는 현재 사용 가능한 모니터 이름이 나열되어 있습니다.

| 형식 이름 | 속성 | 설명 |
|:----------|:-----|:------------|
| 루트 | 루트 | 가상 머신 상태를 나타내는 최상위 모니터입니다. |
| cpu-utilization | cpu-utilization | CPU 사용량 모니터. |
| logical-disks | logical-disks | Windows 가상 머신에서 모니터링되는 모든 디스크의 상태에 대한 집계 모니터입니다. |
| logical-disks\|\* | logical-disks\|C:<br>logical-disks\|D: | Windows 가상 머신에서 지정된 디스크의 상태를 추적하는 집계 모니터입니다. |
| logical-disks\|\*\|free-space | logical-disks\|C:\|free-space<br>logical-disks\|D:\|free-space | Windows 가상 머신에서 사용 가능한 디스크 공간 모니터입니다. |
| filesystems | filesystems | Linux 가상 머신에 있는 모든 파일 시스템의 상태에 대한 집계 모니터입니다. |
| filesystems\|\* | filesystems\|/<br>filesystems\|/mnt | Linux 가상 머신의 파일 시스템에 대한 상태를 추적하는 집계 모니터입니다. |
| filesystems\|\*\|free-space | filesystems\|/\|free-space<br>filesystems\|/mnt\|free-space | Linux 가상 머신 파일 시스템의 디스크 사용 가능한 디스크 공간 모니터입니다. |
| 메모리 | 메모리 | 가상 머신 메모리의 상태에 대한 집계 모니터입니다. |
| memory\|available | memory\|available | 가상 머신에서 사용 가능한 메모리를 추적하는 모니터입니다. |


## <a name="alertconfiguration-element"></a>alertConfiguration 요소
모니터에서 경고를 만들지 여부를 지정합니다.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| 요소 | 필수 | Description | 
|:---|:---|:---|
| `isEnabled` | 아니요 | True로 설정된 경우 모니터는 위험 또는 경고 상태로 전환될 때 경고를 생성하고 정상 상태로 돌아갈 때 경고를 해결합니다. False이거나 생략된 경우 경고가 생성되지 않습니다.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration 요소
유닛 모니터에만 적용됩니다. 상태 및 계산 방법을 포함한 모니터에 대한 구성을 정의합니다.

매개 변수는 임계값과 비교할 메트릭 값을 계산하는 알고리즘을 정의합니다. 모니터는 기본 메트릭에 있는 하나의 데이터 샘플을 사용하는 대신 평가 시간 및 `lookbackSec`시간 전 기간 내에서 받은 몇 가지 메트릭 샘플을 평가합니다. 해당 시간 범위 내에 수신되는 모든 샘플이 고려되며 샘플 수가 `maxSamples`보다 크면 위의 이전 샘플 `maxSamples`가 무시됩니다. 

되돌아보기 간격의 샘플이 `minSamples`보다 적을 경우 모니터는 기본 메트릭의 상태에 대한 의사 결정을 내릴 수 있는 충분 한 데이터가 없다는 것을 *알 수 없음* 상태로 전환하여 표시합니다. 그런 다음, `minSamples`보다 더 큰 수의 샘플을 사용할 수 있을 때 경우 evaluationType 매개 변수에 의해 지정된 집계 함수는 단일 값을 계산하기 위해 세트에 대해 실행됩니다.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| 요소 | 필수 | Description | 
|:---|:---|:---|
| `evaluationFrequencySecs` | 아니요 | 상태 평가를 위한 빈도를 정의합니다. 각 모니터는 에이전트가 시작될 때와 이후에 이 매개 변수에 의해 정의된 정기적인 간격에 따라 평가됩니다. |
| `lookbackSecs`   | 아니요 | 되돌아보기 창의 크기(초)입니다. |
| `evaluationType` | 아니요 | `min` – 전체 샘플 세트에서 최솟값을 사용합니다.<br>`max` – 전체 샘플 세트에서 최댓값을 사용합니다.<br>`avg` – 샘플 세트 값의 평균을 사용합니다.<br>`all` – 세트의 모든 단일 값을 임계값에 비교합니다. 세트의 모든 샘플이 임계값 조건을 만족하는 경우에만 모니터는 상태를 전환합니다. |
| `minSamples`     | 아니요 | 값을 계산하는 데 사용할 값의 최소 수입니다. |
| `maxSamples`     | 아니요 | 값을 계산하는 데 사용할 값의 최대 수입니다. |
| `warningCondition`  | 아니요 | 경고 조건에 대한 임계값 및 비교 논리입니다. |
| `criticalCondition` | 아니요 | 위험 조건에 대한 임계값 및 비교 논리입니다. |


## <a name="warningcondition-element"></a>warningCondition 요소
위험 조건에 대한 임계값 및 비교 논리를 정의합니다. 이 요소가 포함되지 않은 경우 모니터는 경고 상태로 전환되지 않습니다.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 속성 | 필수 | Description | 
|:---|:---|:---|
| `isEnabled` | 아니요 | 조건이 사용되는지 여부를 지정합니다. **False** 로 설정한 경우 임계값 및 연산자 속성을 설정할 수 있지만 조건이 사용되지 않습니다. |
| `threshold` | 아니요 | 평가 값을 비교하는 임계값을 정의합니다. |
| `operator`  | 아니요 | 임계값 식에 사용할 비교 연산자를 정의합니다. 가능한 값: >, <, >=, <=, ==. |


## <a name="criticalcondition-element"></a>criticalCondition 요소
위험 조건에 대한 임계값 및 비교 논리를 정의합니다. 이 요소가 포함되지 않은 경우 모니터는 위험 상태로 전환되지 않습니다.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| 속성 | 필수 | Description | 
|:---|:---|:---|
| `isEnabled` | 아니요 | 조건이 사용되는지 여부를 지정합니다. **False** 로 설정한 경우 임계값 및 연산자 속성을 설정할 수 있지만 조건이 사용되지 않습니다. |
| `threshold` | 아니요 | 평가 값을 비교하는 임계값을 정의합니다. |
| `operator`  | 아니요 | 임계값 식에 사용할 비교 연산자를 정의합니다. 가능한 값: >, <, >=, <=, ==. |

## <a name="sample-data-collection-rule"></a>샘플 데이터 컬렉션 규칙
게스트 모니터링을 사용하는 샘플 데이터 컬렉션 규칙의 경우 [Resource Manager 템플릿을 사용하여 가상 머신 사용](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [데이터 컬렉션 규칙](../agents/data-collection-rule-overview.md)에 대해 자세히 알아봅니다.
