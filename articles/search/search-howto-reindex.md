---
title: Azure Search 인덱스 다시 빌드 또는 검색 가능한 콘텐츠 새로 고침 - Azure Search
description: 전체 다시 빌드 또는 부분 증분 인덱싱에서 새 요소를 추가하거나 기존 요소 또는 문서를 업데이트하거나 오래된 문서를 삭제하여 Azure Search 인덱스를 새로 고칩니다.
services: search
author: HeidiSteen
manager: cgronlun
ms.service: search
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 2595912732389c8a415d1854a84a7b9c182e4dc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871151"
---
# <a name="how-to-rebuild-an-azure-search-index"></a>Azure Search 인덱스를 다시 빌드하는 방법

이 문서에서는 Azure Search 인덱스는 다시 작성하는 방법, 다시 작성이 필요한 상황, 지속적인 쿼리 요청에 다시 작성이 미치는 영향을 완화하기 위한 권장 사항을 설명합니다.

*다시 작성*은 모든 필드 기반의 반전된 인덱스를 포함하여 인덱스와 연결된 실제 데이터 구조를 삭제했다가 다시 만드는 것을 의미합니다. Azure Search에서는 개별 필드를 삭제했다가 다시 만들 수 없습니다. 인덱스를 다시 작성하려면 모든 필드 스토리지를 삭제하고, 기존 또는 수정된 인덱스 스키마를 기준으로 다시 만든 후 인덱스에 푸시되었거나 외부 원본에서 가져온 데이터로 다시 채워야 합니다. 개발 중에 인덱스를 다시 작성하는 것이 일반적이지만, 복합 형식을 추가하거나 제안기에 필드를 추가하는 경우처럼 구조적 변경을 수용하기 위해 프로덕션 수준의 인덱스를 다시 작성해야 할 수도 있습니다.

인덱스를 오프라인으로 전환하는 다시 작성과 달리, *데이터 새로 고침*은 백그라운드 작업으로 실행됩니다. 일반적으로 쿼리를 완료하는 데 더 오래 걸리지만, 쿼리 워크로드 중단을 최소화하면서 문서를 추가하고 제거하고 교체할 수 있습니다. 인덱스 콘텐츠 업데이트에 대한 자세한 내용은 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

## <a name="rebuild-conditions"></a>다시 작성 조건

