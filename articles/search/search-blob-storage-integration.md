---
title: Azure Blob Storage에 전체 텍스트 검색 추가
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색 인덱스를 빌드할 때 콘텐츠를 추출하고 Azure Blob에 구조를 추가합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72d00b70cf3568466715668aa441ee295614c740
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935248"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Azure Cognitive Search를 사용하여 Azure Blob 데이터에 전체 텍스트 검색 추가

Azure Blob Storage에 저장된 다양한 콘텐츠 형식에서 검색하면 어려운 문제를 해결할 수 있습니다. 그러나 [Azure Cognitive Search](search-what-is-azure-search.md)를 사용하면 단 몇 번의 클릭으로 Blob의 콘텐츠에 인덱스를 만들고 검색할 수 있습니다. Azure Cognitive Search에는 인덱싱에 데이터 원본 인식 기능을 추가하는 [‘Blob 인덱서’](search-howto-indexing-azure-blob-storage.md)를 통한 Blob Storage의 인덱싱을 위한 기본 제공 통합이 있습니다.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Blob 데이터에 대한 전체 텍스트 검색 추가의 의미

Azure Cognitive Search는 클라우드 검색 서비스이며 사용자의 검색 서비스에서 호스트되는 사용자 정의 인덱스를 통해 작동하는 인덱싱 및 쿼리 엔진을 제공합니다. 사용자가 검색 쿼리에서 기대하는 속도로 결과를 반환하는 성능을 위해서는 검색 가능한 콘텐츠를 클라우드의 쿼리 엔진과 함께 공동 배치해야 합니다.

Azure Cognitive Search는 인덱싱 계층에서 Azure Blob Storage와 통합하여 ‘반전된 인덱스’에 인덱싱된 검색 문서와 자유 형식 텍스트 쿼리 및 필터 식을 지원하는 기타 쿼리 구조체로 Blob 콘텐츠를 가져옵니다. Blob 콘텐츠는 검색 인덱스로 인덱싱되므로 Blob 콘텐츠에 대한 액세스는 Azure Cognitive Search의 전체 쿼리 기능을 활용할 수 있습니다.

인덱스를 만들고 채운 후에는 Blob 컨테이너와 독립적으로 존재하지만 인덱싱 작업을 다시 실행하여 기본 컨테이너에 대한 변경 내용으로 인덱스를 새로 고칠 수 있습니다. 개별 Blob에 대한 타임스탬프 정보는 변경 검색에 사용됩니다. 예약된 실행 또는 요청 시 인덱싱을 새로 고침 메커니즘으로 선택할 수 있습니다.

입력은 Azure Blob Storage의 단일 컨테이너에 있는 Blob입니다. Blob은 거의 모든 종류의 텍스트 데이터가 될 수 있습니다. Blob에 이미지가 포함되어 있는 경우 [Blob 인덱싱에 대한 AI 보강](search-blob-ai-integration.md)을 추가하여 이미지에서 텍스트를 만들고 추출할 수 있습니다.

출력은 항상 빠른 텍스트 검색, 검색 및 클라이언트 애플리케이션 탐색에 사용되는 Azure Cognitive Search 인덱스입니다. 입력과 출력 사이에는 인덱싱 파이프라인 아키텍처 자체가 있습니다. 파이프라인은 이 문서에서 자세히 설명할 ‘인덱서’ 기능을 기반으로 합니다.

## <a name="start-with-services"></a>서비스 시작

Azure Cognitive Search 및 Azure Blob Storage가 필요합니다. Blob Storage 내에서 원본 콘텐츠를 제공하는 컨테이너가 필요합니다.

스토리지 계정 포털 페이지에서 직접 시작할 수 있습니다. 왼쪽 탐색 페이지의 **Blob service**에서 **Azure Cognitive Search 추가**를 클릭하여 새 서비스를 만들거나 기존 서비스를 선택합니다. 

Azure Cognitive Search를 스토리지 계정에 추가한 후에는 표준 프로세스에 따라 Blob 데이터를 인덱싱할 수 있습니다. 편리한 초기 도입을 위해 Azure Cognitive Search에서 **데이터 가져오기** 마법사를 사용하거나 Postman과 같은 도구를 사용하여 REST API를 호출하는 것이 좋습니다. Postman에서 REST API를 호출하는 단계는 [Azure Cognitive Search에서 반정형 데이터(JSON Blob) 인덱싱 및 검색](search-semi-structured-data.md) 자습서에 안내되어 있습니다. 

## <a name="use-a-blob-indexer"></a>Blob 인덱서 사용

‘인덱서’는 데이터를 샘플링하고, 메타데이터를 읽고, 데이터를 검색하며, 후속 가져오기를 위해 네이티브 형식의 데이터를 JSON 문서로 직렬화하기 위한 내부 논리를 포함하는 데이터 원본 인식 하위 서비스입니다. 

