---
title: 새 Azure Time Series Insights Gen2 API 버전으로 마이그레이션 | Microsoft Docs
description: 새 일반 공급 버전을 사용 하도록 Azure Time Series Insights Gen2 환경을 업데이트 하는 방법입니다.
ms.service: time-series-insights
services: time-series-insights
author: shreyasharmamsft
ms.author: shresha
manager: dpalled
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/12/2020
ms.custom: shresha
ms.openlocfilehash: 784c19844c658af6850c755244314145223c45ef
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88163954"
---
# <a name="migrating-to-new-azure-time-series-insights-gen2-api-versions"></a>새 Azure Time Series Insights Gen2 API 버전으로 마이그레이션

## <a name="overview"></a>개요

공개 미리 보기로 제공 되는 Azure Time Series Insights Gen2 환경을 만든 경우 (2020 7 월 16 일 이전)이 문서에 설명 된 단계에 따라 새 일반 버전의 Api를 사용 하도록 TSI 환경을 업데이트 하세요.

새 API 버전은 이며 `2020-07-31` 업데이트 된 [시계열 식 구문을](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)사용 합니다.

사용자는 환경 [시계열 모델 변수](./concepts-variables.md), 저장 된 쿼리, Power BI 쿼리 및 API 끝점을 호출 하는 사용자 지정 도구를 마이그레이션해야 합니다. 마이그레이션 프로세스에 대 한 질문이 나 궁금한 사항이 있는 경우 Azure Portal를 통해 지원 티켓을 제출 하 고이 문서를 언급 합니다.

> [!IMPORTANT]
> Preview API 버전은 `2018-11-01-preview` 2020 년 10 월 31 일까 지 계속 지원 됩니다. 서비스 중단을 방지 하려면 먼저이 마이그레이션의 모든 해당 단계를 완료 하세요.

## <a name="migrate-time-series-model-and-saved-queries"></a>시계열 모델 및 저장 된 쿼리 마이그레이션

