---
title: 사용 중지된 Azure Cosmos DB 성능 수준
description: Azure Cosmos DB에서 이전에 사용할 수 있는 S1, S2 및 S3 성능 수준에 대해 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928293"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>S1, S2 및 S3 성능 수준 사용 중지

> [!IMPORTANT] 
> 이 문서에서 설명하는 S1, S2 및 S3 성능 수준은 이제 사용 중지되어 새 Azure Cosmos DB 계정에 더 이상 사용할 수 없습니다.
>

이 문서에서는 S1, S2 및 S3 성능 수준에 대한 개요를 간략히 설명하고, 이러한 성능 수준을 사용하는 컬렉션을 단일 파티션 컬렉션으로 마이그레이션하는 방법에 대해 설명합니다. 이 문서를 읽은 다음에는 다음과 같은 질문에 답할 수 있습니다.

- [S1, S2 및 S3 성능 수준의 사용이 중지되는 이유는?](#why-retired)
- [단일 파티션 컬렉션 및 분할된 컬렉션을 S1, S2, S3 성능 수준과 비교하면 어떻습니까?](#compare)
- [내 데이터에 중단 없이 액세스하려면 어떻게 해야 합니까?](#uninterrupted-access)
- [마이그레이션하면 내 컬렉션이 어떻게 변경됩니까?](#collection-change)
- [단일 파티션 컬렉션으로 마이그레이션하면 요금 청구는 어떻게 변경됩니까?](#billing-change)
- [저장 용량이 10GB 이상 필요한 경우 어떻게 해야 합니까?](#more-storage-needed)
- [계획된 마이그레이션 이전에 S1, S2 및 S3 성능 수준을 변경할 수 있습니까?](#change-before)
- [S1, S2, S3 성능 수준에서 단일 파티션 컬렉션으로 마이그레이션하려면 어떻게 해야 합니까?](#migrate-diy)
- [EA 고객에게 미치는 영향은?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>S1, S2 및 S3 성능 수준의 사용이 중지되는 이유는?

S1, S2 및 S3 성능 수준은 표준 Azure Cosmos DB 제품이 제공하는 유연성을 제공하지 않습니다. S1, S2, S3 성능 수준에서는 처리량과 저장소 용량이 둘 다 미리 설정되었으며 탄력성을 제공하지 않았습니다. 이제 Azure Cosmos DB는 처리량과 저장소를 사용자 지정할 수 있는 기능을 제공하므로 크기 조정 기능을 필요에 따라 훨씬 더 유연하게 제공할 수 있습니다.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>단일 파티션 컬렉션 및 분할된 컬렉션을 S1, S2, S3 성능 수준과 비교하면 어떻습니까?

다음 표에서는 단일 파티션 컬렉션, 분할된 컬렉션 및 S1, S2, S3 성능 수준에서 사용할 수 있는 처리량 및 저장소 옵션을 비교합니다. 다음은 미국 동부 2 지역의 예입니다.

|   |분할된 컬렉션|단일 파티션 컬렉션|S1|S2|S3|
|---|---|---|---|---|---|
|최대 처리량|Unlimited|10,000RU/s|250RU/s|1,000RU/s|2,500RU/s|
|최소 처리량|2,500RU/s|400RU/s|250RU/s|1,000RU/s|2,500RU/s|
|최대 저장소|Unlimited|10 GB|10GB|10GB|10 GB|
|가격(월별)|처리량: $6/100RU/s<br><br>저장소: $0.25/GB|처리량: $6/100RU/s<br><br>저장소: $0.25/GB|$25(미화)|$50(미화)|$100(미화)|

EA 고객입니까? 그렇다면 [EA 고객에게 미치는 영향은?](#ea-customer)을 참조하세요.

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>내 데이터에 중단 없이 액세스하려면 어떻게 해야 합니까?

S1, S2 또는 S3 컬렉션이 있는 경우 [.NET SDK를 사용하여](#migrate-diy) 프로그래밍 방식으로 단일 파티션 컬렉션으로 컬렉션을 마이그레이션해야 합니다. 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>마이그레이션하면 내 컬렉션이 어떻게 변경됩니까?

S1 컬렉션이 있는 경우 400RU/s 처리량의 단일 파티션 컬렉션으로 마이그레이션할 수 있습니다. 400RU/s는 단일 파티션 컬렉션에서 사용할 수 있는 최소 처리량입니다. 그러나 400RU/s 단일 파티션 컬렉션에 대한 비용은 250RU/s S1 컬렉션에 지불한 비용과 거의 동일하며, 사용 가능한 여분의 150RU/s에 대해 지불하지 않습니다.

S2 컬렉션이 있는 경우 1,000RU/s 처리량의 단일 파티션 컬렉션으로 마이그레이션할 수 있습니다. 처리량 수준은 변경되지 않습니다.

S3 컬렉션이 있는 경우 2,500RU/s 처리량의 단일 파티션 컬렉션으로 마이그레이션할 수 있습니다. 처리량 수준은 변경되지 않습니다.

이러한 각각의 경우에서 컬렉션을 마이그레이션한 후에는 처리량 수준을 사용자 지정하거나 필요에 따라 크기를 조정하여 대기 시간이 짧은 액세스를 사용자에게 제공할 수 있습니다. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>단일 파티션 컬렉션으로 마이그레이션한 후 요금 청구는 어떻게 변경됩니까?

미국 동부 지역에 각각 10개의 S1 컬렉션(각각 1GB 저장소 사용)이 있고, 이러한 10개 S1 컬렉션을 400RU/s(최소 수준)의 10개 단일 파티션 컬렉션으로 마이그레이션한다고 가정합니다. 한 달 동안 10개의 단일 파티션 컬렉션을 유지하는 경우 청구서는 다음과 같이 표시됩니다.

![10개 S1 컬렉션 및 10개 단일 파티션 컬렉션에 대한 가격 비교](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>저장 용량이 10GB 이상 필요한 경우 어떻게 해야 합니까?

S1, S2 또는 S3 성능 수준 컬렉션 또는 단일 파티션 컬렉션이 있든 간에 모두 10GB 저장소를 사용할 수 있는 경우 Azure Cosmos DB 데이터 마이그레이션 도구를 사용하여 거의 무제한 저장소가 있는 분할된 컬렉션으로 데이터를 마이그레이션할 수 있습니다. 분할된 컬렉션의 이점에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](sql-api-partition-data.md)을 참조하세요. 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>계획된 마이그레이션 이전에 S1, S2 및 S3 성능 수준을 변경할 수 있습니까?

S1, S2 및 S3 성능을 갖춘 기존 계정만 [.NET SDK를 사용하여](#migrate-diy) 프로그래밍 방식으로 성능 수준 계층을 변경할 수 있습니다. S1, S3 또는 S3에서 단일 파티션 컬렉션으로 변경하면 S1, S2 또는 S3 성능 수준으로 되돌릴 수 없습니다.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>S1, S2, S3 성능 수준에서 단일 파티션 컬렉션으로 마이그레이션하려면 어떻게 해야 합니까?

[.NET SDK를 사용하여](#migrate-diy) 프로그래밍 방식으로 S1, S2 및 S3 성능 수준에서 단일 파티션 컬렉션으로 마이그레이션할 수 있습니다. 계획된 마이그레이션 이전에 이 작업을 직접 수행하여 단일 파티션 컬렉션에서 사용할 수 있는 유연한 처리량 옵션을 활용할 수 있습니다.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>.NET SDK를 사용하여 단일 파티션 컬렉션으로 마이그레이션

이 섹션에서는 [SQL .NET API](sql-api-sdk-dotnet.md)를 사용한 컬렉션의 성능 수준 변경에 대해서만 다루고 있으나, 다른 SDK의 경우에도 프로세스는 유사합니다.

다음 코드 조각에서는 컬렉션 처리량을 5,000RU/s로 변경합니다.
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

offer 메서드에 대한 자세한 내용 및 추가 예제를 보려면 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx)을 방문하세요.

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>EA 고객에게 미치는 영향은?

EA 고객에게 적용한 가격은 현재 계약이 종료될 때까지 보호됩니다.

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB의 가격 책정 및 데이터 관리에 대한 자세한 내용은 다음 리소스를 참조하세요.

1.  [Cosmos DB의 데이터 분할](sql-api-partition-data.md). 단일 파티션 컨테이너와 분할된 컨테이너 간의 차이점을 이해하고 매끄럽게 크기를 조정하는 분할 전략을 구현하는 데 유용한 팁을 알아봅니다.
2.  [Cosmos DB 가격 책정](https://azure.microsoft.com/pricing/details/cosmos-db/). 처리량 프로비전 및 저장소 소비 비용에 대해 알아봅니다.
3.  [요청 단위](request-units.md) 읽기, 쓰기, 쿼리와 같은 다양한 작업 유형의 처리량 사용에 대해 알아봅니다.
