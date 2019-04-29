---
title: 전체 텍스트 검색을 위해 Azure Blob 인덱서에서 JSON Blob 인덱싱 - Azure Search
description: Azure Search Blob 인덱서를 사용하여 텍스트 콘텐츠에 대해 Azure JSON Blob을 크롤링합니다. 인덱서는 Azure Blob Storage와 같은 선택된 데이터 원본에 대해 데이터 수집을 자동화합니다.
ms.date: 04/11/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 6db86d3e5aba1a2e43e69e71df8cc516fb14581f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871663"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Azure Search Blob 인덱서를 사용 하 여 JSON blob을 인덱싱하는 방법
이 문서에서는 Azure Search blob을 구성 하는 방법을 보여 줍니다 [인덱서](search-indexer-overview.md) Azure Blob storage에서 JSON 문서에서 구조적된 콘텐츠를 추출 하 여 Azure Search에서 가능 합니다. 이 워크플로 Azure Search 인덱스를 만들고 JSON blob에서 추출 하는 기존 텍스트를 사용 하 여 로드 합니다. 

[포털](#json-indexer-portal), [REST API](#json-indexer-rest) 또는 [.NET SDK](#json-indexer-dotnet)를 사용하여 JSON 콘텐츠를 인덱싱할 수 있습니다. 모든 방법에 대 한 일반적인 JSON 문서는 Azure Storage 계정의 blob 컨테이너에 있는 경우 다른 비 Azure 플랫폼에서 JSON 문서를 푸시하는 방법에 대한 지침은 [Azure Search에서 데이터 가져오기](search-what-is-data-import.md)를 참조하세요.

Azure Blob storage의 JSON blob은 일반적으로 단일 JSON 문서 또는 JSON 엔터티의 컬렉션입니다. JSON 컬렉션에 대 한 blob 있을 수는 **배열** 잘 구성 된 JSON 요소입니다. Blob 줄 바꿈으로 구분 된 여러 개별 JSON 엔터티도 구성 될 수 있습니다. Azure search에서 blob 인덱서를 설정 하는 방법에 따라 이러한 생성, 구문 분석할 수는 **parsingMode** 요청에서 매개 변수입니다.

> [!IMPORTANT]
> `json` 및 `jsonArray` 구문 분석 모드는 일반적으로 사용할 수 있지만 `jsonLines` 구문 분석 모드 공개 미리 보기로 제공 되며 프로덕션 환경에서는 사용할 수 없습니다. 자세한 내용은 [REST api-version=2017-11-11-Preview](search-api-2017-11-11-preview.md)를 참조하세요. 

> [!NOTE]
> 인덱서 구성 권장 사항을 따르십시오 [-일대다 인덱싱](search-howto-index-one-to-many-blobs.md) 하나의 Azure blob에서 여러 검색 문서를 출력 합니다.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>포털 사용

JSON 문서를 인덱싱하는 가장 쉬운 방법은 [Azure Portal](https://portal.azure.com/)에서 마법사를 사용하는 것입니다. Azure Blob 컨테이너의 메타데이터를 구문 분석하여 [**데이터 가져오기**](search-import-data-portal.md) 마법사는 기본 인덱스를 만들고, 원본 필드를 대상 인덱스 필드에 매핑하고, 인덱스를 로드하는 과정을 단일 작업으로 진행할 수 있습니다. 원본 데이터의 크기 및 복잡성에 따라 몇 분 안에 운영 작업에 대한 전체 텍스트 검색 인덱스를 사용할 수 있습니다.

Azure Search와 동일한 지역에 Azure storage에 대 한 동일한 Azure 구독을 사용 하는 것이 좋습니다.

### <a name="1---prepare-source-data"></a>1 - 원본 데이터 준비

1. [Azure Portal에 로그인](https://portal.azure.com/)합니다.

1. [Blob 컨테이너 만들기](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) 데이터를 포함 합니다. 유효한 값 중 하나에 대 한 공용 액세스 수준은 설정할 수 있습니다.

저장소 계정 이름, 컨테이너 이름 및에서 데이터를 검색 하는 액세스 키를 사용 하도록 해야 합니다 **데이터 가져오기** 마법사.

### <a name="2---start-import-data-wizard"></a>2 - 데이터 가져오기 마법사 시작

Azure Search 서비스의 개요 페이지에서 [마법사를 시작 합니다](search-import-data-portal.md) 명령 모음 또는 클릭 하 여 **Azure Search 추가** 에 **Blob service** 섹션에 저장소 계정의 왼쪽 탐색 창입니다.

   ![포털의 데이터 가져오기 명령](./media/search-import-data-portal/import-data-cmd2.png "데이터 가져오기 마법사 시작")

### <a name="3---set-the-data-source"></a>3 - 데이터 원본 설정

**데이터 원본** 페이지에서 원본은 다음 사양을 가진 **Azure Blob 스토리지**여야 합니다.

+ **추출할 데이터**는 *콘텐츠 및 메타데이터*여야 합니다. 이 옵션을 선택하면 마법사가 인덱스 스키마를 유추하고 가져올 필드를 매핑할 수 있습니다.
   
+ **구문 분석 모드** 로 설정 해야 *JSON*를 *JSON 배열을* 하거나 *JSON 줄*합니다. 

  *JSON*은 각 Blob을 단일 검색 문서로 구분하여 검색 결과에서 개별 항목으로 표시합니다. 

  *JSON 배열* 독립적인 검색 문서를 독립 실행형으로 명시 하 여 각 요소 및 올바른 형식의 JSON 데이터가-올바른 형식의 JSON이 포함 된 blob 개체의 배열에 해당 하거나 개체의 배열이 속성이 있습니다. Blob이 복잡하고 *JSON 배열*을 선택하지 않을 경우 전체 Blob이 단일 문서로 수집됩니다.

  *JSON 줄* 이 새-선으로 구분 된 JSON 엔터티를 여러 blob 구성에 대 한 각 엔터티는 독립 실행형 독립적인 검색 문서도 명시를 원하는 위치 합니다. Blob은 복잡 하 고 선택 하지 않으면 하는 경우 *JSON 줄* 모드를 선택한 다음 전체 blob을 구문 분석을 단일 문서로 수집 됩니다.
   
+ **스토리지 컨테이너**는 스토리지 계정 및 컨테이너 또는 컨테이너로 확인되는 연결 문자열을 지정해야 합니다. Blob service 포털 페이지에서 연결 문자열을 가져올 수 있습니다.

   ![Blob 데이터 원본 정의](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 - 마법사에서 "인식 검색 추가" 페이지 건너뛰기

JSON 문서 가져오기를 위해 반드시 인식 기술을 추가할 필요는 없습니다. 인덱싱 파이프라인에 [Cognitive Services API 및 변환을 포함](cognitive-search-concept-intro.md)할 구체적인 요구가 있지 않다면 이 단계는 건너뛰는 것이 좋습니다.

단계를 건너뛰는, 먼저 다음 페이지로 이동 합니다.

   ![인식 검색에 대한 다음 페이지 단추](media/search-get-started-portal/next-button-add-cog-search.png)

해당 페이지를 건너뛸 수 있습니다 인덱스 사용자 지정 합니다.

   ![인식 기술 단계 건너뛰기](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 - 인덱스 특성 설정

**인덱스** 페이지에는 데이터 형식을 포함하는 필드 목록과 인덱스 특성을 설정하기 위한 여러 확인란이 표시됩니다. 마법사는 원본 데이터를 샘플링 하 여 메타 데이터에 따라 필드 목록을 생성할 수 있습니다. 

선택할 수 있습니다 대량-특성 특성 열의 맨 위에 있는 확인란을 클릭 하 여 합니다. 선택할 **조회 가능** 하 고 **검색 가능** 클라이언트 앱을 전체 텍스트 검색 처리에 따라 반환 되어야 하는 모든 필드에 대 한 합니다. 정수 전체 텍스트는 보면 또는 유사 항목 검색 가능 (숫자 정확 하 게 평가 및 필터에 유용한 경우가 많습니다.).

에 대 한 설명을 검토 [특성을 인덱싱](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) 하 고 [언어 분석기](https://docs.microsoft.com/rest/api/searchservice/language-support) 자세한 내용은 합니다. 

시간을 내서 선택 항목을 검토합니다. 마법사를 실행하면 실제 데이터 구조가 만들어지며, 모든 개체를 삭제했다가 다시 만들지 않으면 이러한 필드를 편집할 수 없습니다.

   ![Blob 인덱스 정의](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 인덱서 만들기

완전히 지정된 마법사는 검색 서비스에서 세 개의 고유한 개체를 만듭니다. 데이터 원본 개체 및 인덱스 개체는 Azure Search 서비스에 명명된 리소스로 저장됩니다. 마지막 단계는 인덱서 개체를 만듭니다. 인덱서 이름을 지정하면 인덱서가 동일한 마법사 시퀀스에서 만든 인덱스 및 데이터 원본 개체와는 별도로 예약하고 관리할 수 있는 독립 실행형 리소스가 될 수 있습니다.

인덱서에 익숙하지 않은 경우 *인덱서*를 검색 가능 콘텐츠의 외부 데이터 원본을 탐색하는 Azure Search의 리소스로 간주할 수 있습니다. **데이터 가져오기** 마법사를 실행하면 JSON 데이터 원본을 탐색하고, 검색 가능한 콘텐츠를 추출하고, Azure Search의 인덱스로 가져오는 인덱서가 제공됩니다.

   ![Blob 인덱서 정의](media/search-howto-index-json/import-wizard-json-indexer.png)

**확인**을 클릭하여 마법사를 실행하고 모든 개체를 만듭니다. 인덱싱이 즉시 시작됩니다.

포털 페이지에서 데이터 가져오기를 모니터링할 수 있습니다. 진행률 알림은 인덱싱 상태 및 업로드된 문서 수를 나타냅니다. 

인덱싱이 완료되면 [검색 탐색기](search-explorer.md)를 사용하여 인덱스를 쿼리할 수 있습니다.

> [!NOTE]
> 원하는 데이터가 표시 되지 않으면, 자세한 필드에 더 많은 특성을 설정 해야 합니다. 인덱스 및 인덱서만 생성 및 5 단계에서 인덱스 특성에 대 한 내용을 수정 다시 마법사를 통해 단계를 삭제 합니다. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API 사용

REST API를 사용 하 여 Azure Search에 있는 모든 인덱서에 공통를 세 부분으로 이루어진 워크플로 따르는 JSON blob을 인덱싱할: 데이터 원본 만들기, 인덱스 만들기, 인덱서를 만듭니다. Blob storage에서 데이터 추출 인덱서 만들기 요청을 제출할 때 발생 합니다. 이 요청이 완료 되 면 인덱스를 쿼리 가능 해야 합니다. 

검토할 수 있습니다 [REST 예제 코드](#rest-example) 모든 세 개체를 만드는 방법을 보여 주는이 섹션의 끝입니다. 이 섹션에 대 한 세부 정보 포함 [JSON 구문 분석 모드](#parsing-modes), [blob single](#parsing-single-blobs)를 [JSON 배열](#parsing-arrays), 및 [중첩 배열](#nested-json-arrays)합니다.

JSON 인덱싱 코드 기반에 대해 사용 하 여 [Postman](search-fiddler.md) 및 이러한 개체를 만드는 REST API:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [데이터 원본](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

작업 순서 만들기 및이 순서 대로 개체를 호출 하는 필요 합니다. 달리 포털 워크플로 코드 접근 방식에서는 사용할 수 있는 인덱스를 통해 JSON 문서 전송에 동의 합니다 **인덱서 만들기** 요청 합니다.

Azure Blob storage의 JSON blob은 일반적으로 단일 JSON 문서 또는 JSON "배열". Azure Search에서 Blob 인덱서는 요청에 대한 **parsingMode** 매개 변수를 설정하는 방법에 따라 생성을 구문 분석할 수 있습니다.

| JSON 문서 | parsingMode | 설명 | 가용성 |
|--------------|-------------|--------------|--------------|
| Blob 당 하나 | `json` | JSON Blob을 텍스트의 단일 청크로 구문 분석합니다. 각 JSON Blob은 단일 Azure Search 문서가 됩니다. | 둘 다에서 일반 공급 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API와 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Blob 당 여러 개 | `jsonArray` | Blob에서 JSON 배열을 구문 분석합니다. 여기서 배열의 각 요소는 별도의 Azure Search 문서가 됩니다.  | 둘 다에서 미리 보기로 제공 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API와 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Blob 당 여러 개 | `jsonLines` | 각 엔터티에 별도 Azure Search 문서 되는 줄 바꿈로 구분 된 여러 JSON 엔터티 ("배열")를 포함 하는 blob을 구문 분석 합니다. | 둘 다에서 미리 보기로 제공 [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API와 [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1-요청에 대 한 입력을 조합 합니다.

각 요청에 대 한 서비스 이름 및 저장소 계정 이름과 (POST 헤더)에서 Azure Search에 대 한 관리자 키 및 blob storage에 대 한 키를 제공 해야 합니다. 사용할 수 있습니다 [Postman](search-fiddler.md) Azure Search에 HTTP 요청을 보내도록 합니다.

요청에 붙여 넣을 수 있도록 다음 네 가지 값을 메모장에 복사 합니다.

+ Azure 검색 서비스 이름
+ Azure Search 관리자 키
+ Azure 저장소 계정 이름
+ Azure storage 계정 키

포털에서 이러한 값을 찾을 수 있습니다.

1. Azure search 포털 페이지의 개요 페이지에서 검색 서비스 URL을 복사 합니다.

2. 왼쪽된 탐색 창에서 클릭 **키** 다음 중 하나는 기본 또는 보조 키 (동일)을 복사 합니다.

3. 저장소 계정의 포털 페이지로 전환 합니다. 왼쪽된 탐색 창에서 아래 **설정을**, 클릭 **선택키가**합니다. 이 페이지에서는 계정 이름과 키를 제공 합니다. 저장소 계정 이름 및 키 중 하나를 메모장에 복사 합니다.

### <a name="2---create-a-data-source"></a>2-데이터 원본 만들기

이 단계는 인덱서에서 사용 하는 데이터 원본 연결 정보를 제공 합니다. 데이터 원본에 연결 정보를 유지 하는 Azure Search에서 명명 된 개체입니다. 데이터 원본 유형, `azureblob`, 인덱서에 의해 호출 되는 데이터 추출 동작을 결정 합니다. 

서비스 이름, 관리자 키, 저장소 계정에 대 한 유효한 값을 대체 하 고 키 자리 표시자 계정 키를 누릅니다.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3-대상 검색 인덱스 만들기 

인덱서는 인덱스 스키마과 쌍을 이룹니다. 포털이 아닌 API를 사용하는 경우 인덱서 작업에서 지정할 수 있도록 사전에 인덱스를 준비합니다.

인덱스는 Azure Search에 검색 가능한 콘텐츠를 저장합니다. 인덱스를 만들려면 문서의 필드, 특성 및 검색 환경을 형성하는 기타 항목을 지정하는 스키마를 제공합니다. 원본과 동일한 필드 이름 및 데이터 형식을 갖는 인덱스를 만드는 경우 인덱서는 사용자에게 명시적으로 필드를 매핑하도록 요구하지 않고, 원본 및 대상 필드를 일치시킵니다.

다음 예제는 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index) 요청을 보여 줍니다. 인덱스에는 blob에서 추출된 텍스트를 저장하기 위한 검색 가능한`content` 필드가 표시됩니다.   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4-구성 및 인덱서를 실행 합니다.

인덱스와 데이터 원본 및 인덱서는도 명명 된 개체를 만들고 Azure Search 서비스에서 다시 사용 합니다. 인덱서를 만드는 완전히 지정 된 요청을 다음과 같이 표시 될 수 있습니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

인덱서 구성이 요청의 본문입니다. 데이터 원본 및 Azure Search에 이미 있는 빈 대상 인덱스 필요 합니다. 

일정 및 매개 변수는 선택적입니다. 사용 하 여 인덱서가 즉시 실행을 생략 하면 `json` 구문 분석 모드입니다.

이 특정 인덱서 필드 매핑 포함 되지 않습니다. 인덱서 정의 내에서 생략할 수 있습니다 **필드 매핑** 원본 JSON 문서의 속성 중 대상 검색 인덱스의 필드와 일치 하는 경우. 


### <a name="rest-example"></a>REST 예제

이 섹션은 개체를 만드는 데 사용 되는 모든 요청의 요약입니다. 구성 요소 파트의 내용은이 문서의 이전 섹션을 참조 하세요.

### <a name="data-source-request"></a>데이터 원본 요청

모든 인덱서는 기존 데이터에 대 한 연결 정보를 제공 하는 데이터 원본 개체를 해야 합니다. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>인덱스 요청

모든 인덱서는 데이터를 수신 하는 대상 인덱스가 필요 합니다. 요청 본문에 필드를 검색할 수 있는 인덱스에 원하는 동작을 지원 하기 위해 특성을 사용으로 이루어진 인덱스 스키마를 정의 합니다. 이 인덱스는 인덱서를 실행 하는 경우 비어 있어야 합니다. 

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>인덱서 요청

이 요청에는 완벽 하 게 지정 된 인덱서를 보여 줍니다. 이전 예제에서 생략 된 필드 매핑을 포함 합니다. 해당 "일정", "parameters"를 회수 하 고 "fieldMappings"은 선택 사항으로 사용할 수 있는 기본 키를 누릅니다. 즉시 실행 되도록 인덱서를 사용 하면 "예약"을 생략 합니다. "Json" 기본값을 사용 하려면 인덱스를 하면 "parsingMode"를 생략 합니다.

Azure Search에서 인덱서를 만드는 데이터 가져오기를 트리거합니다. 실행 즉시 하 고 그 후 일정에 따라 하나를 제공한 경우.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>.NET SDK 사용

.NET SDK는 REST API와 완전히 동일한 기능을 제공합니다. 개념, 워크플로 및 요구 사항을 알아보려면 이전 REST API 섹션을 검토하는 것이 좋습니다. 그런 후, 다음 .NET API 참조 설명서를 참조하여 관리되는 코드에서 JSON 인덱서를 구현할 수 있습니다.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>구문 분석 모드

JSON blob는 여러 폼을 가정할 수 있습니다. 합니다 **parsingMode** JSON 인덱서에 매개 변수는 JSON blob 콘텐츠 구문 분석 되 고 Azure Search 인덱스를 구성 하는 방법을 결정 합니다.

| parsingMode | 설명 |
|-------------|-------------|
| `json`  | 각 blob을 단일 문서로 인덱스입니다. 이것이 기본값입니다. |
| `jsonArray` | Blob의 JSON 배열을 구성 하 고 Azure Search에서 별도 문서로 나타내려고 배열의 각 요소 필요한 경우이 모드를 선택 합니다. |
|`jsonLines` | Blob를 새 줄으로 구분 된 JSON 엔터티를 여러 구성 되며 각 엔터티를 Azure Search에서 별도 문서를 해야 하는 경우이 모드를 선택 합니다. |

문서를 검색 결과의 단일 항목으로 생각할 수 있습니다. 개별 항목으로 검색 결과에 표시 된 배열의 각 요소를 하려는 경우 사용 합니다 `jsonArray` 또는 `jsonLines` 적절 하 게 옵션.

인덱서 정의 내에서 [필드 매핑](search-indexer-field-mappings.md)을 사용하여 대상 검색 인덱스를 채우는 데 사용되는 원본 JSON 문서의 속성을 선택할 수도 있습니다. 에 대 한 `jsonArray` 배열의 하위 속성으로 존재 하는 경우 구문 분석 모드 blob 내에서 배열의 위치를 나타내는 문서 루트를 설정할 수 있습니다.

> [!IMPORTANT]
> 사용 하는 경우 `json`하십시오 `jsonArray` 또는 `jsonLines` 구문 분석 모드를 Azure Search 데이터 원본의 모든 blob 포함 하 고 가정 JSON입니다. 동일한 데이터 원본에서 JSON 및 비 JSON BLOB을 지원해야 하는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search)를 통해 알려주세요.


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>단일 JSON blob을 구문 분석

기본적으로 [Azure Search BLOB 인덱서](search-howto-indexing-azure-blob-storage.md) 는 단일 텍스트 청크로 JSON BLOB을 구문 분석합니다. JSON 문서의 구조를 유지하려는 경우가 많습니다. 예를 들어 Azure Blob Storage에 다음 JSON 문서가 있다고 가정합니다.

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Blob 인덱서는 JSON 문서를 단일 Azure Search 문서로 구문 분석합니다. 인덱서는 동일하게 명명되고 형식이 지정된 대상 인덱스 필드에 대해 원본의 "text", "datePublished" 및 "tags"를 일치시켜 인덱스를 로드합니다.

명시된 대로 필드 매핑이 필요하지 않습니다. "text", "datePublished" 및 "tags" 필드를 포함하는 인덱스가 지정된 Blob 인덱서는 요청에서 필드 매핑 없이 올바른 매핑을 유추할 수 있습니다.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON 배열을 구문 분석

또는 JSON 배열 옵션을 사용할 수 있습니다. Blob를 포함 하는 경우이 옵션이 유용 합니다.는 *올바른 형식의 JSON 개체 배열을*, 각 요소는 별도 Azure Search 문서로 나타내려고 하 고 있습니다. 예를 들어 다음 JSON Blob의 경우 각각 "id" 및 "text" 필드가 있는 별도의 3개 문서로 Azure Search 인덱스를 채울 수 있습니다.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON 배열의 경우 인덱서 정의는 다음 예제와 비슷해야 합니다. parsingMode 매개 변수는 `jsonArray` 파서를 지정합니다. 올바른 파서를 지정 하 고 적절 한 데이터를 입력은 JSON blob 인덱싱에 대 한 두 개의 배열 관련 요구 사항입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

다시 말하지만 필드 매핑은 생략할 수 있습니다. 동일하게 명명된 "id" 및 "text" 필드를 포함하는 인덱스가 있는 경우 Blob 인덱서는 명시적인 필드 매핑 목록 없이 올바른 매핑을 유추할 수 있습니다.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>중첩 된 배열 구문 분석
지정할 수 있는 중첩 된 요소 JSON 배열에 대 한는 `documentRoot` 여러 수준의 구조를 나타내는입니다. 예를 들어 blob은 다음과 같습니다.

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

다음 구성을 사용하여 `level2` 속성에 포함된 배열을 인덱싱합니다.

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>줄 바꿈 문자로 구분 된 blob을 구문 분석

줄 바꿈 문자를 구분 하는 여러 JSON 엔터티를 포함 하는 blob을 각 요소는 별도 Azure Search 문서로 나타내려고 하는 경우 JSON 줄 옵션을 선택할 수 있습니다. 예를 들어, 다음 blob을 지정 (있는 세 가지 다른 JSON 엔터티)에서 각각 "id" 및 "text" 필드가 있는 세 가지 별도 문서를 사용 하 여 Azure Search 인덱스를 채울 수 있습니다.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

JSON 줄에 대 한 인덱서 정의 다음 예와 유사 합니다. parsingMode 매개 변수는 `jsonLines` 파서를 지정합니다. 

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

마찬가지로 생략 마찬가지로 필드 매핑 수 있는지를 확인 합니다 `jsonArray` 구문 분석 모드입니다.

## <a name="add-field-mappings"></a>필드 매핑 추가

원본 및 대상 필드가 완벽하게 정렬되지 않은 경우 명시적 필드 간 연결을 위해 요청 본문에서 필드 매핑 섹션을 정의할 수 있습니다.

현재 Azure Search 인덱스 수 없습니다 임의의 JSON 문서 직접 기본 데이터 형식, 문자열 배열 및 GeoJSON 포인트를 지원 하기 때문에. 그러나 **필드 매핑** 을 사용하여 JSON 문서의 부분을 선택하고 검색 문서의 최상위 필드로 이 부분을 "올릴" 수 있습니다. 필드 매핑 기본 사항에 대한 자세한 내용은 [Azure Search 인덱서의 필드 매핑](search-indexer-field-mappings.md)을 참조하세요.

예제 JSON 문서 다시 방문:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

`Edm.String` 형식의 `text`, `Edm.DateTimeOffset` 형식의 `date` 및 `Collection(Edm.String)` 형식의 `tags` 필드를 포함하는 검색 인덱스가 있다고 가정해 봅니다. 원본의 "datePublished"와 인덱스의 `date` 필드 간에 불일치를 확인합니다. JSON을 원하는 모양으로 매핑하려면 다음 필드 매핑을 사용합니다.

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

매핑의 원본 필드 이름은 [JSON 포인터](https://tools.ietf.org/html/rfc6901) 표기법을 사용하여 지정됩니다. JSON 문서의 루트를 참조하도록 슬래시로 시작한 다음 슬래시로 구분된 경로를 사용하여 (임의의 중첩 수준에서) 원하는 속성을 선택합니다.

또한 0부터 시작하는 인덱스를 사용하여 개별 배열 요소를 참조할 수 있습니다. 예를 들어 위의 예제에서 "tags" 배열의 첫 번째 요소를 선택하려면 다음과 같은 필드 매핑을 사용합니다.

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 필드 매핑 경로의 원본 필드 이름이 JSON에 없는 속성을 참조하는 경우 해당 매핑은 오류 없이 건너뜁니다. 다른 스키마를 사용하여 문서를 지원할 수 있도록 이 작업을 수행합니다(일반적인 사용 사례). 유효성을 검사하지 않기 때문에 필드 매핑 사양에 오타가 발생하지 않도록 주의해야 합니다.
>
>

## <a name="see-also"></a>참고 항목

+ [Azure Search의 인덱서](search-indexer-overview.md)
+ [Azure Search로 Azure Blob Storage 인덱싱](search-howto-index-json-blobs.md)
+ [Azure Search Blob 인덱서를 사용하여 CSV Blob 인덱싱](search-howto-index-csv-blobs.md)
+ [자습서: Azure Blob Storage에서 반구조화된 데이터 검색](search-semi-structured-data.md)
