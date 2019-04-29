---
title: Azure Monitor의 자동 크기 조정 설정 이해
description: 자동 크기 조정 설정 및 작동 방법을 자세히 설명합니다. Virtual Machines, Cloud Services 및 Web Apps에 적용
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 02840b8a909f46c37130bdb7162674c694a0ff96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60787498"
---
# <a name="understand-autoscale-settings"></a>자동 크기 조정 설정 이해
자동 크기 조정 설정은 애플리케이션의 변화하는 부하를 처리할 수 있는 적절한 양의 리소스가 실행되도록 하는 데 도움이 됩니다. 부하 또는 성능을 나타내는 메트릭을 기준으로 트리거되거나 예약된 날짜 및 시간에 트리거되도록 자동 크기 조정 설정을 구성할 수 있습니다. 이 문서에서는 자동 크기 조정 설정을 자세히 분석합니다. 이 문서는 스키마 및 설정의 속성부터 설명한 후, 구성할 수 있는 다른 프로필 유형에 대해서도 설명합니다. 마지막으로, 이 문서에서는 Azure의 자동 크기 조정 기능이 지정된 시간에 실행할 프로필을 평가하는 방법을 설명합니다.

## <a name="autoscale-setting-schema"></a>자동 크기 조정 설정 스키마
자동 크기 조정 설정 스키마를 보여 주기 위해 다음 자동 크기 조정 설정이 사용됩니다. 이 자동 크기 조정 설정이 다음 항목을 포함한다는 사실에 유의합니다.
- 1개의 프로필. 
- 이 프로필에는 2개의 메트릭 규칙이 있습니다. 하나는 스케일 아웃용이고 다른 하나는 스케일 인용입니다.
  - 스케일 아웃 규칙은 가상 머신 확장 집합의 평균 CPU 비율 메트릭이 지난 10분 동안 85%보다 높은 경우에 트리거됩니다.
  - 스케일 인 규칙은 가상 머신 확장 집합의 평균이 이전 분 동안 60%보다 낮은 경우에 트리거됩니다.

