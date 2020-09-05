---
title: Azure Stream Analytics 출력
description: 이 문서에서는 Azure Stream Analytics에 사용할 수 있는 데이터 출력 옵션을 설명 합니다.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: ade5972226a735ce5d093890091536baf02f5eff
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068986"
---
# <a name="outputs-from-azure-stream-analytics"></a>Azure Stream Analytics 출력

Azure Stream Analytics 작업은 입력, 쿼리 및 출력으로 구성됩니다. 변환 된 데이터를 보낼 수 있는 몇 가지 출력 형식이 있습니다. 이 문서에는 지원 되는 Stream Analytics 출력이 나와 있습니다. Stream Analytics 쿼리를 설계하는 경우 [INTO 절](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)을 사용하여 출력의 이름을 참조합니다. 쿼리에 여러 개의 INTO 절을 추가 하 여 작업당 단일 출력 또는 스트리밍 작업당 여러 출력 (필요한 경우)을 사용할 수 있습니다.

Stream Analytics 작업 출력을 만들고, 편집하고, 테스트하려면 [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output) 및 [Visual Studio](stream-analytics-quick-create-vs.md)를 사용할 수 있습니다.

일부 출력 형식은 처리량을 최적화하기 위해 [분할](#partitioning) 및 다양한 [출력 일괄 처리 크기](#output-batch-size)를 지원합니다. 다음 표에서는 각 출력 형식에 대해 지원 되는 기능을 보여 줍니다.

| 출력 형식 | 분할 | 보안 | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|예|Azure Active Directory 사용자 </br> MSI|
|[Azure SQL Database](sql-database-output.md)|예, 사용하도록 설정해야 합니다.|SQL 사용자 인증 </br> MSI (미리 보기)|
|[Azure Synapse Analytics(미리 보기)](azure-synapse-analytics-output.md)|아니요|SQL 사용자 인증|
|[Blob storage 및 Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|예|MSI </br> 액세스 키|
|[Azure Event Hubs](event-hubs-output.md)|예|액세스 키|
|[Power BI](power-bi-output.md)|아니요|Azure Active Directory 사용자 </br> MSI|
|[Azure Table Storage](table-storage-output.md)|예|계정 키|
|[Azure Service Bus 큐](service-bus-queues-output.md)|예|액세스 키|
|[Azure Service Bus 항목](service-bus-topics-output.md)|예|액세스 키|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|예|액세스 키|
|[Azure Functions](azure-functions-output.md)|예|액세스 키|

## <a name="partitioning"></a>분할

Stream Analytics는 Power BI를 제외 하 고 모든 출력에 대 한 파티션을 지원 합니다. 파티션 키 및 출력 작성기 수에 대 한 자세한 내용은 관심 있는 특정 출력 형식에 대 한 문서를 참조 하세요. 모든 출력 아티클은 이전 섹션에서 연결 됩니다.  

쿼리에서 (INTO INTO) 절을 사용 하 여 출력 기록기의 수를 제어할 수 있습니다 `INTO <partition count>` .이 절은 원하는 작업 토폴로지를 달성 하는 데 도움이 될 수 있습니다. [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 출력 어댑터가 분할 되지 않은 경우 하나의 입력 파티션에 데이터가 없으면 지연 도착 시간이 지연 됩니다. 이러한 경우 출력이 단일 기록기에 병합되어 파이프라인에서 병목 현상이 발생할 수 있습니다. 지연 도착 정책에 대해 알아보려면 [Azure Stream Analytics 이벤트 순서 고려 사항](stream-analytics-out-of-order-and-late-events.md)을 참조하세요.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

모든 출력은 일괄 처리를 지원 하지만 일부는 일괄 처리 크기를 명시적으로 지원 합니다. Azure Stream Analytics는 가변 크기 일괄 처리를 사용하여 이벤트를 처리하고 출력에 씁니다. 일반적으로 Stream Analytics 엔진은 효율성을 위해 한 번에 하나의 메시지를 쓰지 않고 일괄 처리를 사용합니다. 들어오는 이벤트와 나가는 이벤트의 비율이 높으면 Stream Analytics에서 더 큰 일괄 처리를 사용합니다. 송신 속도가 낮은 경우 대기 시간을 낮게 유지하기 위해 더 작은 일괄 처리를 사용합니다.

## <a name="parquet-output-batching-window-properties"></a>Parquet 출력 일괄 처리 창 속성

Azure Resource Manager 템플릿 배포 또는 REST API를 사용 하는 경우 두 개의 일괄 처리 창 속성은 다음과 같습니다.

1. *timeWindow*

   일괄 처리당 최대 대기 시간입니다. 값은 Timespan의 문자열 이어야 합니다. 예를 들어 2 분의 경우 "00:02:00"입니다. 이 시간 후에는 최소 행 요구 사항이 충족 되지 않은 경우에도 일괄 처리가 출력에 기록 됩니다. 기본값은 1 분이 고 허용 되는 최대값은 2 시간입니다. Blob 출력에 경로 패턴 빈도가 있는 경우 대기 시간은 파티션 시간 범위보다 클 수 없습니다.

2. *sizeWindow*

   일괄 처리당 최소 행 수입니다. Parquet의 경우 모든 일괄 처리는 새 파일을 만듭니다. 현재 기본값은 2,000개 행이고, 허용되는 최댓값은 10,000개 행입니다.

이러한 일괄 처리 창 속성은 API 버전 **2017-04-01-미리 보기**에서만 지원 됩니다. 다음은 REST API 호출에 대 한 JSON 페이로드의 예입니다.

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
>
> [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
