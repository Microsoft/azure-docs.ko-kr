---
title: Azure Cosmos DB의 스토리지 비용 최적화
description: 이 문서에서는 Azure Cosmos DB에 저장된 데이터에 대한 스토리지 비용을 관리하는 방법을 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: aed32db257ddbc8cb5b8f4af5f27024c23583798
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928916"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Azure Cosmos DB의 스토리지 비용 최적화

Azure Cosmos DB는 무제한 스토리지 및 처리량을 제공합니다. Azure Cosmos 컨테이너 또는 데이터베이스에서 프로비전/구성해야 하는 처리량과 달리, 스토리지는 사용량에 따라 요금이 청구됩니다. 사용하는 논리적 스토리지에 대해서만 요금이 청구되며, 스토리지를 미리 예약할 필요가 없습니다. 스토리지는 Azure Cosmos DB 컨테이너에 추가하거나 제거하는 데이터에 따라 자동으로 확장되고 축소됩니다.

## <a name="storage-cost"></a>저장소 비용

스토리지는GB 단위로 요금이 청구됩니다. 로컬 SSD 지원 스토리지는 데이터 및 인덱싱에 사용됩니다. 사용된 총 스토리지는 Azure Cosmos DB를 사용하는 모든 지역에서 사용되는 데이터 및 인덱스에 필요한 스토리지와 같습니다. Azure Cosmos 계정을 세 지역에 전역으로 복제하는 경우 이러한 세 지역의 총 스토리지 비용을 지불합니다. 스토리지 요구 사항을 예측하려면 [Capacity Planner](https://www.documentdb.com/capacityplanner) 도구를 참조하세요. Azure Cosmos DB의 스토리지 비용은 $0.25GB/월입니다. 최신 업데이트는 [가격 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요. 경고를 설정하여 Azure Cosmos 컨테이너에서 사용하는 스토리지를 확인할 수 있습니다. 스토리지를 모니터링하려면 [Azure Cosmos DB 모니터링](monitor-accounts.md)을 참조하세요.

## <a name="optimize-cost-with-item-size"></a>항목 크기를 통해 비용 최적화

Azure Cosmos DB에서 최적 성능과 비용 혜택을 얻으려면 항목 크기가 2MB 이하여야 합니다. 2MB 초과 데이터를 저장할 항목이 필요한 경우 항목 스키마를 다시 설계하는 것이 좋습니다. 드물긴 하지만 스키마를 다시 설계할 수 없는 경우에는 항목을 하위 항목으로 분할하고 공통 ID(식별자)를 사용하여 논리적으로 연결할 수 있습니다. 모든 Azure Cosmos DB 기능은 해당 논리적 식별자를 기준으로 일관성 있게 작동합니다.

## <a name="optimize-cost-with-indexing"></a>인덱싱을 통해 비용 최적화

기본적으로 데이터는 자동으로 인덱싱되므로 사용되는 총 스토리지가 증가할 수 있습니다. 그러나 사용자 지정 인덱스 정책을 적용하여 이 오버헤드를 줄일 수 있습니다. 정책을 통해 튜닝되지 않은 자동 인덱싱은 항목 크기의 약 10-20%입니다. 인덱스 정책을 제거하거나 사용자 지정하면 쓰기에 대한 추가 비용을 지불하지 않으며 추가 처리량 용량도 필요하지 않습니다. 사용자 지정 인덱싱 정책을 구성하려면 [Azure Cosmos DB의 인덱싱](indexing-policies.md)을 참조하세요. 이전에 관계형 데이터베이스를 사용한 경우 “모든 항목 인덱싱”이 두 배 이상의 스토리지를 의미한다고 생각할 수 있습니다. 그러나 Azure Cosmos DB에서는 보통의 경우 사용되는 스토리지가 훨씬 더 적습니다. Azure Cosmos DB의 인덱스는 스토리지 공간을 적게 사용하도록 설계되었으므로 스토리지 오버헤드가 일반적으로 자동 인덱싱에서도 낮습니다(10-20%). 인덱싱 정책을 관리하면 더 세분화된 방식으로 인덱스 공간과 쿼리 성능의 절충을 제어할 수 있습니다.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>TTL(Time to Live) 및 변경 피드를 사용한 비용 최적화

데이터가 더 이상 필요하지 않으면 [TTL(Time to Live)](time-to-live.md), [변경 피드](change-feed.md)를 사용하여 Azure Cosmos 계정에서 정상적으로 데이터를 삭제하거나, Azure Blob Storage, Azure 데이터 웨어하우스 등의 다른 데이터 저장소로 이전 데이터를 마이그레이션할 수 있습니다. Azure Cosmos DB는 Time to Live, 즉 TTL을 통해 일정 기간이 지난 후 컨테이너에서 항목을 자동으로 삭제하는 기능을 제공합니다. 기본적으로 컨테이너 수준에서 TTL(Time to Live)을 설정하거나 항목별 기준으로 값을 재정의할 수 있습니다. 컨테이너 또는 항목 수준에서 TTL을 설정할 경우 Azure Cosmos DB는 마지막으로 수정한 시간으로부터 해당 시간이 지나면 이러한 항목을 자동으로 제거합니다. 변경 피드를 사용하면 Azure Cosmos DB의 다른 컨테이너 또는 외부 데이터 저장소로 데이터를 마이그레이션할 수 있습니다. 마이그레이션의 가동 중지 시간은 0이며, 마이그레이션이 완료되면 원본 Azure Cosmos 컨테이너를 삭제하거나 삭제하도록 TTL(Time to Live)을 구성할 수 있습니다.

## <a name="optimize-cost-with-rich-media-data-types"></a>리치 미디어 데이터 형식을 통해 비용 최적화 

리치 미디어 유형(예: 비디오, 이미지 등)을 저장하려는 경우 Azure Cosmos DB에 여러 옵션이 있습니다. 옵션 중 하나는 이러한 리치 미디어 유형을 Azure Cosmos 항목으로 저장하는 것입니다. 항목당 2MB 제한이 있으며, 데이터 항목을 여러 하위 항목에 연결하여 이 제한을 피할 수 있습니다. 또는 Azure Blob Storage에 저장하고 메타데이터를 사용하여 Azure Cosmos 항목에서 참조할 수 있습니다. 이 접근 방법에는 많은 장단점이 있습니다. 첫 번째 접근 방법은 일반적인 Azure Cosmos 항목 외에 리치 미디어 데이터 유형에 대한 대기 시간, 처리량 SLA 및 턴키 전역 분산 기능 측면에서 최상의 성능을 제공합니다. 그러나 지원이 더 높은 가격으로 제공됩니다. Azure Blob Storage에 미디어를 저장하면 전체 비용을 줄일 수 있습니다. 대기 시간이 중요한 경우 Azure Cosmos 항목에서 참조되는 리치 미디어 파일에 대해 프리미엄 스토리지를 사용할 수 있습니다. 이 경우 CDN과의 네이티브 통합을 통해 저렴한 비용으로 에지 서버의 이미지를 제공하여 지역 제한을 우회합니다. 이 시나리오의 단점은 Azure Cosmos DB 및 Azure Blob Storage의 두 서비스를 처리해야 하므로 운영 비용이 증가할 수 있다는 것입니다. 

## <a name="check-storage-consumed"></a>사용한 스토리지 확인

Azure Cosmos 컨테이너의 스토리지 사용을 확인하려면 컨테이너에서 HEAD 또는 GET 요청을 실행하고 `x-ms-request-quota` 및 `x-ms-request-usage` 헤더를 검사할 수 있습니다. 또는.NET SDK를 사용할 때 사용할 수는 [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), 및 [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) 소비 되는 저장소를 가져올 속성입니다.

## <a name="using-sdk"></a>SDK 사용

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보

