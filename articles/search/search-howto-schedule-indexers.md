---
title: 인덱서 실행 예약
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱서를 예약 하 여 콘텐츠를 주기적으로 또는 특정 시간에 인덱싱합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: b77eaec0440aa4fcd22d7b35e7a205b0276164f2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935826"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서를 예약 하는 방법

인덱서는 생성 후 즉시 한 번 실행 됩니다. 포털, REST API 또는 .NET SDK를 사용 하 여 요청 시 다시 실행할 수 있습니다. 정기적으로 일정에 따라 실행 되도록 인덱서를 구성할 수도 있습니다.

인덱서 일정이 유용 하 게 사용할 수 있는 경우는 다음과 같습니다.

* 원본 데이터는 시간이 지남에 따라 변경 되며 Azure Cognitive Search 인덱서는 변경 된 데이터를 자동으로 처리 하려고 합니다.
* 인덱스는 여러 데이터 원본에서 채워지고 인덱서는 충돌을 줄이기 위해 서로 다른 시간에 실행 되도록 합니다.
* 원본 데이터가 매우 크고 인덱서 처리를 시간에 따라 분산 하려고 합니다. 대량의 데이터를 인덱싱하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search에서 대용량 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)을 참조 하세요.

스케줄러는 Azure Cognitive Search의 기본 제공 기능입니다. 외부 스케줄러를 사용 하 여 검색 인덱서를 제어할 수 없습니다.

## <a name="define-schedule-properties"></a>일정 속성 정의

인덱서 일정에는 두 가지 속성이 있습니다.
* 예약 된 인덱서 실행 간의 시간을 정의 하는 **간격**입니다. 허용 되는 최소 간격은 5 분이 고 최대값은 24 시간입니다.
* **시작 시간 (UTC)**-처음으로 인덱서를 실행 해야 하는 시간을 나타냅니다.

