---
title: Azure Digital Twins의 쿼리 단위
titleSuffix: Azure Digital Twins
description: Azure Digital Twins에서 쿼리 단위의 청구 개념 이해
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c68cb8cc0ecf759b9af0e313e09663cdbc327917
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067708"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure Digital Twins의 쿼리 단위 

Azure Digital Twins **쿼리 단위 (QU)** 는 [쿼리 API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)를 사용 하 여 [azure digital twins 쿼리](how-to-query-graph.md) 를 실행 하는 데 사용 되는 주문형 계산 단위입니다. 

Azure Digital Twins에서 지 원하는 쿼리 작업을 수행 하는 데 필요한 CPU, IOPS 및 메모리와 같은 시스템 리소스를 추상화 하 여 쿼리 단위 사용을 추적할 수 있습니다.

쿼리를 실행 하는 데 사용 되는 쿼리 단위의 크기는 다음의 영향을 받습니다.
* 쿼리의 복잡성 
* 결과 집합의 크기 (결과 10 개를 반환 하는 쿼리는 하나의 결과만 반환 하는 비슷한 복잡성의 쿼리 보다 더 많은 QUs를 사용 함)

이 문서에서는 쿼리 단위를 이해 하 고 쿼리 단위 소비를 추적 하는 방법을 설명 합니다.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Azure Digital Twins에서 쿼리 단위 소비 찾기 

Azure Digital Twins [쿼리 API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query)를 사용 하 여 쿼리를 실행 하는 경우 응답 헤더를 검사 하 여 쿼리에 사용 된 QUs 수를 추적할 수 있습니다. Azure Digital Twins에서 다시 전송 된 응답에서 "쿼리 요금"을 찾습니다. 

Azure Digital Twins [sdk](how-to-use-apis-sdks.md) 를 사용 하면 페이징할 수 있는 응답에서 쿼리 요금 헤더를 추출할 수 있습니다. 이 섹션에서는 디지털 쌍을 쿼리 하는 방법 및 페이징할 수 있는 응답을 반복 하 여 쿼리 요금 헤더를 추출 하는 방법을 보여 줍니다. 

다음 코드 조각에서는 쿼리 API를 호출할 때 발생 하는 쿼리 요금을 추출 하는 방법을 보여 줍니다. 먼저 응답 페이지를 반복 하 여 쿼리 요금 헤더에 액세스 한 다음 각 페이지 내의 디지털 쌍 결과를 반복 합니다. 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>다음 단계

Azure Digital Twins 쿼리에 대 한 자세한 내용은 다음을 참조 하세요.
* [*개념: 쿼리 언어*](concepts-query-language.md)
* [*방법: 쌍 그래프 쿼리*](how-to-query-graph.md)
* [쿼리 API 참조 설명서](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

Azure Digital Twins 쿼리 관련 제한은 [*참조: 공개 미리 보기로*](reference-service-limits.md)제공 되는 서비스 제한에서 찾을 수 있습니다.