---
title: Azure Cosmos DB에서 날짜 사용
description: Azure Cosmos DB에서 DataTime 개체를 저장, 인덱싱 및 쿼리 하는 방법에 대해 알아봅니다.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7a524d34fcf8e629914d4fbc4669caada210866b
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019191"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Azure Cosmos DB에서 날짜 사용

Azure Cosmos DB는 네이티브 [JSON](https://www.json.org) 데이터 모델을 통해 스키마 유연성과 풍부한 인덱싱을 제공합니다. 데이터베이스, 컨테이너, 문서 및 저장 프로시저를 포함한 모든 Azure Cosmos DB 리소스는 모델링되어 JSON 문서로 저장됩니다. 이식 가능성을 위한 요구 사항인 JSON(및 Azure Cosmos DB)은 String, Number, Boolean, Array, Object 및 Null과 같은 기본 형식만 지원합니다. 그러나 JSON은 유연하므로 개발자와 프레임워크는 이러한 기본형을 사용하여 개체 또는 배열로 구성하여 보다 복잡한 형식을 나타낼 수 있습니다.

기본형 외에도 많은 애플리케이션에는 날짜 및 타임스탬프를 나타내기 위해 DateTime 형식이 필요합니다. 이 문서에서는 개발자가 .NET SDK를 사용하여 Azure Cosmos DB에서 날짜를 저장, 검색 및 쿼리하는 방법에 대해 설명합니다.

## <a name="storing-datetimes"></a>날짜/시간 저장

Azure Cosmos DB는-string, number, boolean, null, array, object 등의 JSON 형식을 지원 합니다. DateTime 형식을 직접 지원 하지 않습니다. 현재 Azure Cosmos DB는 날짜 지역화를 지원 하지 않습니다. 따라서 DateTimes를 문자열로 저장 해야 합니다. Azure Cosmos DB의 DateTime 문자열에 권장 되는 형식은 `yyyy-MM-ddTHH:mm:ss.fffffffZ` ISO 8601 UTC 표준을 따릅니다. Azure Cosmos DB의 모든 날짜를 UTC로 저장 하는 것이 좋습니다. 날짜 문자열을이 형식으로 변환 하면 날짜를 사전순으로 정렬할 수 있습니다. 비 UTC 날짜를 저장 한 경우에는 클라이언트 쪽에서 논리를 처리 해야 합니다. 현지 DateTime을 UTC로 변환 하려면 오프셋을 JSON에서 속성으로 인식/저장 해야 하며 클라이언트는이 오프셋을 사용 하 여 UTC 날짜/시간 값을 계산할 수 있습니다.

Datetime 문자열을 필터로 사용 하는 범위 쿼리는 DateTime 문자열이 모두 UTC이 고 길이가 동일한 경우에만 지원 됩니다. Azure Cosmos DB [Getcurrentdatetime](sql-query-getcurrentdatetime.md) 시스템 함수는 현재 UTC 날짜 및 시간 ISO 8601 문자열 값을 형식으로 반환 `yyyy-MM-ddTHH:mm:ss.fffffffZ` 합니다.

대부분의 애플리케이션에서는 다음과 같은 이유로 DateTime의 기본 문자열 표현을 사용할 수 있습니다.

* 문자열을 비교할 수 있으며, 문자열로 변환할 때 DateTime 값의 상대적인 순서는 유지됩니다.
* 이 접근 방식에는 JSON 변환에 대해 사용자 지정 코드 또는 속성이 필요하지 않습니다.
* JSON에 저장된 날짜는 사람이 읽을 수 있습니다.
* 이 접근 방식은 빠른 쿼리 성능을 위해 Azure Cosmos DB의 인덱스를 활용할 수 있습니다.

예를 들어 다음 코드 조각에서는 .NET SDK를 사용하여 두 가지 DateTime 속성(`ShipDate` 및 `OrderDate`)을 문서로 포함하는 `Order` 개체를 저장합니다.

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

이 문서는 다음과 같이 Azure Cosmos DB에 저장됩니다.

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

또는 DateTime을 Unix 타임스탬프, 즉 1970년 1월 1일 이후로 경과된 초 수를 나타내는 숫자로 저장할 수 있습니다. Azure Cosmos DB의 내부 타임스탬프(`_ts`) 속성은 이 접근 방식을 따릅니다. [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) 클래스를 사용하여 DateTime을 숫자로 직렬화할 수 있습니다.

## <a name="querying-datetimes-in-linq"></a>LINQ에서 날짜/시간 쿼리

SQL .NET SDK는 LINQ를 통해 Azure Cosmos DB에 저장된 데이터의 쿼리를 자동으로 지원합니다. 예를 들어, 다음 코드 조각은 지난 3 일간 배송 된 주문을 필터링 하는 LINQ 쿼리를 보여 줍니다.

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

다음 SQL 문으로 변환 되 고 Azure Cosmos DB에서 실행 됩니다.

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

[Linq에서 Cosmos DB를 쿼리하면](sql-query-linq-to-sql.md)AZURE COSMOS DB의 SQL 쿼리 언어와 linq 공급자에 대해 자세히 알아볼 수 있습니다.

## <a name="indexing-datetimes-for-range-queries"></a>범위 쿼리의 날짜/시간 인덱싱

쿼리는 DateTime 값에 공통적으로 사용 됩니다. 이러한 쿼리를 효율적으로 실행 하려면 쿼리 필터의 모든 속성에 인덱스를 정의 해야 합니다.

[Azure Cosmos DB 인덱싱 정책](index-policy.md)에서 인덱싱 정책을 구성하는 방법에 대해 자세히 알아볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [GitHub 샘플 코드](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)(영문)에서 코드 샘플을 다운로드하여 실행합니다.
* [SQL 쿼리](sql-query-getting-started.md)에 대한 자세한 정보
* [Azure Cosmos DB 인덱싱 정책에 대해 알아보기](index-policy.md)