인덱서를 처음 만들 때 또는 나중에 인덱서의 속성을 업데이트 하 여 일정을 지정할 수 있습니다. 인덱서 일정은 [포털](#portal), [REST API](#restApi)또는 [.net SDK](#dotNetSdk)를 사용 하 여 설정할 수 있습니다.

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 다음 실행이 예약 될 때 인덱서가 이미 실행 중인 경우 해당 실행은 예약 된 다음 시간까지 연기 됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. 시간 간격으로 시간 **간격** 으로 인덱서 일정을 구성 하 고 **시작 시간** 을 2019 년 6 월 1 일 오전 8:00:00 UTC로 구성 한다고 가정 합니다. 인덱서 실행이 1 시간 이상 소요 되는 경우 다음과 같은 상황이 발생할 수 있습니다.

* 첫 번째 인덱서 실행은 2019 년 6 월 1 일 또는 오전 8:00 UTC에 시작 됩니다. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
* 두 번째 실행은 6 월 1 일 또는 2019 9:00 오전에 시작 됩니다. 이 실행에는 1 시간 이상 70 분이 소요 되는 것으로 가정 하 고 10:10 AM UTC까지 완료 되지 않습니다.
* 세 번째 실행은 오전 10:00 시에 시작 되도록 예약 되어 있지만이 시점에서 이전 실행은 계속 실행 됩니다. 그런 다음이 예약 된 실행을 건너뜁니다. 다음 인덱서 실행은 오전 11:00에서 오전까지 시작 되지 않습니다.

> [!NOTE]
> 인덱서가 특정 일정으로 설정 되어 있지만 실행 될 때마다 같은 문서를 반복 해 서 반복 해 서 실패 하는 경우 인덱서는 성공적으로 다시 진행 될 때까지 빈도가 낮은 간격으로 (최대 24 시간까지) 실행을 시작 합니다.  특정 지점에서 인덱서를 발생 시킨 문제를 해결 한 것으로 판단 되는 경우 인덱서의 요청 시 실행을 수행할 수 있으며, 성공적으로 진행 되 면 인덱서가 설정 된 일정 간격으로 다시 반환 됩니다.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>포털의 일정

포털에서 데이터 가져오기 마법사를 사용 하 여 만들 때 인덱서의 일정을 정의할 수 있습니다. 기본 일정 설정은 **매시간**이며이는 인덱서를 만든 후 한 번 실행 하 고 나중에 1 시간 마다 다시 실행 함을 의미 합니다.

인덱서를 자동으로 다시 실행 하지 않으려는 경우 또는 **매일** 한 번씩 실행 되도록 일정 설정을 **한 번** 으로 변경할 수 있습니다. 다른 간격이 나 특정 이후 시작 시간을 지정 하려면 **사용자 지정** 으로 설정 합니다.

일정을 **사용자 지정**으로 설정 하는 경우 **간격** 및 **시작 시간 (UTC)** 을 지정할 수 있는 필드가 표시 됩니다. 허용 되는 최단 시간 간격은 5 분이 고 가장 긴 시간은 1440 분 (24 시간)입니다.

   ![데이터 가져오기 마법사에서 인덱서 일정 설정](media/search-howto-schedule-indexers/schedule-import-data.png "데이터 가져오기 마법사에서 인덱서 일정 설정")

인덱서를 만든 후에는 인덱서의 편집 패널을 사용 하 여 일정 설정을 변경할 수 있습니다. 일정 필드는 데이터 가져오기 마법사와 동일 합니다.

   ![인덱서 편집 패널에서 일정 설정](media/search-howto-schedule-indexers/schedule-edit.png "인덱서 편집 패널에서 일정 설정")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>REST Api를 사용 하 여 예약

REST API를 사용 하 여 인덱서의 일정을 정의할 수 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 포함 합니다. 다음 예에서는 기존 인덱서를 업데이트 하는 PUT 요청을 보여 줍니다.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

선택적 **startTime** 은 예약 된 실행을 시작 해야 하는 시기를 나타냅니다. 생략한 경우 현재 UTC 시간이 사용됩니다. 이 시간은 이전에 일 수 있습니다 .이 경우 첫 번째 실행은 인덱서가 원래 **startTime**이후에도 계속 실행 되 고 있는 것 처럼 예약 됩니다.

인덱서 실행 호출을 사용 하 여 언제 든 지 요청 시 인덱서를 실행할 수도 있습니다. 인덱서를 실행 하 고 인덱서 일정을 설정 하는 방법에 대 한 자세한 내용은 REST API 참조에서 [인덱서 실행](/rest/api/searchservice/run-indexer), [인덱서 가져오기](/rest/api/searchservice/get-indexer)및 [업데이트 인덱서](/rest/api/searchservice/update-indexer) 를 참조 하세요.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>.NET SDK를 사용 하 여 예약

Azure Cognitive Search .NET SDK를 사용 하 여 인덱서 일정을 정의할 수 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 포함 합니다.

다음 c # 예제에서는 미리 정의 된 데이터 원본 및 인덱스를 사용 하 여 인덱서를 만들고 지금부터 30 분부터 30 분 마다 실행 되도록 일정을 설정 합니다.

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
**일정** 매개 변수를 생략 하면 인덱서가 생성 된 후 즉시 실행 됩니다.

**StartTime** 매개 변수는 과거 시간으로 설정할 수 있습니다. 이 경우 첫 번째 실행은 지정 된 **startTime**이후에 인덱서가 지속적으로 실행 된 것 처럼 예약 됩니다.

일정은 [Indexingschedule](/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) 클래스를 사용 하 여 정의 됩니다. **Indexingschedule** 생성자에는 **TimeSpan** 개체를 사용 하 여 지정 된 **interval** 매개 변수가 필요 합니다. 허용 되는 최소 간격 값은 5 분이 고 최대값은 24 시간입니다. **DateTimeOffset** 개체로 지정 된 두 번째 **startTime** 매개 변수는 선택 사항입니다.

.NET SDK를 사용 하면 **IIndexersOperations** 인터페이스에서 메서드를 구현 하는 [SearchServiceClient](/dotnet/api/microsoft.azure.search.searchserviceclient) 클래스 및 [인덱서](/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) 속성을 사용 하 여 인덱서 작업을 제어할 수 있습니다. 

[Run](/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [Runasync](/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)또는 [RunWithHttpMessagesAsync](/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) 메서드 중 하나를 사용 하 여 언제 든 지 요청 시 인덱서를 실행할 수 있습니다.

인덱서를 만들고 업데이트 하 고 실행 하는 방법에 대 한 자세한 내용은 [IIindexersOperations](/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)를 참조 하세요.