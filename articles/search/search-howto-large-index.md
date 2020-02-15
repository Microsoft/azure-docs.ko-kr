---
title: 기본 제공 인덱서를 사용 하 여 대량 데이터 집합 인덱스
titleSuffix: Azure Cognitive Search
description: 일괄 처리 모드, 높아지면, 예약 된 병렬 및 분산 인덱싱을 위한 기술을 통한 대량 데이터 인덱싱 또는 계산 집약적 인덱싱에 대 한 전략입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: 4ad5e961e390b60784355ff3bc72aca4a2f73e11
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190955"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>Azure Cognitive Search에서 대량 데이터 집합을 인덱싱하는 방법

데이터 볼륨이 커지거나 처리 요구가 변경 됨에 따라 단순 또는 기본 인덱싱 전략이 더 이상 실용적이 지 않을 수 있습니다. Azure Cognitive Search의 경우 데이터 업로드 요청을 구조화 하는 방법에서 예약 된 작업 및 분산 된 작업에 대해 원본 관련 인덱서를 사용 하는 것에 이르기까지 더 큰 데이터 집합을 수용 하기 위한 여러 가지 방법이 있습니다.

동일한 기법은 장기 실행 프로세스에도 적용 됩니다. 특히 [병렬 인덱싱](#parallel-indexing) 에 설명 된 단계는 [AI 보강 파이프라인](cognitive-search-concept-intro.md)에서 이미지 분석 또는 자연어 처리와 같은 계산 집약적인 인덱싱에 유용 합니다.

다음 섹션에서는 많은 양의 데이터를 인덱싱하는 세 가지 방법에 대해 설명 합니다.

## <a name="option-1-pass-multiple-documents"></a>옵션 1: 여러 문서 전달

대용량 데이터 세트를 인덱싱하기 위한 가장 간단한 메커니즘 중 하나는 단일 요청에서 여러 문서 또는 레코드를 제출하는 것입니다. 전체 페이로드가 16MB 미만인 한 요청은 대량 업로드 작업에서 최대 1000개의 문서를 처리할 수 있습니다. 이러한 제한은 .NET SDK에서 [문서 추가 REST API](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 또는 [인덱스 메서드](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) 를 사용 하는지에 따라 적용 됩니다. 두 API의 경우 각 요청의 본문에 1000 문서를 패키지할 수 있습니다.

일괄 처리 인덱싱은 REST 또는 .NET을 사용하여 또는 인덱서를 통해 개별 요청에 대해 구현됩니다. 몇 가지 인덱서는 다른 제한에서 작동합니다. 특히 Azure Blob 인덱싱은 큰 평균 문서 크기의 인식으로 10개의 문서에서 일괄 처리 크기를 설정합니다. [만들기 인덱서 REST API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer)에 따른 인덱서의 경우 `BatchSize` 인수를 설정하여 데이터의 특징에 더 잘 일치하도록 이 설정을 사용자 지정할 수 있습니다. 

> [!NOTE]
> 문서 크기를 유지 하기 위해 인덱스에 쿼리할 수 없는 데이터를 추가 하지 마세요. 이미지 및 기타 이진 데이터는 직접 검색할 수 없으므로 인덱스에 저장하지 않아야 합니다. 쿼리할 수 없는 데이터를 검색 결과에 통합하려면 리소스에 대한 URL 참조를 저장하는 검색할 수 없는 필드를 정의해야 합니다.

## <a name="option-2-add-resources"></a>옵션 2: 리소스 추가

[표준 가격 책정 계층](search-sku-tier.md) 중 하나에서 프로비전되는 서비스는 종종 스토리지 및 워크로드(쿼리 또는 인덱싱) 모두에 대해 활용도 낮은 용량을 갖습니다. 이는 [파티션 및 복제본 수를 늘려](search-capacity-planning.md) 더 큰 데이터 세트를 수용하기 위한 확실한 솔루션이 됩니다. 최상의 결과를 위해 스토리지에 대한 파티션 및 데이터 수집 작업에 대한 복제본의 리소스가 필요합니다.

복제본과 파티션을 늘리면 비용이 증가 하는 청구 가능 이벤트가 발생 하지만, 최대 부하 상태에서 연속 인덱싱을 하지 않는 한 인덱싱 프로세스 기간에 대 한 크기 조정을 추가한 다음, 인덱싱이 완료 된 후에 다시 리소스 수준을 다시 설정할 수 있습니다. 날짜.

## <a name="option-3-use-indexers"></a>옵션 3: 인덱서 사용

[인덱서](search-indexer-overview.md) 는 검색 가능한 콘텐츠에 대해 지원 되는 Azure 데이터 원본을 탐색 하는 데 사용 됩니다. 대규모 인덱싱에 대해 명시적으로 의도하지 않으므로 여러 인덱서 기능은 더 큰 데이터 세트를 수용하는 데 특히 유용합니다.

+ 스케줄러를 통해 시간이 지남에 따라 분배할 수 있도록 정기적으로 인덱싱을 분배할 수 있습니다.
+ 예약된 인덱싱은 마지막으로 알려진 중지 지점에서 다시 시작할 수 있습니다. 데이터 원본이 24시간 기간 내에 완벽하게 크롤링되지 않는 경우 인덱서는 중단 위치에서 두 번째 날에 인덱싱을 다시 시작합니다.
+ 데이터를 작은 개별 데이터 원본으로 분할하면 병렬 처리가 가능합니다. Azure Blob storage의 여러 컨테이너와 같이 원본 데이터를 더 작은 구성 요소로 분할 한 다음 동시에 인덱싱할 수 있는 Azure Cognitive Search에 해당 하는 여러 [데이터 원본 개체](https://docs.microsoft.com/rest/api/searchservice/create-data-source) 를 만들 수 있습니다.

> [!NOTE]
> 인덱서는 데이터 소스에 따라 달라 지는 인덱서 방법을 사용 하는 것은 Azure에서 선택한 데이터 원본에 대해서만 사용할 수 있는 [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Blob 저장소](search-howto-indexing-azure-blob-storage.md), [테이블 저장소](search-howto-indexing-azure-tables.md), [Cosmos DB](search-howto-index-cosmosdb.md)입니다.

### <a name="scheduled-indexing"></a>예약된 인덱싱

인덱서 예약은 큰 데이터 세트를 처리하고 인식 검색 파이프라인에서 이미지 분석과 같이 느리게 처리되는 분석을 처리하는 중요한 메커니즘입니다. 인덱서 처리는 24시간 범위 내에 실행됩니다. 24시간 내에 처리가 완료되지 않으면 인덱서 일정 예약의 동작이 사용자에게 유리하게 작용할 수 있습니다. 

기본적으로 예약된 인덱싱은 특정 간격으로 시작되며, 대개 다음으로 예약된 간격에서 다시 시작되기 전에 작업이 완료됩니다. 하지만 간격 내에 처리가 완료되지 않으면 인덱서가 중지됩니다(시간이 초과되기 때문임). 다음 번 간격이 시작되면, 마지막으로 중단된 위치에서 처리가 다시 시작되며, 시스템은 해당 위치를 추적합니다. 

실제 여러 날에 걸쳐 수행되는 인덱스 로드의 경우 24시간 일정으로 인덱서를 배치할 수 있습니다. 다음 번 24시간 주기에서 인덱싱이 다시 시작되면 마지막으로 성공한 문서에서 다시 시작됩니다. 이러한 방식으로 인덱서는 처리되지 않은 모든 문서가 처리될 때까지 여러 날에 걸쳐 문서 백로그를 통해 작업을 수행할 수 있습니다. 이러한 방식에 대한 자세한 내용은 [Azure Blob 스토리지에서 큰 데이터 세트 인덱싱](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)을 참조하세요. 일반적으로 일정을 설정 하는 방법에 대 한 자세한 내용은 [인덱서 REST API 만들기](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer) 또는 [Azure Cognitive Search에 대 한 인덱서를 예약 하는 방법](search-howto-schedule-indexers.md)을 참조 하세요.

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>병렬 인덱싱

병렬 인덱싱 전략은 여러 데이터 원본을 한 번에 인덱싱하는 데 기반하며 여기에서 각 데이터 원본 정의는 데이터의 하위 세트를 지정합니다. 

비 루틴, 계산 집약적 인덱싱 요구 사항의 경우(예제: 인식 검색 파이프라인에서 스캔된 문서의 OCR, 이미지 분석 또는 자연어 처리) 병렬 인덱싱 전략은 종종 짧은 시간에 장기 실행 프로세스를 완료하기 위한 적합한 방법입니다. 쿼리 요청을 제거하거나 줄일 수 있는 경우 쿼리를 동시에 처리하지 않는 서비스에 대한 병렬 인덱싱은 처리 속도가 느린 대량의 콘텐츠를 통한 작업에 최상의 전략 옵션입니다. 

병렬 처리에는 다음과 같은 요소가 있습니다.

+ 원본 데이터를 동일한 컨테이너 내의 여러 컨테이너 또는 여러 가상 폴더로 세분화합니다. 
+ 각 미니 데이터 집합을 고유한 [인덱서와](https://docs.microsoft.com/rest/api/searchservice/create-indexer)쌍으로 고유한 [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)에 매핑합니다.
+ Cognitive Search의 경우 각 인덱서 정의에 동일한 [기술 집합](https://docs.microsoft.com/rest/api/searchservice/create-skillset)을 참조합니다.
+ 동일한 대상 검색 인덱스에 기록합니다. 
+ 모든 인덱서가 동시에 실행되도록 예약합니다.

> [!NOTE]
> Azure Cognitive Search에서는 개별 복제본 또는 파티션을 인덱싱 또는 쿼리 처리에 할당할 수 없습니다. 시스템은 리소스가 사용 되는 방법을 결정 합니다. 쿼리 성능에 미치는 영향을 이해 하려면 프로덕션 환경으로 롤링 하기 전에 테스트 환경에서 병렬 인덱싱을 시도할 수 있습니다.  

### <a name="how-to-configure-parallel-indexing"></a>병렬 인덱싱을 구성하는 방법

인덱서의 경우 처리 용량은 검색 서비스에 사용되는 각 SU(서비스 단위)에 대해 하나의 인덱서 하위 시스템을 기반으로 합니다. 기본 또는 표준 계층에 프로 비전 된 두 개 이상의 복제본이 있는 Azure Cognitive Search 서비스에서 여러 동시 인덱서가 가능 합니다. 

1. [Azure Portal](https://portal.azure.com)의 검색 서비스 대시보드 **개요** 페이지에서 **가격 책정 계층**을 확인하여 병렬 인덱싱을 수용할 수 있는지 확인합니다. 기본 및 표준 계층 모두 여러 복제본이 제공됩니다.

2. **설정** > **규모**에서 병렬 처리를 위해 인덱서 워크로드마다 복제본을 하나씩 추가하여 [복제본을 증가](search-capacity-planning.md)시킵니다. 기존 쿼리 볼륨에 대해 충분한 수를 유지합니다. 인덱싱을 위해 쿼리 워크로드를 희생하는 것은 바람직한 절충안이 아닙니다.

3. Azure Cognitive Search 인덱서가 도달할 수 있는 수준에서 여러 컨테이너에 데이터를 배포 합니다. 여기에는 Azure SQL Database의 여러 테이블, Azure Blob Storage의 여러 컨테이너 또는 여러 컬렉션이 해당될 수 있습니다. 각 테이블 또는 컨테이너에 대해 하나의 데이터 원본 개체를 정의합니다.

4. 병렬로 실행할 여러 인덱서 생성 및 예약:

   + 서비스에 복제본이 여섯 개 있다고 가정합니다. 전체 데이터 집합의 6방향 분할을 위해 데이터 집합의 1/6을 포함하는 데이터 원본에 각각 매핑된 인덱서 여섯 개를 구성합니다. 

   + 각 인덱서에서 동일한 인덱스를 가리킵니다. Cognitive Search 워크로드의 경우 각 인덱서가 동일한 기술 집합을 가리키도록 합니다.

   + 각 인덱서 정의 내에 동일한 런타임 실행 패턴을 예약합니다. 예를 들어, `"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }`는 2018-05-15에 모든 인덱서에 대해 8시간 간격으로 실행되는 일정을 만듭니다.

예약된 시간에 모든 인덱서는 실행, 데이터 로드, 보강(Cognitive Search 파이프라인을 구성한 경우) 적용, 인덱스에 쓰기 작업을 시작합니다. Azure Cognitive Search는 업데이트에 대 한 인덱스를 잠그지 않습니다. 동시 쓰기가 관리되며 첫 번째 시도에서 특정 쓰기가 성공하지 못하면 다시 시도됩니다.

> [!Note]
> 복제본을 늘릴 때 인덱스 크기가 상당히 증가할 것으로 예상되면 파티션 수를 늘리는 것이 좋습니다. 파티션은 인덱싱된 콘텐츠 조각을 저장합니다. 파티션이 많을수록 각 파티션이 저장해야 하는 조각이 줄어듭니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Cognitive Search의 보안](search-security-overview.md)
