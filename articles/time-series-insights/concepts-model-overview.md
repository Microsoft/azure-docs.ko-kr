---
title: 시계열 모델-Azure Time Series Insights Gen2 | Microsoft Docs
description: Azure Time Series Insights Gen2의 시계열 모델에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 18212bf92304e75c702c51ff12628cd670755bb0
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855201"
---
# <a name="time-series-model-in-azure-time-series-insights-gen2"></a>Azure Time Series Insights Gen2의 시계열 모델

이 문서에서는 시계열 모델, 기능 및 Azure Time Series Insights Gen2 환경에서 사용자 고유의 모델 빌드 및 업데이트를 시작 하는 방법을 설명 합니다.

> [!TIP]
>
> * 라이브 시계열 모델 예제는 [Contoso 바람 팜 데모](https://insights.timeseries.azure.com/preview/samples) 환경으로 이동 합니다.
> * Azure Time Series Insights TSI 탐색기를 사용 하 여 [시계열 모델을 사용 하는 방법](/azure/time-series-insights/how-to-edit-your-model) 에 대해 알아봅니다.

## <a name="summary"></a>요약

전통적으로 IoT 디바이스에서 수집되는 데이터는 컨텍스트 정보가 부족하기 때문에 신속하게 센서를 찾아서 분석하기가 어렵습니다. 시계열 모델에 대 한 주요 동기는 IoT 또는 시계열 데이터를 간편 하 게 찾고 분석 하는 것입니다. 이러한 목표는 시계열 데이터의 curation, 유지 관리 및 보강을 사용 하 여 분석을 위해 소비자 지원 데이터 집합을 준비 하는 데 도움이 됩니다.

## <a name="scenario-contosos-new-smart-oven"></a>시나리오: Contoso의 새로운 스마트 오븐

**Contoso 스마트 오븐의 가상 시나리오를 고려 합니다.** 이 시나리오에서는 각 Contoso 스마트 오븐에 4 개의 상단 버너와 오븐 자체에 대 한 4 개의 온도 센서가 하나씩 있다고 가정 합니다. 최근 까지는 각 Contoso 온도 센서가 데이터를 개별적으로 전송, 저장 및 시각화 합니다. 주방 어플라이언스 모니터링의 경우 Contoso는 개별 센서 마다 하나씩 기본 차트에 의존 합니다.

Contoso가 초기 데이터 및 시각화 솔루션에 만족 하는 동안 다음과 같은 몇 가지 제한 사항이 드러납니다.

* 고객은 대부분의 최고 버너가 켜져 있을 때 전체 오븐이 얼마나 핫을 얻을 수 있는지 알고 싶어 했습니다. Contoso는 전체 오븐의 조건에 대 한 통합 대답을 분석 하 고 제시 하기가 더 어려워집니다.
* Contoso 엔지니어는 상위 버너가 동시에 실행 되는 것을 확인 하는 것이 비효율적입니다. 서로 연결 된 온도 및 전압 센서와 저장소에서 해당 센서를 찾는 방법을 상호 참조 하는 데 어려움을 겪고 있습니다.
* Contoso 품질 보증 팀은 두 센서 버전 간의 기록을 감사 하 고 비교 하고자 했습니다. 센서 버전에 속한 데이터를 확인 하는 데 어려움이 있습니다.

가장 중요 한 스마트 오븐 시계열 모델을 구성 하 고, 구성 하 고, 정의 하는 기능이 없는 경우 각 온도 센서는 유지 관리 되지 않는 데이터 요소를 유지 관리 합니다. 각 데이터 집합은 서로 독립적 이기 때문에 이러한 데이터 요소를 실행 가능한 정보로 전환 하는 것이 더 어렵습니다.

이러한 제한 사항으로 인해 스마트 데이터 집계 및 시각화 도구는 Contoso의 새로운 오븐과 함께 제공 됩니다.

* 데이터 시각화는 데이터를 편리한 뷰에 연결 하 고 결합할 수 있는 경우에 유용 합니다. 온도 센서와 함께 전압 센서를 표시 하는 경우를 예로 들 수 있습니다.
* 비교, 확대/축소 및 시간 범위 기능과 함께 여러 엔터티에 대 한 다차원 데이터를 관리 하는 것이 어려울 수 있습니다.

시계열 모델은이 가상의 예제에서 발생 하는 다양 한 시나리오에 대 한 **편리한 솔루션을 제공 합니다** .

[![시계열 모델 스마트 오븐 차트 예제](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* 시계열 모델은 데이터를 시간 범위에서 그리고 센서와 장치 종류 간에 비교할 수 있으므로 데이터를 tsi 하기 때문에 쿼리 및 탐색에서 중요 한 역할을 합니다. (**A**)
* 시계열 모델에서 유지 되는 데이터는 시계열 쿼리 계산을 변수로 유지 하 고 쿼리 시간에 다시 계산 하기 때문에 데이터는 추가 까닭 됩니다.
* 시계열 모델은 향상 된 시각화 및 관리 기능을 위해 데이터를 구성 하 고 집계 합니다. (**B**)

### <a name="key-capabilities"></a>주요 기능

시계열 모델을 사용 하면 간단 하 고 간편 하 게 시계열 contextualization을 관리할 수 있기 때문에 Azure Time Series Insights Gen2에서 다음과 같은 기능을 사용할 수 있습니다. 다음과 같은 도움을 줍니다.

* 스칼라 함수, 집계 작업 등을 활용 하 여 계산 또는 수식을 작성 하 고 관리 합니다.
* 부모-자식 관계를 정의 하 여 탐색, 검색 및 참조를 사용 하도록 설정 합니다.
* *인스턴스 필드로*정의 된 인스턴스와 연결 된 속성을 정의 하 고이 속성을 사용 하 여 계층을 만듭니다.

### <a name="components"></a>구성 요소

시계열 모델에는 세 가지 핵심 구성 요소가 있습니다.

* [시계열 모델 인스턴스](#time-series-model-instances)
* [시계열 모델 계층 구조](#time-series-model-hierarchies)
* [시계열 모델 형식](#time-series-model-types)

이러한 구성 요소를 결합 하 여 시계열 모델을 지정 하 고 데이터를 구성 합니다.

[![시계열 모델 개요 차트](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

[Azure Time Series Insights TSI 탐색기](/azure/time-series-insights/concepts-model-overview)를 통해 시계열 모델을 만들고 관리할 수 있습니다. 시계열 모델 설정은 [모델 설정 API](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis)를 통해 관리할 수 있습니다.

## <a name="time-series-model-instances"></a>시계열 모델 인스턴스

시계열 모델 *인스턴스* 는 시계열 자체를 가상으로 표현한 것입니다.

대부분의 경우 인스턴스는 **deviceId** 또는 **assetId**에 의해 고유 하 게 식별 되며이는 시계열 id로 저장 됩니다.

인스턴스에는 시계열 ID, 형식, 이름, 설명, 계층 및 인스턴스 필드와 같은 *인스턴스 속성*이라고 하는 설명 정보가 있습니다. 인스턴스 속성에는 적어도 계층 구조 정보가 포함됩니다.

*인스턴스 필드* 는 계층 수준에 대 한 값 뿐만 아니라 제조업체, 연산자 등을 포함할 수 있는 설명 정보의 모음입니다.

Azure Time Series Insights Gen2 환경에 대 한 이벤트 원본이 구성 된 후 인스턴스는 시계열 모델에서 자동으로 검색 되 고 생성 됩니다. 인스턴스는 시계열 모델 쿼리를 사용 하 여 Azure Time Series Insights TSI 탐색기를 통해 만들거나 업데이트할 수 있습니다.

[Contoso 바람 팜 데모](https://insights.timeseries.azure.com/preview/samples) 에서는 몇 가지 라이브 인스턴스 예제를 제공 합니다.

[![시계열 모델 인스턴스 예제](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>인스턴스 속성

인스턴스는 **Timeseriesid**, **typeId**, **name**, **description**, **계층 id**및 **instancefields**에 의해 정의 됩니다. 각 인스턴스는 한 가지 *유형*및 하나 이상의 *계층*에 매핑됩니다.

| 속성 | 설명 |
| --- | ---|
| timeSeriesId | 인스턴스가 연결 된 시계열의 고유 ID입니다. 대부분의 경우 인스턴스는 deviceId 또는 assetId와 같은 속성으로 고유 하 게 식별 됩니다. 경우에 따라 최대 3 개의 속성을 결합 하는 보다 구체적인 복합 ID를 사용할 수 있습니다. |
| typeId | 인스턴스가 연결 된 시계열 모델 유형의 고유 문자열 ID (대/소문자 구분)입니다. 기본적으로 검색 되는 새 인스턴스는 모두 기본 형식에 연결 됩니다.
| name | **Name** 속성은 선택 사항이 며 대/소문자를 구분 합니다. **이름을** 사용할 수 없는 경우 기본적으로 **Timeseriesid**로 설정 됩니다. 이름이 제공 되는 경우에도 **Timeseriesid** 는 여전히 사용할 수 [있습니다.](time-series-insights-update-explorer.md#4-time-series-well) |
| description | 인스턴스에 대 한 텍스트 설명입니다. |
| 계층 Id | 인스턴스가 속한 계층을 정의 합니다. |
| instanceFields | 인스턴스 속성 및 인스턴스를 정의 하는 정적 데이터입니다. 계층 구조 또는 비 계층 구조의 값을 정의하는 한편 검색 작업을 수행하기 위한 인덱싱도 지원합니다. |

> [!NOTE]
> 계층은 인스턴스 필드를 사용 하 여 빌드됩니다. 추가 인스턴스 속성 정의에 대해 추가 **Instancefields** 를 추가할 수 있습니다.

인스턴스에는 다음과 같은 JSON 표현이 있습니다.

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> 인스턴스 API 만들기, 읽기, 업데이트 및 삭제 (CRUD) 지원에 대 한 자세한 내용은 [데이터 쿼리](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) 문서 및 [인스턴스 API REST 설명서](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#instances-api)를 참조 하세요.

## <a name="time-series-model-hierarchies"></a>시계열 모델 계층 구조

시계열 모델 *계층* 은 속성 이름 및 해당 관계를 지정 하 여 인스턴스를 구성 합니다.

지정 된 Azure Time Series Insights Gen2 환경에서 여러 계층을 구성할 수 있습니다. 시계열 모델 인스턴스는 단일 계층 구조 나 여러 계층 (다 대 다 관계)에 매핑될 수 있습니다.

[Contoso 바람 팜 데모](https://insights.timeseries.azure.com/preview/samples) 에서는 표준 인스턴스 및 유형 계층 구조를 표시 합니다.

[![시계열 모델 계층 구조 예](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>계층 정의

계층은 계층 **id**, **이름**및 **원본**에 의해 정의 됩니다.

| 속성 | Description |
| ---| ---|
| id | 예를 들어 인스턴스를 정의할 때 사용 되는 계층의 고유 식별자입니다. |
| name | 계층의 이름을 제공 하는 데 사용 되는 문자열입니다. |
| source | 사용자가 만들려는 계층의 하향식 부모 자식인 조직의 계층 또는 경로를 지정 합니다. 부모-자식 속성은 인스턴스 필드로 매핑됩니다. |

계층은 JSON에서 다음과 같이 표시 됩니다.

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

이전 JSON 예제에서 다음을 수행 합니다.

* `Location` 부모 및 자식 계층을 정의 `states` `cities` 합니다. 각에는 `location` 여러가 있을 수 있으며 `states` ,이 경우에는 여러가 있을 수 있습니다 `cities` .
* `ManufactureDate` 부모 및 자식 계층을 정의 `year` `month` 합니다. 각에는 `ManufactureDate` 여러가 있을 수 있으며 `years` ,이 경우에는 여러가 있을 수 있습니다 `months` .

> [!TIP]
> 계층 API 만들기, 읽기, 업데이트 및 삭제 (CRUD) 지원에 대 한 자세한 내용은 [데이터 쿼리](concepts-query-overview.md#time-series-model-query-tsm-q-apis) 문서 및 [계층 API REST 설명서](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#hierarchies-api)를 참조 하세요.

### <a name="hierarchy-example"></a>계층 예

**H1** `building` `floor` `room` **InstanceFieldNames** 정의의 일부로 계층 H1에, 및가 있는 경우를 예로 들어 보겠습니다.

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

이전 정의 및 여러 시계열에서 사용 되는 인스턴스 필드가 지정 된 경우 계층 특성 및 값은 다음 표와 같이 표시 됩니다.

| Time Series ID | 인스턴스 필드 |
| --- | --- |
| ID1 | "빌딩" = "1000", "floor" = "10", "room" = "55"  |
| ID2 | "빌딩" = "1000", "방" = "55" |
| ID3 | "floor" = "10" |
| ID4 | "빌딩" = "1000", "floor" = "10"  |
| ID5 | "건물", "층" 또는 "방"은 설정 되지 않았습니다. |

시계열 **ID1** 및 **ID4** 는 완전히 정의 되 고 올바르게 정렬 된 *빌딩*, *floor*및 *대화방* 매개 변수를 포함 하므로 [Azure Time Series Insights tsi 탐색기](time-series-insights-update-explorer.md) 에서 계층 **H1** 의 일부로 표시 됩니다.

다른 항목은 지정 된 데이터 계층 구조를 준수 하지 않기 때문에 *Unparented 인스턴스로* 분류 됩니다.

## <a name="time-series-model-types"></a>시계열 모델 형식

시계열 모델 *형식*은 계산을 수행하기 위한 변수 또는 수식을 정의하는 데 도움이 됩니다. 형식은 특정 인스턴스와 연결 됩니다.

형식에는 하나 이상의 변수가 포함될 수 있습니다. 예를 들어 시계열 모델 인스턴스는 *평균 온도*, *최소 온도*및 *최대 온도*를 변수로 구성 된 *온도 센서*형식일 수 있습니다.

[Contoso 바람 팜 데모](https://insights.timeseries.azure.com/preview/samples) 는 해당 인스턴스와 연결 된 여러 시계열 모델 유형을 시각화 합니다.

[![시계열 모델 유형 예](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> 형식 API 만들기, 읽기, 업데이트 및 삭제 (CRUD) 지원에 대 한 자세한 내용은 [데이터 쿼리](concepts-query-overview.md#time-series-model-query-tsm-q-apis) 문서 및 [형식 API REST 설명서](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis#types-api)를 참조 하세요.

### <a name="type-properties"></a>형식 속성

시계열 모델 유형은 **id**, **이름**, **설명**및 **변수로**정의 됩니다.

| 속성 | Description |
| ---| ---|
| id | 형식에 대 한 대/소문자를 구분 하는 고유 문자열 ID입니다. |
| name | 형식의 이름을 제공 하는 데 사용 되는 문자열입니다. |
| description | 형식에 대 한 문자열 설명입니다. |
| variables | 형식과 연결 된 변수를 지정 합니다. |

형식은 다음 JSON 예제를 따릅니다.

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event['Speed-Sensor'].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "right($value)"
          }
        }
      }
    }
  ]
}
```

시계열 모델 유형에는 이벤트에 대 한 수식 및 계산 규칙을 지정 하는 여러 변수가 있을 수 있습니다. [시계열 모델 변수를 정의 하는 방법](./concepts-variables.md) 에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

* Api를 통해 모델을 편집 하는 방법에 대 한 자세한 내용은 [Time Series 모델](https://docs.microsoft.com/rest/api/time-series-insights/reference-model-apis) 참조 설명서를 참조 하세요.

* 시계열 [모델 변수](./concepts-variables.md) 를 사용 하 여 만들 수 있는 수식 및 계산 살펴보기

* Azure Time Series Insights Gen2에서 [데이터를 쿼리](concepts-query-overview.md) 하는 방법에 대해 알아봅니다.