Azure Storage의 Blob은 [Azure Cognitive Search Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)를 사용하여 인덱싱됩니다. **데이터 가져오기** 마법사, REST API 또는 .NET SDK를 사용하여 이 인덱서를 호출할 수 있습니다. 코드에서는 형식을 설정하고 Blob 컨테이너와 함께 Azure Storage 계정을 포함하는 연결 정보를 제공하여 이 인덱서를 사용합니다. 만든 후에 매개 변수로 전달할 수 있는 가상 디렉터리를 만들거나 파일 형식 확장에서 필터링하여 Blob의 하위 집합을 지정할 수 있습니다.

인덱서는 “문서 크래킹”을 수행하여 콘텐츠를 검사하는 Blob을 엽니다. 이 작업은 데이터 원본에 연결한 후 파이프라인에서 첫 번째 단계입니다. Blob 데이터의 경우에는 PDF, Office 문서 및 기타 콘텐츠 형식이 검색되는 단계입니다. 텍스트 추출을 사용한 문서 크래킹은 무료로 제공됩니다. Blob에 이미지 콘텐츠가 포함되어 있는 경우 [AI 보강 추가](search-blob-ai-integration.md)를 하지 않으면 이미지가 무시됩니다. 표준 인덱싱은 텍스트 콘텐츠에만 적용됩니다.

Blob 인덱서는 구성 매개 변수와 함께 제공되며 기본 데이터가 충분한 정보를 제공하는 경우 변경 내용 추적을 지원합니다. [Azure Cognitive Search Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)의 핵심 기능에 대해 자세히 알아볼 수 있습니다.

### <a name="supported-content-types"></a>지원하는 콘텐츠 유형

컨테이너에 대해 Blob 인덱서를 실행하면 단일 쿼리를 사용하여 다음과 같은 콘텐츠 형식에서 텍스트 및 메타데이터를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Blob 메타데이터 인덱싱

모든 콘텐츠 형식의 Blob을 손쉽게 정렬할 수 있는 일반적인 시나리오는 각 Blob에 대해 사용자 지정 메타데이터와 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 검색 서비스의 인덱스에 저장된 문서 형식과 관계없이 모든 Blob을 인덱싱할 수 있습니다. 그런 다음, 새 인덱스를 사용하여 모든 Blob Storage 콘텐츠에서 정렬, 필터링, 패싯을 수행할 수 있습니다.

> [!NOTE]
> Blob 인덱스 태그는 Blob Storage 서비스에 의해 네이티브로 인덱싱되고 쿼리를 위해 노출됩니다. Blob의 키/값 특성에 인덱싱 및 필터링 기능이 필요한 경우 메타데이터 대신 Blob 인덱스 태그를 활용해야 합니다.
>
> Blob 인덱스에 대해 자세히 알아보려면 [Blob 인덱스를 사용하여 Azure Blob Storage에서 데이터 관리 및 찾기](../storage/blobs/storage-manage-find-blobs.md)를 참조하세요.

### <a name="indexing-json-blobs"></a>JSON Blob 인덱싱
JSON이 포함된 Blob에 있는 구조화된 콘텐츠를 추출하도록 인덱서를 구성할 수 있습니다. 인덱서는 JSON Blob을 읽고, 구조화된 콘텐츠를 검색 문서의 적절한 필드로 구문 분석합니다. 인덱서는 또한 JSON 개체의 배열을 포함하는 Blob을 처리하고 각 요소를 개별 검색 문서에 매핑할 수 있습니다. 구문 분석 모드를 설정하여 인덱서에서 만든 JSON 개체의 형식에 영향을 줄 수 있습니다.

## <a name="search-blob-content-in-a-search-index"></a>검색 인덱스에서 Blob 콘텐츠 검색 

인덱싱의 출력은 클라이언트 앱에서 자유 텍스트 및 필터링된 쿼리를 사용하여 대화형 탐색에 사용되는 검색 인덱스입니다. 콘텐츠를 처음 살펴보고 확인하려면 포털에서 [검색 탐색기](search-explorer.md)로 시작하여 문서 구조를 검토하는 것이 좋습니다. 검색 탐색기에서 [간단한 쿼리 구문](query-simple-syntax.md), [전체 쿼리 구문](query-lucene-syntax.md) 및 [필터 식 구문](query-odata-filter-orderby-syntax.md)을 사용할 수 있습니다.

더 영구적인 솔루션은 클라이언트 애플리케이션에서 쿼리 입력을 수집하고 검색 결과로 응답을 제공하는 것입니다. 검색 애플리케이션을 빌드하는 방법은 [Azure Cognitive Search에서 첫 번째 애플리케이션 만들기](tutorial-csharp-create-first-app.md) C# 자습서에 설명되어 있습니다.

## <a name="next-steps"></a>다음 단계

+ [Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열(Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Blob 인덱서 설정(Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md)