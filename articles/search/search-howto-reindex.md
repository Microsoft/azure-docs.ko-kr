---
title: 검색 인덱스 다시 작성
titleSuffix: Azure Cognitive Search
description: 새 요소를 추가 하거나, 기존 요소나 문서를 업데이트 하거나, 전체 다시 빌드 또는 부분 인덱싱에서 사용 되지 않는 문서를 삭제 하 여 Azure Cognitive Search 인덱스를 새로 고칩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 10c7d4146d61f5b589b29bc8faad5fa8e60a293a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924030"
---
# <a name="how-to-rebuild-an-index-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱스를 다시 작성 하는 방법

이 문서에서는 Azure Cognitive Search 인덱스를 다시 작성 하는 방법, 다시 작성 해야 하는 상황 및 진행 중인 쿼리 요청에 대 한 재작성의 영향을 완화 하기 위한 권장 사항을 설명 합니다.

*다시 작성*은 모든 필드 기반의 반전된 인덱스를 포함하여 인덱스와 연결된 실제 데이터 구조를 삭제했다가 다시 만드는 것을 의미합니다. Azure Cognitive Search에서는 개별 필드를 삭제 하 고 다시 만들 수 없습니다. 인덱스를 다시 작성하려면 모든 필드 스토리지를 삭제하고, 기존 또는 수정된 인덱스 스키마를 기준으로 다시 만든 후 인덱스에 푸시되었거나 외부 원본에서 가져온 데이터로 다시 채워야 합니다. 

인덱스 디자인을 반복할 때 개발 중에 인덱스를 다시 작성 하는 것이 일반적 이지만 복잡 한 형식을 추가 하거나 확인 기에 필드를 추가 하는 등 구조적 변경을 수용 하기 위해 프로덕션 수준 인덱스를 다시 작성 해야 할 수도 있습니다.

## <a name="rebuild-versus-refresh"></a>"다시 작성" 및 "새로 고침"

새로 작성, 수정 또는 삭제 된 문서를 사용 하 여 인덱스 내용을 새로 고치는 것과 혼동 해서는 안 됩니다. 검색 모음를 새로 고치는 것은 거의 모든 검색 앱에서 제공 되는 경우가 거의 있습니다. 예를 들어 검색 모음가 온라인 판매 앱에서 재고 변경 내용을 반영 해야 하는 경우와 같이 최신 업데이트가 필요 합니다.

인덱스의 구조를 변경 하지 않는 한 인덱스를 처음 로드 하는 데 사용한 것과 동일한 기술을 사용 하 여 인덱스를 새로 고칠 수 있습니다.

* 밀어넣기 모드 인덱싱의 경우 [추가, 업데이트 또는 삭제 문서](/rest/api/searchservice/addupdate-or-delete-documents) 를 호출 하 여 변경 내용을 인덱스에 푸시합니다.

* 인덱서의 경우 [인덱서 실행을 예약](search-howto-schedule-indexers.md) 하 고 변경 내용 추적 또는 타임 스탬프를 사용 하 여 델타를 식별할 수 있습니다. 업데이트를 스케줄러에서 관리할 수 있는 것 보다 빠르게 반영 해야 하는 경우에는 푸시 모드 인덱싱을 대신 사용할 수 있습니다.

## <a name="rebuild-conditions"></a>다시 작성 조건

다음 조건 중 하나라도 충족 되 면 인덱스를 삭제 하 고 다시 만듭니다. 