> [!NOTE]
> 하나의 설정에 여러 프로필이 있을 수 있습니다. 자세한 내용은 [프로필](#autoscale-profiles) 섹션을 참조하세요. 하나의 프로필에 여러 스케일 아웃 규칙 및 스케일 인 규칙이 정의될 수도 있습니다. 평가되는 방식을 알아보려면 [평가](#autoscale-evaluation) 섹션을 참조하세요.

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "GreaterThan",
              "threshold": 85
            },
            "scaleAction": {
              "direction": "Increase",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          },
    {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
              "timeAggregation": "Average",
              "operator": "LessThan",
              "threshold": 60
            },
            "scaleAction": {
              "direction": "Decrease",
              "type": "ChangeCount",
              "value": "1",
              "cooldown": "PT5M"
            }
          }
        ]
      }
    ]
  }
}
```

| 섹션 | 요소 이름 | 설명 |
| --- | --- | --- |
| 설정 | ID | 자동 크기 조정 설정의 리소스 ID입니다. 자동 크기 조정 설정은 Azure Resource Manager 리소스입니다. |
| 설정 | 이름 | 자동 크기 조정 설정 이름입니다. |
| 설정 | location | 자동 크기 조정 설정의 위치입니다. 이 위치는 크기가 조정되는 리소스의 위치와 다를 수 있습니다. |
| properties | targetResourceUri | 크기가 조정되는 리소스의 리소스 ID입니다. 리소스당 하나의 자동 크기 조정 설정만 있을 수 있습니다. |
| properties | 프로필 | 자동 크기 조정 설정은 하나 이상의 프로필로 구성됩니다. 자동 크기 조정 엔진이 실행될 때마다 1개의 프로필이 실행됩니다. |
| 프로필 | 이름 | 프로필의 이름입니다. 프로필을 식별하는 데 도움이 되는 어떤 이름도 선택할 수 있습니다. |
| 프로필 | Capacity.maximum | 허용되는 최대 용량입니다. 따라서 이 프로필 실행 시, 자동 크기 조정이 리소스를 이 수치보다 높게 조정하지 않게 됩니다. |
| 프로필 | Capacity.minimum | 허용되는 최소 용량입니다. 따라서 이 프로필 실행 시, 자동 크기 조정이 리소스를 이 수치보다 낮게 조정하지 않게 됩니다. |
| 프로필 | Capacity.default | 리소스 메트릭(이 경우 CPU “vmss1”)을 읽는 데 문제가 있고 현재 용량이 기본 용량보다 적으면, 자동 크기 조정 기능은 기본값으로 스케일 아웃합니다. 이를 통해 리소스의 가용성이 보장됩니다. 이미 현재 용량이 기본 용량보다 높아도 자동 크기 조정 기능이 스케일 인하지 않습니다. |
| 프로필 | 규칙 | 자동 크기 조정 기능은 프로필의 규칙을 사용하여 최대 및 최소 용량 간을 자동으로 조정합니다. 하나의 프로필에 여러 규칙이 포함될 수 있습니다. 일반적으로 2개의 규칙, 즉 스케일 아웃할 때를 결정하는 규칙과 스케일 인할 때를 결정하는 규칙이 있습니다. |
| 규칙 | metricTrigger | 규칙의 메트릭 조건을 정의합니다. |
| metricTrigger | metricName | 메트릭의 이름입니다. |
| metricTrigger |  metricResourceUri | 메트릭을 내보내는 리소스의 리소스 ID입니다. 대부분의 경우, 크기가 조정되는 리소스와 같습니다. 경우에 따라 메트릭을 내보내는 리소스와 크기가 조정되는 리소스가 서로 다를 수 있습니다. 예를 들어 저장소 큐에 있는 메시지의 수에 따라 가상 머신 확장 집합의 크기를 조정할 수 있습니다. |
| metricTrigger | timeGrain | 메트릭 샘플링 기간입니다. 예를 들어 **TimeGrain = “PT1M”** 은 statistic 요소에 지정된 집계 방법을 사용하여 매 1분마다 집계한다는 것을 의미합니다. |
| metricTrigger | statistic | timeGrain 기간 내 집계 방법입니다. 예를 들어 **statistic = “Average”** 및 **timeGrain = “PT1M”** 은, 평균을 구하여 메트릭이 1분마다 집계되어야 함을 의미합니다. 이 속성은 메트릭이 샘플링되는 방식을 나타냅니다. |
| metricTrigger | timeWindow | 메트릭을 다시 확인할 기간입니다. 예를 들어 **timeWindow = “PT10M”** 은 자동 크기 조정 기능이 실행될 때마다 지난 10분 동안의 메트릭을 쿼리하는 것을 의미합니다. 이 시간 창은 메트릭을 정규화할 수 있도록 하며 일시적인 스파이크에 대응하지 않도록 합니다. |
| metricTrigger | timeAggregation | 샘플링된 메트릭을 집계하는 데 사용되는 집계 방법입니다. 예를 들어 **TimeAggregation = “Average”** 는 평균을 구하여 샘플링된 메트릭을 집계합니다. 앞의 경우에서 1분짜리 샘플을 10개 가져와서 평균을 구합니다. |
| 규칙 | scaleAction | 규칙의 metricTrigger가 트리거될 때 수행할 작업입니다. |
| scaleAction | direction | 스케일 아웃하려는 경우 "Increase"이고, 스케일 인하려는 경우 "Decrease"입니다.|
| scaleAction | 값 | 늘리거나 줄일 리소스 용량 크기입니다. |
| scaleAction | cooldown | 크기 조정 작업 후, 다시 크기를 조정하기 전에 대기하는 시간입니다. 예를 들어, **cooldown = “PT10M”** 인 경우 자동 크기 조정 기능은 추가로 10분 동안 다시 크기 조정을 시도하지 않습니다. cooldown은 인스턴스의 추가 또는 제거 후에 메트릭이 안정화될 수 있도록 합니다. |

## <a name="autoscale-profiles"></a>자동 크기 조정 프로필

다음 세 가지 유형의 자동 크기 조정 프로필이 있습니다.

- **일반 프로필:** 가장 일반적인 프로필입니다. 요일 또는 특정 일을 기준으로 리소스를 확장해야 할 필요가 없으면 일반 프로필을 사용할 수 있습니다. 그런 다음 스케일 아웃할 때와 스케일 인할 때를 나타내는 메트릭 규칙으로 이 프로필을 구성할 수 있습니다. 일반 프로필은 하나만 정의하는 것이 좋습니다.

    이 문서의 앞부분에서 사용된 예제 프로필은 일반 프로필의 예입니다. 리소스의 정적 인스턴스 수까지 크기가 조정되도록 프로필을 설정할 수도 있습니다.

- **고정된 날짜 프로필:** 이 프로필은 특수한 경우에 사용됩니다. 예를 들어, 2017년 12월 26일(PST)에 예정된 중요한 이벤트가 있다고 가정해 보겠습니다. 이 날에는 리소스의 최소 및 최대 용량이 다르지만 동일한 메트릭 비율을 유지하려고 합니다. 이 경우 고정 날짜 프로필을 설정의 프로필 목록에 추가해야 합니다. 프로필은 해당 이벤트 날에만 실행되도록 구성됩니다. 다른 날에는 일반 프로필이 사용됩니다.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **되풀이 프로필:** 이 유형의 프로필을 사용하면 이 프로필이 항상 특정 요일에 사용되도록 할 수 있습니다. 되풀이 프로필에는 시작 시간이 하나만 지정됩니다. 이러한 되풀이 프로필은 다음 되풀이 프로필 또는 고정된 날짜 프로필이 시작되도록 설정될 때까지 실행됩니다. 되풀이 프로필이 1개만 있는 자동 크기 조정 설정은 같은 설정에 일반 프로필이 정의되어 있더라도 되풀이 프로필을 실행합니다. 다음 두 예제는 이 프로필이 사용되는 방법을 보여 줍니다.

    **예제 1: 평일 및 주말**
    
    예를 들어, 주말에는 최대 용량을 4로 유지하려고 합니다. 평일에는 더 많은 부하가 예상되기 때문에 최대 용량을 10으로 유지하려고 합니다. 이 경우 설정에는 2개의 되풀이 프로필, 즉 주말에 실행되는 프로필 1개와 주중에 실행되는 되풀이 프로필 1개가 포함됩니다.
    설정은 다음과 같습니다.

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    이전 설정은 각 되풀이 프로필에 일정이 있음을 보여 줍니다. 이 일정은 프로필이 실행되기 시작할 때를 결정합니다. 다른 프로필이 실행될 시간이 되면 해당 프로필은 중지됩니다.

    예를 들어, 앞의 설정에서 "weekdayProfile"은 월요일, 오전 12시에 시작되도록 설정됩니다. 즉, 이 프로필은 월요일 오전 12시에 실행되기 시작합니다. 이 프로필은 "weekendProfile"이 실행을 시작하도록 예약된 토요일 오전 12시가 될 때까지 계속 실행됩니다.

    **예제 2: 업무 시간**
    
    업무 시간(오전 9시부터 오후 5시) 동안 하나의 메트릭 임계값이 지정되고 다른 모든 시간에는 다른 메트릭 임계값이 지정된다고 가정해 보겠습니다. 이 설정은 다음과 같습니다.
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    이전 설정은 "businessHoursProfile"이 월요일 오전 9시에 실행되기 시작하고, 오후 5시까지 계속됨을 나타냅니다. 이때 “nonBusinessHoursProfile”이 실행되기 시작합니다. "nonBusinessHoursProfile"은 화요일 오전 9시까지 실행되고, "businessHoursProfile"이 다시 시작됩니다. 이는 금요일 오후 5시까지 반복됩니다. 이때부터 "nonBusinessHoursProfile"은 월요일, 오전 9시까지 계속 실행됩니다.
    
> [!Note]
> Azure Portal의 자동 크기 조정 사용자 인터페이스는 되풀이 프로필에 대한 종료 시간을 적용하고, 되풀이 프로필 사이에 자동 크기 조정 설정의 기본 프로필을 실행하기 시작합니다.
    
## <a name="autoscale-evaluation"></a>자동 크기 조정 평가
자동 크기 조정 설정에 여러 프로필이 있을 수 있고, 각 프로필에 여러 메트릭 규칙이 있을 수 있으므로 자동 크기 조정 설정이 평가되는 방식을 이해하는 것이 중요합니다. 자동 크기 조정 작업이 실행될 때마다 적용 가능한 프로필을 선택하여 시작합니다. 그 다음 자동 크기 조정 기능은 최소값 및 최대값을 평가하고, 프로필의 메트릭 규칙을 파악한 후 크기 조정 작업이 필요한지 결정합니다.

### <a name="which-profile-will-autoscale-pick"></a>자동 크기 조정 기능은 어떤 프로필을 선택하나요?

자동 크기 조정 기능은 다음 순서에 따라 프로필을 선택합니다.
1. 지금 실행되도록 구성된 고정 날짜 프로필을 먼저 찾습니다. 있으면 해당 프로필을 실행합니다. 실행해야 하는 고정 날짜 프로필이 여러 개 있으면 자동 크기 조정 기능은 첫 번째 프로필을 선택합니다.
2. 고정 날짜 프로필이 없는 경우, 자동 크기 조정 기능은 되풀이 프로필을 확인합니다. 되풀이 프로필이 있으면 실행합니다.
3. 고정 날짜 또는 되풀이 프로필이 없는 경우, 자동 크기 조정 기능은 일반 프로필을 실행합니다.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>자동 크기 조정 기능은 여러 규칙을 어떻게 평가하나요?

자동 크기 조정 기능이 실행할 프로필을 결정하면 프로필의 모든 스케일 아웃 규칙(**direction = “Increase”** 인 규칙)을 먼저 실행합니다.

하나 이상의 스케일 아웃 규칙이 트리거되면 자동 크기 조정 기능은 해당하는 각 규칙의 **scaleAction**에 따라 결정되는 새 용량을 계산합니다. 그런 다음 해당 용량의 최대 크기까지 스케일 아웃하여 서비스 가용성을 보장합니다.

예를 들어, 현재 용량이 10인 가상 머신 확장 집합이 있다고 가정해 보겠습니다. 2개의 스케일 아웃 규칙, 즉 용량이 10%씩 증가하는 규칙과 용량이 3씩 증가하는 규칙이 있습니다. 첫 번째 규칙 때문에 새 용량은 11이 되지만 두 번째 규칙에 따라 용량은 13이 됩니다. 서비스 가용성을 보장하기 위해, 자동 크기 조정 기능은 최대 용량을 가져올 작업을 선택하므로, 두 번째 규칙이 선택됩니다.

스케일 아웃 규칙이 트리거되는 경우 자동 크기 조정 기능은 모든 스케일 인 규칙(**direction = “Decrease”** 인 규칙)을 평가합니다. 자동 크기 조정 기능은 모든 스케일 인 규칙이 트리거될 경우에만 스케일 인 작업을 수행합니다.

자동 크기 조정 기능은 해당하는 각 규칙의 **scaleAction**에 따라 결정되는 새 용량을 계산합니다. 그런 다음 최대 용량을 유지하여 서비스 가용성을 보장하는 크기 조정 작업을 선택합니다.

예를 들어, 현재 용량이 10인 가상 머신 확장 집합이 있다고 가정해 보겠습니다. 2개의 스케일 인 규칙, 즉 용량이 50%씩 감소하는 규칙과 용량이 3씩 감소하는 규칙이 있습니다. 첫 번째 규칙 때문에 새 용량은 5가 되지만 두 번째 규칙에 따라 용량은 7이 됩니다. 서비스 가용성을 보장하기 위해, 자동 크기 조정 기능은 최대 용량을 가져올 작업을 선택하므로, 두 번째 규칙이 선택됩니다.

## <a name="next-steps"></a>다음 단계
자동 크기 조정에 대한 자세한 내용은 다음을 참조하세요.

* [자동 크기 조정 개요](../../azure-monitor/platform/autoscale-overview.md)
* [Azure Monitor 자동 크기 조정 공용 메트릭](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Azure Monitor 자동 크기 조정에 대한 모범 사례](../../azure-monitor/platform/autoscale-best-practices.md)
* [크기 자동 조정 작업을 사용하여 전자 메일 및 웹후크 경고 알림 보내기](../../azure-monitor/platform/autoscale-webhook-email.md)
* [자동 크기 조정 REST API](https://msdn.microsoft.com/library/dn931953.aspx)