사용자가 시계열 [모델 변수](./concepts-variables.md) 및 저장 된 쿼리를 마이그레이션하는 데 도움이 되도록 [Azure Time Series Insights 탐색기](https://insights.timeseries.azure.com)를 통해 기본 제공 도구를 사용할 수 있습니다. 마이그레이션하려는 환경으로 이동 하 여 다음 단계를 수행 합니다. **마이그레이션을 부분적으로 완료 하 고 반환 하 여 나중에 완료할 수 있지만 업데이트를 되돌릴 수 없습니다.**

> [!NOTE]
> 시계열 모델 및 저장 된 쿼리를 업데이트 하려면 환경에 대 한 참가자 여야 합니다. 참가자가 아닌 경우에는 저장 된 개인 쿼리만 마이그레이션할 수 있습니다. 계속 하기 전에 [환경 액세스 정책](./concepts-access-policies.md) 및 액세스 수준을 검토 하세요.

1. 탐색기에서 시계열 모델 변수 및 저장 된 쿼리에 사용 되는 구문을 업데이트 하 라는 메시지가 표시 됩니다.

    [![요청할](media/api-migration/ux-prompt.png)](media/v2-update-overview/overview-one.png#lightbox)

    알림을 실수로 닫은 경우 알림 패널에서 찾을 수 있습니다.

1. **업데이트 표시** 를 클릭 하 여 마이그레이션 도구를 엽니다.

1. **다운로드 유형**을 클릭 합니다. 마이그레이션은 현재 형식을 alter variable 구문을 덮어쓰므로 현재 형식의 복사본을 저장 해야 합니다. 이 도구는 형식이 다운로드 될 때 사용자에 게 알립니다.

    [![다운로드 유형](media/api-migration/ux-migration-tool.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **변수 업데이트**를 클릭 합니다. 변수가 업데이트 되 면이 도구는 사용자에 게 알립니다.

    > [!IMPORTANT]
    > 형식을 업데이트 하는 경우 이전 API 버전 ()을 사용 하는 사용자 지정 응용 프로그램에서 `2018-11-01-preview` 계속 작업 하려면 새 api 버전 ()을 사용 해야 `2020-07-31` 합니다. 사용 중인 API 버전을 잘 모르겠으면 업데이트 하기 전에 관리자에 게 확인 하세요. 마이그레이션 도구를 닫고 나중에 이러한 단계로 돌아갈 수 있습니다. 자세한 내용은 [사용자 지정 응용 프로그램을 마이그레이션하는 방법을](#migrate-custom-applications)참조 하세요.

    [![변수 업데이트](media/api-migration/ux-migration-tool-downloaded-types.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **저장 된 쿼리 업데이트**를 클릭 합니다. 변수가 업데이트 되 면이 도구는 사용자에 게 알립니다.

    [![저장 된 쿼리 업데이트](media/api-migration/ux-migration-tool-updated-variables.png)](media/v2-update-overview/overview-one.png#lightbox)

1. **완료**를 클릭합니다.

    [![완료 된 마이그레이션](media/api-migration/ux-migration-tool-updated-saved-queries.png)](media/v2-update-overview/overview-one.png#lightbox)

새로 만든 변수 및 저장 된 쿼리 중 일부를 차트로 작성 하 여 업데이트 된 환경을 검토 합니다. 차트를 작성 하는 동안 예기치 않은 동작이 표시 되 면 탐색기에서 피드백 도구를 사용 하 여 피드백을 보내 주세요.

## <a name="migrate-power-bi-queries"></a>Power BI 쿼리 마이그레이션

Power BI 커넥터를 사용 하 여 쿼리를 생성 한 경우 미리 보기 API 버전 및 이전 시계열 식 구문을 사용 하 여 Azure Time Series Insights를 호출 하 게 됩니다. 이러한 쿼리는 미리 보기 API가 더 이상 사용 되지 않을 때까지 계속 해 서 데이터를 검색 합니다.

새 API 버전 및 새 시계열 식 구문을 사용 하도록 쿼리를 업데이트 하려면 탐색기에서 쿼리를 다시 생성 해야 합니다. [Power BI 커넥터를 사용 하 여 쿼리를 만드는](./how-to-connect-power-bi.md)방법에 대해 자세히 알아보세요.

> [!NOTE]
> Power BI Desktop 7 월 2020 버전을 사용 해야 합니다. 그렇지 않으면 [잘못 된 쿼리 페이로드 버전 오류가](./how-to-diagnose-troubleshoot.md#problem-power-bi-connector-shows-unable-to-connect)표시 될 수 있습니다.

## <a name="migrate-custom-applications"></a>사용자 지정 응용 프로그램 마이그레이션

사용자 지정 응용 프로그램이 다음 REST 끝점을 호출 하는 경우 URI에서 API 버전을로 업데이트 하는 것은 충분 합니다 `2020-07-31` .

- Time Series Model API
  - 모델 설정 Api
    - [가져오기](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/get)
    - [Update](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/modelsettings/update)
  - 인스턴스 API
    - [모든 일괄 처리 작업](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/executebatch)
    - [목록](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/list)
    - [검색](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/search)
    - [제안](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriesinstances/suggest)
  - 계층 구조 Api
    - [모든 일괄 처리 작업](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/executebatch)
    - [목록](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeserieshierarchies/list)
  - 형식 Api
    - [삭제, 가져오기 작업](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch)
    - [목록](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/list)

다음 REST 끝점의 경우 URI에서 API 버전을로 업데이트 하 `2020-07-31` 고 속성의 모든 항목이 `tsx` 업데이트 된 [시계열 식 구문을](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax)사용 하는지 확인 해야 합니다.

- 형식 Api
  - [Put 작업](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/timeseriestypes/executebatch#typesbatchput)
- 쿼리 Api
  - [GetEvents](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getevents)
  - [GetSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#getseries)
  - [GetAggregateSeries](https://docs.microsoft.com/rest/api/time-series-insights/dataaccessgen2/query/execute#aggregateseries)

### <a name="examples"></a>예

#### <a name="typesbatchput"></a>유형 Batchput

이전 요청 본문 (에서 사용 됨 `2018-11-01-preview` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.[Temperature_Celsius].Double"
          },
          "filter": {
            "tsx": "$event.[Mode].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

업데이트 된 요청 본문 (에서 사용 됨 `2020-07-31` ):

```JSON
{
  "put": [
    {
      "id": "c1cb7a33-ed9b-4cf1-9958-f3162fed8ee8",
      "name": "OutdoorTemperatureSensor",
      "description": "This is an outdoor temperature sensor.",
      "variables": {
        "AverageTemperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event['Temperature_Celsius'].Double"
          },
          "filter": {
            "tsx": "$event['Mode'].String = 'outdoor'"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

또는 `filter` 도 일 수 있습니다 `$event.Mode.String = 'outdoor'` . 는 `value` 괄호를 사용 하 여 특수 문자 ()를 이스케이프 해야 합니다 `_` .

#### <a name="getevents"></a>GetEvents

이전 요청 본문 (에서 사용 됨 `2018-11-01-preview` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.[Value].Double != null) OR ($event.[Status].String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

업데이트 된 요청 본문 (에서 사용 됨 `2020-07-31` ):

```JSON
{
  "getEvents": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952",
      "T1"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "filter": {
      "tsx": "($event.Value.Double != null) OR ($event.Status.String = 'Good')"
    },
    "projectedProperties": [
      {
        "name": "Temperature",
        "type": "Double"
      }
    ]
  }
}
```

또는 `filter` 도 일 수 있습니다 `($event['Value'].Double != null) OR ($event['Status'].String = 'Good')` .

#### <a name="getseries"></a>GetSeries

이전 요청 본문 (에서 사용 됨 `2018-11-01-preview` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event.[Bar-Pressure-Offset]"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

업데이트 된 요청 본문 (에서 사용 됨 `2020-07-31` ):

```JSON
{
  "getSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "inlineVariables": {
      "pressure": {
        "kind": "numeric",
        "value": {
          "tsx": "$event['Bar-Pressure-Offset']"
        },
        "aggregation": {
          "tsx": "avg($value)"
        }
      }
    },
    "projectedVariables": [
      "pressure"
    ]
  }
}
```

또는 `value` 도 일 수 있습니다 `$event['Bar-Pressure-Offset'].Double` . 데이터 형식이 지정 되지 않은 경우 데이터 형식은 항상 Double로 가정 됩니다. 특수 문자 ()를 이스케이프 하려면 대괄호 표기법을 사용 해야 합니다 `-` .

#### <a name="aggregateseries"></a>AggregateSeries

이전 요청 본문 (에서 사용 됨 `2018-11-01-preview` ):

```JSON
{
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.[Temp].Double, toDouble($event.[Temp].Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

업데이트 된 요청 본문 (에서 사용 됨 `2020-07-31` ):

```JSON
  "aggregateSeries": {
    "timeSeriesId": [
      "006dfc2d-0324-4937-998c-d16f3b4f1952"
    ],
    "searchSpan": {
      "from": "2016-08-01T00:00:00Z",
      "to": "2016-08-01T00:16:50Z"
    },
    "interval": "PT1M",
    "inlineVariables": {
      "MinTemperature": {
        "kind": "numeric",
        "value": {
          "tsx": "coalesce($event.Temp.Double, toDouble($event.Temp.Long))"
        },
        "aggregation": {
          "tsx": "min($value)"
        }
      },
    },
    "projectedVariables": [
      "MinTemperature"
    ]
  }
}
```

또는 `value` 도 일 수 있습니다 `coalesce($event['Temp'].Double, toDouble($event['Temp'].Long))` .

### <a name="potential-errors"></a>잠재적 오류

#### <a name="invalidinput"></a>InvalidInput

다음 오류가 표시 되 면 새 API 버전 ()을 사용 `2020-07-31` 하지만 TSX 구문이 업데이트 되지 않은 것입니다. 위의 [시계열 식 구문](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax) 및 마이그레이션 예를 검토 하세요. `tsx`API 요청을 다시 전송 하기 전에 모든 속성이 올바르게 업데이트 되었는지 확인 합니다.

```JSON
{
    "error": {
        "code": "InvalidInput",
        "message": "Unable to parse 'value' time series expression (TSX) in variable 'Temperature'.",
        "target": "projectedVariables.Temperature.value",
        "innerError": {
            "parseErrorDetails": [
                {
                    "pos": [
                        0,
                        5
                    ],
                    "line": 1,
                    "msg": "Unsupported Time Series Expression version TSX01 used instead of TSX00.",
                    "code": "UnsupportedTSXVersionTSX01",
                    "target": "$event"
                }
            ],
            "code": "TsxParseError"
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

- [Azure Time Series Insights 탐색기](./concepts-ux-panels.md) 또는 사용자 지정 응용 프로그램을 통해 환경을 테스트 합니다.