| 조건 | Description |
|-----------|-------------|
| 필드 정의 변경 | 필드 이름, 데이터 형식 또는 특정 [인덱싱 특성](/rest/api/searchservice/create-index)(검색 가능, 필터링 가능, 정렬 가능, 패싯 가능)을 수정하려면 전체적으로 다시 작성해야 합니다. |
| 필드에 분석기 할당 | [분석기](search-analyzers.md)는 인덱스에 정의된 후 필드에 할당됩니다. 언제든지 새 분석기 정의를 인덱스에 추가할 수 있지만, 분석기 *할당*은 필드를 만들 때만 가능합니다. **분석기**와 **indexAnalyzer** 둘 다 그렇습니다. **searchAnalyzer** 속성은 예외입니다(이 속성을 기존 필드에 할당 가능). |
| 인덱스의 분석기 정의 업데이트 또는 삭제 | 전체 인덱스를 다시 작성하지 않는 한 인덱스의 기존 분석기 구성(분석기, 토크나이저, 토큰 필터 또는 char 필터)을 삭제 또는 변경할 수 없습니다. |
| 제안기에 필드 추가 | 필드가 이미 있고 필드에 [Suggesters](index-add-suggesters.md) 구문을 추가하려면 인덱스를 다시 작성해야 합니다. |
| 필드 삭제 | 필드의 모든 추적을 실제로 제거하려면 인덱스를 다시 작성해야 합니다. 즉시 다시 빌드를 수행할 수 없는 경우 "deleted" 필드에 대 한 액세스를 사용 하지 않도록 응용 프로그램 코드를 수정 하거나 [$select 쿼리 매개 변수](search-query-odata-select.md) 를 사용 하 여 결과 집합에 표시할 필드를 선택할 수 있습니다. 실제로 필드 정의와 콘텐츠는 다음 번에 의심스러운 필드를 생략하는 스키마를 적용할 때 다시 작성이 수행될 때까지 인덱스에 남아 있습니다. |
| 계층 전환 | 용량이 더 필요 하면 Azure Portal에 전체 업그레이드가 없습니다. 새 서비스에서 새 서비스를 만들고 인덱스를 처음부터 새로 빌드해야 합니다. 이 프로세스를 자동화 하는 데 도움이 되도록이 [Azure Cognitive Search .net 샘플 리포지토리의](https://github.com/Azure-Samples/azure-search-dotnet-samples) **인덱스 백업 복원** 샘플 코드를 사용할 수 있습니다. 이 앱은 일련의 JSON 파일에 인덱스를 백업한 다음 지정 하는 검색 서비스에서 인덱스를 다시 만듭니다.|

## <a name="update-conditions"></a>업데이트 조건

기존 물리적 구조에 영향을 주지 않고 많은 다른 수정 작업을 수행할 수 있습니다. 특히 다음 변경 내용에는 인덱스를 다시 작성할 필요가 *없습니다* . 이러한 변경을 위해 [인덱스 정의](/rest/api/searchservice/update-index) 를 변경 내용으로 업데이트할 수 있습니다.

+ 새 필드 추가
+ 기존 필드의 **retrievable** 특성 설정
+ 기존 필드의 **searchAnalyzer** 설정
+ 인덱스에 새 분석기 정의 추가
+ 점수 매기기 프로필 추가, 업데이트 또는 삭제
+ CORS 설정 추가, 업데이트 또는 삭제
+ synonymMaps 추가, 업데이트 또는 삭제

새 필드를 추가할 때 기존의 인덱싱된 문서에는 새 필드에 null 값이 제공됩니다. 이후에 데이터를 새로 고칠 때 외부 원본 데이터의 값이 Azure Cognitive Search에 의해 추가 된 null을 대체 합니다. 인덱스 콘텐츠 업데이트에 대한 자세한 내용은 [문서 추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents)를 참조하세요.

## <a name="how-to-rebuild-an-index"></a>인덱스를 다시 작성하는 방법

개발 하는 동안 인덱스 스키마는 자주 변경 됩니다. 작은 대표 데이터 집합으로 신속 하 게 삭제, 다시 작성 및 다시 로드 될 수 있는 인덱스를 만들어 계획할 수 있습니다.

이미 프로덕션 환경에서 사용되는 애플리케이션의 경우 기존 인덱스와 나란히 실행되는 새 인덱스를 만들어 쿼리 가동 중지 시간을 피하는 것이 좋습니다. 응용 프로그램 코드는 새 인덱스에 대 한 리디렉션을 제공 합니다.

인덱싱은 백그라운드에서 실행 되지 않으며 서비스는 진행 중인 쿼리에 대 한 추가 인덱싱의 균형을 유지 합니다. 인덱싱 중에 포털에서 [쿼리 요청을 모니터링](search-monitor-queries.md) 하 여 쿼리가 적시에 완료 되도록 할 수 있습니다.

1. 다시 빌드가 필요한 지 여부를 확인 합니다. 필드를 추가 하거나 필드와 관련이 없는 인덱스의 일부를 변경 하는 경우 삭제, 다시 만들기 및 완전히 다시 로드 하지 않고도 [정의를 업데이트할](/rest/api/searchservice/update-index) 수 있습니다.

1. 나중에 참조할 수 있도록 필요한 경우 [인덱스 정의를 가져옵니다](/rest/api/searchservice/get-index) .

1. 새 인덱스와 이전 인덱스를 나란히 실행 하지 않는다고 가정 하 고 [기존 인덱스를 삭제](/rest/api/searchservice/delete-index)합니다. 

   해당 인덱스를 대상으로 하는 모든 쿼리는 즉시 삭제됩니다. 인덱스 삭제는 취소할 수 없으므로 필드 컬렉션 및 기타 구문에 대 한 물리적 저장소를 삭제 합니다. 삭제 하기 전에 영향을 고려 하기 위해 일시 중지 합니다. 

1. 요청 본문에 변경 되거나 수정 된 필드 정의가 포함 된 [수정 된 인덱스를 만듭니다](/rest/api/searchservice/create-index).

1. 외부 원본의 [문서를 사용하는 인덱스를 로드](/rest/api/searchservice/addupdate-or-delete-documents)합니다.

인덱스를 만들면 실제 스토리지가 인덱스 스키마의 각 필드에 할당되고 검색 가능한 각 필드에 대해 반전된 인덱스가 생성됩니다. 검색할 수 없는 필드는 필터 또는 식에 사용할 수 있지만, 반전된 인덱스를 갖고 있지 않으며 전체 텍스트 또는 유사 항목 검색이 불가능합니다. 인덱스 다시 작성 시, 사용자가 제공하는 인덱스 스키마에 따라 이러한 반전된 인덱스가 삭제된 후 다시 만들어집니다.

인덱스를 로드할 때 각 필드의 반전된 인덱스가 각 문서의 고유한 토큰화된 모든 단어와 해당 문서 ID에 대한 맵으로 채워집니다. 예를 들어, 호텔 데이터 세트를 인덱싱할 때 City 필드용으로 만든 반전된 인덱스는 Seattle, Portland 등의 용어를 포함할 수 있습니다. City 필드에 Seattle 또는 Portland가 포함된 문서는 해당 용어와 함께 문서 ID가 표시됩니다. [추가, 업데이트 또는 삭제](/rest/api/searchservice/addupdate-or-delete-documents) 작업에서 용어 및 문서 ID 목록이 이에 따라 업데이트됩니다.

> [!NOTE]
> SLA 요구 사항이 엄격한 경우, 특별히 이 작업용으로 새 서비스를 프로비전하고 프로덕션 인덱스와는 완전히 고립된 상태로 개발 및 인덱싱을 진행하는 것이 바람직할 수 있습니다. 별도 서비스를 자체 하드웨어에서 실행하여 리소스 경합 가능성을 모두 제거합니다. 개발이 완료 되 면 새 인덱스를 그대로 두거나 새 끝점 및 인덱스로 쿼리를 리디렉션하고 완성 된 코드를 실행 하 여 원래 Azure Cognitive Search 서비스에 수정 된 인덱스를 게시할 수 있습니다. 현재는 사용 준비가 완료된 인덱스를 다른 서비스로 이동하기 위한 메커니즘이 없습니다.

## <a name="check-for-updates"></a>업데이트 확인

첫 번째 문서를 로드하는 즉시 인덱스 쿼리를 시작할 수 있습니다. 문서 ID를 알고 있는 경우 [문서 조회 REST API](/rest/api/searchservice/lookup-document)가 특정 문서를 반환합니다. 보다 광범위한 테스트를 위해서는 인덱스가 완전히 로드될 때까지 기다린 다음, 쿼리를 사용하여 보려는 컨텍스트를 확인합니다.

[검색 탐색기](search-explorer.md) 또는 [postman](search-get-started-postman.md) 과 같은 웹 테스트 도구를 사용 하 여 업데이트 된 콘텐츠를 확인할 수 있습니다.

필드를 추가 하거나 이름을 바꾼 경우 [$select](search-query-odata-select.md) 를 사용 하 여 해당 필드를 반환 합니다. `search=*&$select=document-id,my-new-field,some-old-field&$count=true`

## <a name="see-also"></a>참조

+ [인덱서 개요](search-indexer-overview.md)
+ [대규모 데이터 집합 인덱싱](search-howto-large-index.md)
+ [포털의 인덱싱](search-import-data-portal.md)
+ [Azure SQL Database 인덱서](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)
+ [Azure Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)
+ [Azure Table Storage 인덱서](search-howto-indexing-azure-tables.md)
+ [Azure Cognitive Search의 보안](search-security-overview.md)