| 조건 | 설명 |
|-----------|-------------|
| 필드 정의 변경 | 필드 이름, 데이터 형식 또는 특정 [인덱싱 특성](https://docs.microsoft.com/rest/api/searchservice/create-index)(검색 가능, 필터링 가능, 정렬 가능, 패싯 가능)을 수정하려면 전체적으로 다시 작성해야 합니다. |
| 필드에 분석기 할당 | [분석기](search-analyzers.md)는 인덱스에 정의된 후 필드에 할당됩니다. 언제든지 새 분석기 정의를 인덱스에 추가할 수 있지만, 분석기 *할당*은 필드를 만들 때만 가능합니다. **분석기**와 **indexAnalyzer** 둘 다 그렇습니다. **searchAnalyzer** 속성은 예외입니다(이 속성을 기존 필드에 할당 가능). |
| 인덱스의 분석기 정의 업데이트 또는 삭제 | 전체 인덱스를 다시 작성하지 않는 한 인덱스의 기존 분석기 구성(분석기, 토크나이저, 토큰 필터 또는 char 필터)을 삭제 또는 변경할 수 없습니다. |
| 제안기에 필드 추가 | 필드가 이미 있고 필드에 [Suggesters](index-add-suggesters.md) 구문을 추가하려면 인덱스를 다시 작성해야 합니다. |
| 필드 삭제 | 필드의 모든 추적을 실제로 제거하려면 인덱스를 다시 작성해야 합니다. 즉시 다시 작성이 가능하지 않은 경우 "삭제된" 필드에 액세스하지 못하도록 애플리케이션 코드를 수정하면 됩니다. 실제로 필드 정의와 콘텐츠는 다음 번에 의심스러운 필드를 생략하는 스키마를 적용할 때 다시 작성이 수행될 때까지 인덱스에 남아 있습니다. |
| 계층 전환 | 용량이 더 필요한 경우 현재 위치 업그레이드가 수행되지 않습니다. 새 용량 지점에서 새 서비스가 만들어지며, 새 서비스에서는 인덱스를 처음부터 새로 작성해야 합니다. |

기존 실제 구조를 영향을 주지 않으면서 다른 부분을 수정할 수 있습니다. 특히, 다음 변경은 인덱스를 다시 작성할 필요가 *없습니다*.

+ 새 필드 추가
+ 기존 필드의 **retrievable** 특성 설정
+ 기존 필드의 **searchAnalyzer** 설정
+ 인덱스에 새 분석기 정의 추가
+ 점수 매기기 프로필 추가, 업데이트 또는 삭제
+ CORS 설정 추가, 업데이트 또는 삭제
+ synonymMaps 추가, 업데이트 또는 삭제

새 필드를 추가할 때 기존의 인덱싱된 문서에는 새 필드에 null 값이 제공됩니다. 나중에 데이터를 새로 고치면 외부 원본 데이터의 값이 Azure Search에 의해 추가된 null을 대체합니다. 인덱스 콘텐츠 업데이트에 대한 자세한 내용은 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

## <a name="partial-or-incremental-indexing"></a>부분 또는 증분 인덱싱

Azure Search에서는 필드를 기준으로 하는 인덱싱을 제어하여 특정 필드를 삭제할지 또는 다시 만들지를 선택할 수 없습니다. 마찬가지로, [조건에 따른 문서 인덱싱](https://stackoverflow.com/questions/40539019/azure-search-what-is-the-best-way-to-update-a-batch-of-documents)을 위한 기본 제공 메커니즘이 없습니다. 조건 기반 인덱싱에 대한 요구 사항은 사용자 지정 코드를 통해 충족해야 합니다.

그러나 쉽게 수행할 수 있는 작업은 인덱스에서 *문서를 새로 고치는 것*입니다. 많은 검색 솔루션에서 외부 원본 데이터는 일시적이며 원본 데이터와 검색 인덱스 간의 동기화를 수행하는 것이 일반적입니다. 코드에서 [문서 추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 작업 또는 [해당 .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexesoperationsextensions.createorupdate?view=azure-dotnet)을 호출하여 인덱스 콘텐츠를 업데이트하거나 새 필드의 값을 추가합니다.

## <a name="partial-indexing-with-indexers"></a>인덱서를 사용하는 부분 인덱싱

[인덱서](search-indexer-overview.md)는 데이터 새로 고침 작업을 간소화합니다. 인덱서는 외부 데이터 원본에서 하나의 테이블 또는 뷰만 인덱싱할 수 있습니다. 여러 테이블을 인덱싱하려는 경우 가장 간단한 방법은 테이블을 조인하고 인덱싱하려는 열을 프로젝션하는 뷰를 만드는 것입니다. 

외부 데이터 원본을 탐색하는 인덱서를 사용하는 경우 원본 데이터에서 "상위 워터마크" 열을 확인합니다. 이 열이 있는 경우 새 콘텐츠 또는 수정된 콘텐츠가 포함된 행만 선택하여 증분 변경 내용 검색에 사용할 수 있습니다. [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection)의 경우 `lastModified` 필드가 사용됩니다. [Azure Table Storage](search-howto-indexing-azure-tables.md#incremental-indexing-and-deletion-detection)에서 `timestamp`는 동일한 용도로 사용됩니다. 마찬가지로 [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows)와 [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md#indexing-changed-documents) 모두에는 행 업데이트 플래그를 지정하는 필드가 있습니다. 

인덱서에 대한 자세한 내용은 [인덱서 개요](search-indexer-overview.md) 및 [인덱서 재설정 REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)를 참조하세요.

## <a name="how-to-rebuild-an-index"></a>인덱스를 다시 작성하는 방법

인덱스 스키마가 유동적인 상태인 경우 개발하는 동안 빈번한 전체 다시 빌드를 계획합니다. 이미 프로덕션 환경에서 사용되는 애플리케이션의 경우 기존 인덱스와 나란히 실행되는 새 인덱스를 만들어 쿼리 가동 중지 시간을 피하는 것이 좋습니다.

인덱스 업데이트에는 서비스 수준의 읽기-쓰기 권한이 필요합니다. 

포털에서 인덱스를 다시 작성할 수 없습니다. 프로그래밍 방식으로 전체 다시 작성을 위한 [인덱스 업데이트 REST API](https://docs.microsoft.com/rest/api/searchservice/update-index) 또는 [동등한 .NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexesoperations.createorupdatewithhttpmessagesasync?view=azure-dotnet)를 호출할 수 있습니다. 인덱스 업데이트 요청은 [인덱스 만들기 REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)와 동일하지만 컨텍스트는 다릅니다.

다음 워크플로는 REST API 중심이지만, .NET SDK에도 똑같이 적용됩니다.

1. 인덱스 이름을 다시 사용할 때 [기존 인덱스를 삭제](https://docs.microsoft.com/rest/api/searchservice/delete-index)해야 합니다. 

   해당 인덱스를 대상으로 하는 모든 쿼리는 즉시 삭제됩니다. 인덱스 삭제는 되돌릴 수 없으며, 필드 컬렉션 및 기타 구문의 실제 스토리지가 제거됩니다. 따라서 인덱스를 삭제하기 전에 삭제에 따른 결과를 명확히 인식해야 합니다. 

2. 서비스 엔드포인트, API 키 및 [관리자 키](https://docs.microsoft.com/azure/search/search-security-api-keys)를 사용하여 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 요청을 작성합니다. 관리자 키는 쓰기 작업에 필요합니다.

3. 요청 본문에서, 변경된 또는 수정된 필드 정의를 인덱스 스키마에 제공합니다. 요청 본문은 인덱스 스키마와 프로필, 분석기, 제안기 및 CORS 옵션에 점수를 매기기 위한 구문을 포함합니다. 스키마 요구 사항은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)에 설명되어 있습니다.

4. Azure Search의 물리적 인덱스 식을 다시 작성하는 [인덱스 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-index) 요청을 보냅니다. 

5. 외부 원본의 [문서를 사용하는 인덱스를 로드](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)합니다.

인덱스를 만들면 실제 스토리지가 인덱스 스키마의 각 필드에 할당되고 검색 가능한 각 필드에 대해 반전된 인덱스가 생성됩니다. 검색할 수 없는 필드는 필터 또는 식에 사용할 수 있지만, 반전된 인덱스를 갖고 있지 않으며 전체 텍스트 또는 유사 항목 검색이 불가능합니다. 인덱스 다시 작성 시, 사용자가 제공하는 인덱스 스키마에 따라 이러한 반전된 인덱스가 삭제된 후 다시 만들어집니다.

인덱스를 로드할 때 각 필드의 반전된 인덱스가 각 문서의 고유한 토큰화된 모든 단어와 해당 문서 ID에 대한 맵으로 채워집니다. 예를 들어, 호텔 데이터 세트를 인덱싱할 때 City 필드용으로 만든 반전된 인덱스는 Seattle, Portland 등의 용어를 포함할 수 있습니다. City 필드에 Seattle 또는 Portland가 포함된 문서는 해당 용어와 함께 문서 ID가 표시됩니다. [추가, 업데이트 또는 삭제](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents) 작업에서 용어 및 문서 ID 목록이 이에 따라 업데이트됩니다.

> [!NOTE]
> SLA 요구 사항이 엄격한 경우, 특별히 이 작업용으로 새 서비스를 프로비전하고 프로덕션 인덱스와는 완전히 고립된 상태로 개발 및 인덱싱을 진행하는 것이 바람직할 수 있습니다. 별도 서비스를 자체 하드웨어에서 실행하여 리소스 경합 가능성을 모두 제거합니다. 개발이 완료되면 새 인덱스를 원래 위치에 두고, 쿼리를 새 엔드포인트 및 인덱스로 리디렉션하거나, 완료된 코드를 실행하여 원래 Azure Search 서비스에서 수정된 인덱스를 게시할 수 있습니다. 현재는 사용 준비가 완료된 인덱스를 다른 서비스로 이동하기 위한 메커니즘이 없습니다.

## <a name="view-updates"></a>업데이트 보기

첫 번째 문서를 로드하는 즉시 인덱스 쿼리를 시작할 수 있습니다. 문서 ID를 알고 있는 경우 [문서 조회 REST API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)가 특정 문서를 반환합니다. 보다 광범위한 테스트를 위해서는 인덱스가 완전히 로드될 때까지 기다린 다음, 쿼리를 사용하여 보려는 컨텍스트를 확인합니다.

## <a name="see-also"></a>참고 항목

+ [인덱서 개요](search-indexer-overview.md)
+ [대규모 데이터 집합 인덱싱](search-howto-large-index.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Search의 보안](search-security-overview.md)