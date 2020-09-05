---
title: Azure Functions에서 소비 계획 비용 예측
description: Azure의 소비 계획에서 함수 앱을 실행할 때 발생할 수 있는 비용을 보다 정확 하 게 예측 하는 방법을 알아봅니다.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 33c892bd7904d2921039a4b2afb9c775d6a4926a
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88207765"
---
# <a name="estimating-consumption-plan-costs"></a>소비 계획 비용 예측

현재는 Azure Functions에서 실행 되는 앱에 대 한 호스팅 계획의 세 가지 유형이 있으며 각 계획에는 고유한 가격 책정 모델이 있습니다. 

| 계획 | 설명 |
| ---- | ----------- |
| [**Consumption**](functions-scale.md#consumption-plan) | 함수 앱이 실행 되는 시간에 대해서만 요금이 청구 됩니다. 이 요금제에는 구독 별로 [무료 부여][가격 책정 페이지가] 포함 되어 있습니다.|
| [**Premium**](functions-scale.md#premium-plan) | 는 소비 계획과 동일한 기능 및 크기 조정 메커니즘을 제공 하지만 향상 된 성능 및 VNET 액세스를 제공 합니다. 비용은 선택한 가격 책정 계층을 기준으로 합니다. 자세한 내용은 [Azure Functions Premium 요금제](functions-premium-plan.md)를 참조 하세요. |
| [**전용 (App Service)**](functions-scale.md#app-service-plan) <br/>(기본 계층 이상) | 전용 Vm 또는 격리에서 실행 해야 하는 경우 사용자 지정 이미지를 사용 하거나 과도 한 App Service 계획 용량을 사용 하려고 합니다. [정기적인 App Service 요금제 청구](https://azure.microsoft.com/pricing/details/app-service/)를 사용 합니다. 비용은 선택한 가격 책정 계층을 기준으로 합니다.|

함수 성능 및 비용 요구 사항을 가장 잘 지 원하는 계획을 선택 했습니다. 자세한 내용은 [Azure Functions 크기 조정 및 호스팅](functions-scale.md)을 참조하세요.

이 계획의 결과로 비용이 계산 되기 때문에이 문서에서는 소비 계획만 다룹니다. 이 문서는 [소비 계획 비용 청구 FAQ](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) 문서를 대체 합니다.

Durable Functions 소비 계획에서 실행할 수도 있습니다. Durable Functions를 사용할 때의 비용 고려 사항에 대 한 자세한 내용은 [Durable Functions 청구](./durable/durable-functions-billing.md)를 참조 하세요.

## <a name="consumption-plan-costs"></a>소비 계획 비용

단일 함수 실행의 실행 *비용은* *GB 초*단위로 측정 됩니다. 실행 비용은 메모리 사용량과 실행 시간을 결합 하 여 계산 됩니다. 더 많은 메모리를 소비 하는 함수를 사용 하 여 더 많은 비용을 위해 실행 하는 함수입니다. 

함수에서 사용 되는 메모리 양이 일정 하 게 유지 되는 경우를 고려 합니다. 이 경우 비용을 계산 하는 것은 간단한 곱셈입니다. 예를 들어 함수가 3 초 동안 0.5 GB를 사용 한다고 가정 합니다. 실행 비용은 `0.5GB * 3s = 1.5 GB-seconds` 입니다. 

시간이 지남에 따라 메모리 사용이 변경 되기 때문에 계산은 기본적으로 시간에 따른 메모리 사용량의 정수 계열입니다.  시스템은 일정 한 간격으로 프로세스의 메모리 사용량 (자식 프로세스와 함께)을 샘플링 하 여이 계산을 수행 합니다. [가격 책정 페이지]에 설명 된 대로 메모리 사용량은 가장 가까운 128-MB 버킷으로 반올림 됩니다. 프로세스에서 160 MB를 사용 하는 경우 256 MB에 대해 요금이 청구 됩니다. 계산은 동일한 프로세스에서 여러 동시 함수 실행 인 동시성을 고려 합니다.

> [!NOTE]
> CPU 사용량은 실행 비용에서 직접 고려 되지 않지만 함수의 실행 시간에 영향을 미칠 경우 비용에 영향을 줄 수 있습니다.

HTTP 트리거 함수의 경우 함수 코드가 실행 되기 전에 오류가 발생 하는 경우 실행에 대 한 요금이 청구 되지 않습니다. 즉, API 키 유효성 검사 또는 App Service 인증/권한 부여 기능으로 인해 플랫폼의 401 응답이 실행 비용에 계산 되지 않습니다. 마찬가지로, 5xx 상태 코드 응답은 요청을 처리 하는 함수 전에 플랫폼에서 발생 하는 경우에는 계산 되지 않습니다. 함수 코드 실행이 시작 된 후 플랫폼에 의해 생성 된 5xx 응답은 함수 코드에 의해 오류가 발생 하지 않더라도 여전히 실행으로 계산 됩니다.

## <a name="other-related-costs"></a>기타 관련 비용

계획에서 함수를 실행 하는 전체 비용을 예측할 때 함수 런타임은 각각 별도로 청구 되는 여러 Azure 서비스를 사용 합니다. 함수 앱에 대 한 가격 책정을 계산할 때 다른 Azure 서비스와 통합 하는 트리거와 바인딩을 사용 하려면 이러한 추가 서비스를 만들고 지불 해야 합니다. 

소비 계획에서 실행 되는 함수의 경우 총 비용은 함수의 실행 비용과 대역폭 및 추가 서비스의 비용을 더한 것입니다. 

함수 앱 및 관련 서비스의 전체 비용을 예측 하는 경우 [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=functions)를 사용 합니다. 

| 관련 비용 | 설명 |
| ------------ | ----------- |
| **스토리지 계정** | 각 함수 앱에는 [별도로 청구](https://azure.microsoft.com/pricing/details/storage/)되는 연결 된 범용 [Azure Storage 계정이](../storage/common/storage-introduction.md#types-of-storage-accounts)있어야 합니다. 이 계정은 함수 런타임에서 내부적으로 사용 되지만 저장소 트리거 및 바인딩에 사용할 수도 있습니다. 저장소 계정이 없는 경우 함수 앱을 만들 때 하나씩 만들어집니다. 자세한 내용은 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.|
| **Application Insights** | 함수는 [Application Insights](../azure-monitor/app/app-insights-overview.md) 을 사용 하 여 함수 앱에 고성능 모니터링 환경을 제공 합니다. 필수는 아니지만 [Application Insights 통합을 사용 하도록 설정](functions-monitoring.md#enable-application-insights-integration)해야 합니다. 원격 분석 데이터의 무료 부여는 매달 포함 됩니다. 자세히 알아보려면 [Azure Monitor 가격 책정 페이지](https://azure.microsoft.com/pricing/details/monitor/)를 참조 하세요. |
| **네트워크 대역폭** | 동일한 지역에 있는 Azure 서비스 간의 데이터 전송에 대해서는 지불 하지 않습니다. 그러나 다른 지역 또는 Azure 외부로 아웃 바운드 데이터 전송에 대 한 비용을 발생 시킬 수 있습니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조 하세요. |

## <a name="behaviors-affecting-execution-time"></a>실행 시간에 영향을 주는 동작

다음 함수 동작은 실행 시간에 영향을 줄 수 있습니다.

+ **트리거 및 바인딩**: [함수 바인딩에](functions-triggers-bindings.md) 대 한 입력을 읽고 출력을 쓰는 데 걸리는 시간은 실행 시간으로 계산 됩니다. 예를 들어 함수가 출력 바인딩을 사용 하 여 Azure storage 큐에 메시지를 쓰는 경우 실행 시간에는 함수 비용 계산에 포함 된 메시지를 큐에 쓰는 데 걸리는 시간이 포함 됩니다. 

+ **비동기 실행**: 함수가 비동기 요청 (c #의 경우)의 결과를 기다리는 시간을 `await` 실행 시간으로 계산 합니다. GB 초 계산은 함수의 시작 및 종료 시간과 해당 기간 동안의 메모리 사용을 기반으로 합니다. CPU 작업을 기준으로 해당 시간에 발생 하는 것은 계산에서 제외 됩니다. 비동기 작업 중에 [Durable Functions](durable/durable-functions-overview.md)를 사용 하 여 비용을 절감할 수 있습니다. Orchestrator 함수에서 기다립니다에 소요 된 시간에 대해서는 요금이 청구 되지 않습니다.

## <a name="view-execution-data"></a>실행 데이터 보기

[청구서](../cost-management-billing/understand/download-azure-invoice.md)에서 실제 청구 된 비용과 함께 **총 실행** 수, 함수 및 **실행 시간 함수**에 대 한 비용 관련 데이터를 볼 수 있습니다. 그러나이 송장 데이터는 과거 송장 기간에 대 한 월별 집계입니다. 

함수의 비용 영향을 보다 잘 이해 하기 위해 Azure Monitor를 사용 하 여 함수 앱에서 현재 생성 하 고 있는 비용 관련 메트릭을 볼 수 있습니다. [Azure Portal] 또는 REST api에서 [Azure Monitor 메트릭 탐색기](../azure-monitor/platform/metrics-getting-started.md) 를 사용 하 여이 데이터를 가져올 수 있습니다.

### <a name="monitor-metrics-explorer"></a>메트릭 탐색기 모니터링

[Azure Monitor 메트릭 탐색기](../azure-monitor/platform/metrics-getting-started.md) 를 사용 하 여 소비 계획 함수 앱에 대 한 비용 관련 데이터를 그래픽 형식으로 볼 수 있습니다. 

1. **검색 서비스, 리소스 및 문서** 에서 [Azure Portal] 맨 위에 있는 `monitor` **서비스**에서 **모니터** 를 검색 하 고 선택 합니다.

1. 왼쪽에서 메트릭을 선택한 **Metrics**  >  **Select a resource**다음 이미지 아래의 설정을 사용 하 여 함수 앱을 선택 합니다.

    ![함수 앱 리소스 선택](media/functions-consumption-costing/select-a-resource.png)

      
    |설정  |제안 값  |설명  |
    |---------|---------|---------|
    | Subscription    |  사용자의 구독  | 함수 앱을 사용 하는 구독입니다.  |
    | Resource group     | 리소스 그룹  | 함수 앱을 포함 하는 리소스 그룹입니다.   |
    | 리소스 종류     |  App Services | 함수 앱은 모니터의 App Services 인스턴스로 표시 됩니다. |
    | 리소스     |  함수 앱  | 모니터링할 함수 앱입니다.        |

1. **적용** 을 선택 하 여 모니터링할 리소스로 함수 앱을 선택 합니다.

1. **메트릭**에서 **함수 실행 수** 및 **집계**에 대 한 **합계** 를 선택 합니다. 이렇게 하면 선택한 기간 동안의 실행 횟수 합계가 차트에 추가 됩니다.

    ![차트에 추가할 함수 앱 메트릭을 정의 합니다.](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. **메트릭 추가** 를 선택 하 고 2-4 단계를 반복 하 여 차트에 **함수 실행 단위** 를 추가 합니다. 

결과 차트에는 선택한 시간 범위의 실행 메트릭에 대 한 합계가 모두 포함 됩니다 (이 경우 2 시간).

![함수 실행 수 및 실행 단위의 그래프](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

실행 단위 수가 실행 수보다 훨씬 크므로 차트에는 실행 단위만 표시 됩니다.

이 차트에서는 `Function Execution Units` 2 시간 동안 사용 된 총 11억1000만 (MB)를 표시 합니다. GB-초로 변환 하려면 1024000으로 나눕니다. 이 예제에서 함수 앱은 `1110000000 / 1024000 = 1083.98` GB-초를 사용 했습니다. 이 값을 사용 하 여 실행 시간에 대 한 무료 권한을 이미 사용 하 고 있다고 가정 하 여이 두 시간에 대 한 비용을 제공 하는 [함수 가격 책정 페이지][가격 책정]페이지에서 현재 가격을 곱합니다. 

### <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/) 에는 메트릭을 검색 하는 명령이 있습니다. CLI는 로컬 명령 환경에서 사용 하거나 [Azure Cloud Shell](../cloud-shell/overview.md)를 사용 하 여 포털에서 직접 사용할 수 있습니다. 예를 들어 다음 [az monitor 메트릭 list](/cli/azure/monitor/metrics#az-monitor-metrics-list) 명령은 이전에 사용 된 것과 동일한 기간에 매시간 데이터를 반환 합니다.

`<AZURE_SUBSCRIPTON_ID>`명령을 실행 하는 Azure 구독 ID로 바꾸어야 합니다.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

이 명령은 다음 예제와 같은 JSON 페이로드를 반환 합니다.

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
이 특정 응답에서는에서 `2019-09-11T21:46` 로의 `2019-09-11T23:18` 응용 프로그램이 11억1000만 m b-밀리초 (1083.98 g b-초)를 사용 하는 것을 보여 줍니다.

## <a name="determine-memory-usage"></a>메모리 사용량 확인

함수 실행 단위는 실행 시간과 메모리 사용의 조합입니다 .이를 통해 메모리 사용을 이해 하는 데 어려운 메트릭을 사용할 수 있습니다. 메모리 데이터는 현재 Azure Monitor를 통해 사용할 수 있는 메트릭이 아닙니다. 그러나 앱의 메모리 사용을 최적화 하려는 경우는 Application Insights에서 수집 된 성능 카운터 데이터를 사용할 수 있습니다.  

아직 수행 하지 않은 경우 [함수 앱에서 Application Insights를 사용 하도록 설정](functions-monitoring.md#enable-application-insights-integration)합니다. 이 통합을 사용 하도록 설정 하면 [포털에서이 원격 분석 데이터를 쿼리할](functions-monitoring.md#query-telemetry-data)수 있습니다.  

**모니터링**아래에서 **로그 (분석)** 를 선택 하 고 다음 원격 분석 쿼리를 복사 하 여 쿼리 창에 붙여넣고 **실행**을 선택 합니다. 이 쿼리는 샘플링 된 각 시간의 총 메모리 사용량을 반환 합니다.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

결과는 다음 예제와 같습니다.

| 타임 스탬프 \[ UTC\]          | name          | 값       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 AM | 프라이빗 바이트 | 209932288 |
| 9/12/2019, 1:06:14 \. 994 AM | 프라이빗 바이트 | 212189184 |
| 9/12/2019, 1:06:30 \. 010 AM | 프라이빗 바이트 | 231714816 |
| 9/12/2019, 1:07:15 \. 040 AM | 프라이빗 바이트 | 210591744 |
| 9/12/2019, 1:12:16 \. 285 AM | 프라이빗 바이트 | 216285184 |
| 9/12/2019, 1:12:31 \. 376 AM | 프라이빗 바이트 | 235806720 |

## <a name="function-level-metrics"></a>함수 수준 메트릭

Azure Monitor는 함수 앱 인 리소스 수준에서 메트릭을 추적 합니다. Application Insights 통합은 함수 단위로 메트릭을 내보냅니다. 함수의 평균 기간을 가져오는 분석 쿼리 예제는 다음과 같습니다.

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [함수 앱 모니터링에 대 한 자세한 정보](functions-monitoring.md)

[가격 책정 페이지]:https://azure.microsoft.com/pricing/details/functions/
[Azure Portal]: https://portal.azure.com
