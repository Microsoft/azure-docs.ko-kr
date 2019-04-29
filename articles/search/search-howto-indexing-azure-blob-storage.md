---
title: 전체 텍스트 검색을 위해 Azure Blob Storage 콘텐츠 인덱싱 - Azure Search
description: Azure Blob Storage를 인덱싱하고 Azure Search를 사용하여 문서에서 텍스트를 추출하는 방법을 알아봅니다.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 87dc1dab0670f69ff8c418be476986baec2821fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871350"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Azure Search로 Azure Blob Storage에서 문서 인덱싱
이 문서에서는 Azure Search를 사용하여 Azure Blob Storage에 저장된 문서(예: PDF, Office 파일 및 다양한 기타 일반적인 형식)를 인덱싱하는 방법을 보여줍니다. 먼저, blob 인덱서 설정 및 구성의 기본 사항을 설명합니다. 그런 다음, 동작 및 발생할 수 있는 시나리오의 심층적 탐색을 제공합니다.

## <a name="supported-document-formats"></a>지원되는 문서 형식
BLOB 인덱서는 다음과 같은 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>BLOB 인덱싱 설정
다음을 사용하여 Azure Blob Storage 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> 일부 기능(예: 필드 매핑)은 포털에서 아직 사용할 수 없으며 프로그래밍 방식으로 사용해야 합니다.
>

여기에서는 REST API를 사용하여 흐름을 설명합니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기
데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 데이터 원본을 동일한 검색 서비스의 여러 인덱서에서 사용할 수 있습니다.

데이터 원본에는 BLOB 인덱싱을 위한 다음과 같은 필수 속성이 있어야 합니다.

* **name**은 검색 서비스 내 데이터 원본의 고유 이름입니다.
* **type**은 `azureblob`여야 합니다.
* **credentials**는 저장소 계정 연결 문자열을 `credentials.connectionString` 매개 변수로 제공합니다. 자세한 내용은 아래에서 [자격 증명을 지정하는 방법](#Credentials)을 참조하세요.
* **container**는 저장소 계정에 있는 컨테이너를 지정합니다. 기본적으로 컨테이너 내의 모든 BLOB은 검색 가능합니다. 특정 가상 디렉터리의 BLOB만 인덱싱하려면 선택 사항인 **query** 매개 변수를 사용하여 해당 디렉터리를 지정할 수 있습니다,

데이터 원본을 만드는 방법:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

데이터 원본 만들기 API에 대한 자세한 내용은 [데이터 원본 만들기](https://docs.microsoft.com/rest/api/searchservice/create-data-source)를 참조하세요.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>자격 증명을 지정하는 방법 ####

Blob 컨테이너에 대한 자격 증명을 제공하는 방법은 다음 중 하나입니다.

- **전체 액세스 스토리지 계정 연결 문자열**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` 저장소 계정 블레이드로 이동 하 여 Azure portal에서 연결 문자열을 가져올 수 있습니다 > 설정 > 키 (클래식 저장소 계정) 또는 설정 > 액세스 키 (Azure Resource Manager 저장소 계정).
- **스토리지 계정 공유 액세스 서명**(SAS) 연결 문자열: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS에 컨테이너 및 개체(이 경우 Blob)에 대한 읽기 권한 및 목록이 있어야 합니다.
-  **컨테이너 공유 액세스 서명**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS에 컨테이너에 대한 읽기 권한 및 목록이 있어야 합니다.

저장소 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

> [!NOTE]
> SAS 자격 증명을 사용하는 경우 자격 증명이 만료되는 것을 방지하기 위해 갱신된 서명을 사용하여 데이터 원본 자격 증명을 주기적으로 업데이트해야 합니다. SAS 자격 증명이 만료되면 `Credentials provided in the connection string are invalid or have expired.`와 유사한 오류 메시지가 표시되면서 인덱서가 실행되지 못합니다.  

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기
인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

다음은 검색 가능한`content` 필드가 있는 인덱스를 만들어 blob에서 추출된 텍스트를 저장하는 방법입니다.   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

인덱스 만들기에 자세한 내용은 [인덱스 만들기](https://docs.microsoft.com/rest/api/searchservice/create-index)를 참조하세요.

### <a name="step-3-create-an-indexer"></a>3단계: 인덱서 만들기
인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](https://docs.microsoft.com/rest/api/searchservice/create-indexer)를 확인하세요.

## <a name="how-azure-search-indexes-blobs"></a>Azure Search가 BLOB을 인덱싱하는 방식

[인덱서 구성](#PartsOfBlobToIndex)에 따라, Blob 인덱서는 저장소 메타데이터만 인덱싱하거나(메타데이터만 필요하고 Blob 콘텐츠를 인덱싱할 필요가 없는 경우에 유용함), 저장소 및 콘텐츠 메타데이터를 인덱싱하거나, 메타데이터와 텍스트 콘텐츠 모두를 인덱싱할 수 있습니다. 기본적으로 인덱서는 메타데이터와 콘텐츠를 둘 다 추출합니다.

> [!NOTE]
> 기본적으로 JSON 또는 CSV와 같이 구조화된 콘텐츠를 포함하는 Blob은 단일 텍스트 청크로 인덱싱됩니다. 구조화된 방식으로 JSON 및 CSV Blob을 인덱싱하려면 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md) 및 [CSV BLOB 인덱싱](search-howto-index-csv-blobs.md) 미리 보기 기능을 참조하세요.
>
> 복합 또는 포함된 문서(예: ZIP 보관 파일 또는 첨부 파일이 있는 Outlook 메일이 포함된 Word 문서)도 단일 문서로 인덱싱됩니다.

* 문서의 텍스트 콘텐츠가 `content`라는 문자열 필드로 추출됩니다.

> [!NOTE]
> Azure Search는 가격 책정 계층에 따라 추출하는 텍스트의 양을 제한합니다. 무료 계층은 32,000 문자, 기본 계층은 64,000 문자, 그리고 표준, 표준 S2 및 표준 S3 계층은 4,000,000 문자입니다. 잘린 문서의 인덱서 상태 응답에는 경고가 포함되어 있습니다.  

* BLOB에 있는 사용자 지정 메타데이터 속성은 그대로 추출됩니다(있는 경우).
* 표준 BLOB 메타데이터 속성이 다음 필드로 추출됩니다.

  * **metadata\_storage\_name**(Edm.String) - BLOB의 파일 이름. 예를 들어 blob /my-container/my-folder/subfolder/resume.pdf를 포함하는 경우 이 필드의 값은 `resume.pdf`입니다.
  * **metadata\_storage\_path**(Edm.String - 스토리지 계정을 포함한 BLOB의 전체 URI. 위치(예: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type**(Edm.String) - BLOB를 업로드하기 위해 사용한 코드에 지정된 콘텐츠 형식. 예: `application/octet-stream`
  * **metadata\_storage\_last\_modified**(Edm.DateTimeOffset) - BLOB에 대해 마지막으로 수정된 타임스탬프. Azure Search는 이 타임스탬프로 변경된 BLOB을 식별하여 초기 인덱싱 후 모든 항목을 다시 인덱싱하는 것을 방지합니다.
  * **metadata\_storage\_size** (Edm.Int64) - BLOB 크기(바이트).
  * **metadata\_storage\_content\_md5**(Edm.String) - BLOB 콘텐츠의 MD5 해시(사용 가능한 경우).
* 각 문서 형식과 관련된 메타데이터 속성이 [여기](#ContentSpecificMetadata) 나열된 필드로 추출됩니다.

검색 인덱스에서 위의 모든 속성에 대한 필드를 정의하지 않아도 되는 경우 애플리케이션에 필요한 속성만 캡처합니다.

> [!NOTE]
> 기존 인덱스의 필드 이름이 문서 추출 중에 생성된 필드 이름과 달라지는 경우가 있습니다. **필드 매핑**을 사용하여 Azure Search에서 제공한 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 아래에 필드 매핑 사용 예제가 있습니다.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>문서 키 및 필드 매핑 정의
Azure Search에서는 문서 키가 문서를 고유하게 식별합니다. 모든 검색 인덱스는 Edm.String 형식의 키 필드를 정확히 하나만 포함해야 합니다. 인덱스에 추가할 각 문서에는 키 필드가 필요합니다(이 필드는 실제로 유일한 필수 필드임).  

어떤 추출된 필드를 인덱스에 대한 키 필드에 매핑할지 신중하게 고려해야 합니다. 후보는 다음과 같습니다.

* **metadata\_storage\_name** - 편리한 후보일 수 있으나 1) 다른 폴더에 같은 이름을 가진 BLOB를 포함할 수 있으므로 이름이 고유하지 않을 수 있으며 2) 이름에 대시와 같은 문서 키로 유효하지 않은 문자가 포함될 수 있습니다. `base64Encode` [필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 유효하지 않은 문자를 처리할 수 있습니다. 이렇게 하면 Lookup과 같은 API 호출에 전달할 때 문서 키를 인코딩해야 합니다. 예를 들어 .NET에서 이러한 용도로 [UrlTokenEncode 메서드](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)를 사용할 수 있습니다.
* **metadata\_storage\_path** - 전체 경로를 사용하여 고유성을 보장할 수 있지만 해당 경로에 [문서 키로 유효하지 않은](https://docs.microsoft.com/rest/api/searchservice/naming-rules) `/` 문자가 분명히 포함됩니다.  위와 같이 `base64Encode` [함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 키를 인코딩하는 옵션이 제공됩니다.
* 위의 옵션이 작동하지 않는 경우 BLOB에 사용자 지정 메타데이터 속성을 추가할 수 있습니다. 그러나 이 옵션에는 해당 메타데이터 속성을 모든 BLOB에 추가하는 BLOB 업로드 프로세스가 필요합니다. 키는 필수 속성이므로 해당 속성이 없는 모든 BLOB는 인덱싱에 실패합니다.

> [!IMPORTANT]
> 인덱스의 키 필드에 대한 명시적인 매핑이 없는 경우 Azure Search에서 자동으로 `metadata_storage_path`를 키로 사용하고 키 값을 base-64로 인코딩합니다(위 두 번째 옵션).
>
>

이 예제에서는 문서 키로 `metadata_storage_name` 필드를 선택하겠습니다. 또한 인덱스에 `key` 키 필드와 문서 크기를 저장하는 `fileSize` 필드가 있다고 가정해보겠습니다. 원하는 대로 연결하려면 인덱서를 만들거나 업데이트할 때 다음 필드 매핑을 지정합니다.

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

이를 모두 함께 연결하기 위해 필드 매핑을 추가하고 기존 인덱서에 대한 키의 base-64 인코딩을 사용하도록 설정하는 방법은 다음과 같습니다.

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> 필드 매핑에 대한 자세한 내용은 [이 문서](search-indexer-field-mappings.md)를 참조하세요.
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>인덱싱할 Blob 제어
인덱싱할 Blob과 건너뛸 Blob을 제어할 수 있습니다.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>특정 파일 확장명을 가진 Blob만 인덱싱
`indexedFileNameExtensions` 인덱서 구성 매개 변수를 사용하여 지정한 파일 이름 확장명을 가진 Blob만 인덱싱할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PDF 및 .DOCX Blob만을 인덱싱하려면 다음을 수행합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>특정 파일 확장명으로 Blob 제외
`excludedFileNameExtensions` 구성 매개 변수를 사용하여 인덱싱에서 특정 파일 이름 확장명으로 Blob를 제외할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PNG 및 .JPEG 확장명을 가진 Blob을 제외한 모든 Blob을 인덱싱하려면 다음을 수행합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

`indexedFileNameExtensions` 및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure Search는 먼저 `indexedFileNameExtensions`를 확인한 후 `excludedFileNameExtensions`를 찾습니다. 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외되는 것을 의미합니다.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Blob에서 인덱싱할 부분 제어

`dataToExtract` 구성 매개 변수를 사용하여 Blob에서 인덱싱할 부분을 제어할 수 있습니다. 사용되는 값은 다음과 같습니다.

* `storageMetadata` - [표준 BLOB 속성 및 사용자가 지정한 메타데이터](../storage/blobs/storage-properties-metadata.md)만 인덱싱되도록 지정합니다.
* `allMetadata` - BLOB 콘텐츠에서 추출한 [Content-Type별 메타데이터](#ContentSpecificMetadata) 및 저장소 메타데이터가 인덱싱되도록 지정합니다.
* `contentAndMetadata` - Blob에서 추출한 모든 메타데이터 및 텍스트 콘텐츠가 인덱싱되도록 지정합니다. 기본값입니다.

예를 들어 저장소 메타데이터만 인덱싱하려면 다음을 사용합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB 메타데이터를 사용하여 BLOB이 인덱싱되는 방식 제어

위에 설명된 구성 매개 변수는 모든 Blob에 적용됩니다. 어떤 때는 *개별 Blob*이 인덱싱되는 방식을 제어해야 하는 경우도 있습니다. 다음 Blob 메타데이터 속성 및 값을 추가하여 이 작업을 수행할 수 있습니다.

| 속성 이름 | 속성 값 | 설명 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |BLOB 인덱서가 BLOB을 완전히 건너뛰도록 지시합니다. 메타데이터와 콘텐츠 추출이 모두 시도되지 않습니다. 특정 BLOB이 반복적으로 실패하고 인덱싱 프로세스가 중단되는 경우에 유용합니다. |
| AzureSearch_SkipContent |"true" |특정 Blob으로 범위가 지정된 [위에](#PartsOfBlobToIndex) 설명된 `"dataToExtract" : "allMetadata"` 설정과 동일합니다. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>오류 처리

기본적으로 Blob 인덱서는 지원되지 않는 콘텐츠 형식(예: 이미지)을 포함하는 Blob을 발견하는 즉시 중지됩니다. 물론 `excludedFileNameExtensions` 매개 변수를 사용하여 특정 콘텐츠 형식을 건너뛸 수 있습니다. 하지만, 있을 수 있는 모든 콘텐츠 형식을 미리 알지 못하는 상태에서 Blob을 인덱싱해야 하는 경우도 있습니다. 지원되지 않는 콘텐츠 형식이 발견될 때 인덱싱을 계속하려면 `failOnUnsupportedContentType` 구성 매개 변수를 `false`로 설정합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

일부 Blob의 경우 Azure Search는 콘텐츠 형식을 확인할 수 없거나 지원되는 다른 콘텐트 형식의 문서를 처리할 수 없습니다. 이 오류 모드를 무시하려면 `failOnUnprocessableDocument` 구성 매개 변수를 False로 설정합니다.

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Search는 인덱싱되는 Blob의 크기를 제한합니다. 이러한 제한 사항은 [Azure Search의 서비스 제한 사항](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)에서 설명됩니다. 너무 큰 Blob은 기본적으로 오류로 처리됩니다. 그러나 `indexStorageMetadataOnlyForOversizedDocuments` 구성 매개 변수를 true로 설정한 경우 너무 큰 Blob의 저장소 메타데이터를 여전히 인덱싱할 수 있습니다. 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

또한 Blob을 구문 분석하거나 문서를 인덱스를 추가할 때 임의 처리 지점에서 오류가 발생하는 경우에도 인덱싱을 계속할 수 있습니다. 설정 개수의 오류를 무시하려면 `maxFailedItems` 및 `maxFailedItemsPerBatch` 구성 매개 변수를 원하는 값으로 설정합니다. 예를 들면 다음과 같습니다.

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 감지
BLOB 인덱서가 일정에 따라 실행되도록 설정하는 경우 BLOB의 `LastModified` 타임스탬프에 지정된 대로 변경된 BLOB만 다시 인덱싱합니다.

> [!NOTE]
> 변경 감지 정책을 지정하지 않아도 됩니다. 증분 인덱싱이 자동으로 사용됩니다.

문서 삭제를 지원하려면 "일시 삭제" 방법을 사용합니다. Blob을 완전히 삭제해도 해당 문서는 검색 인덱스에서 제거되지 않습니다. 대신 다음 단계를 사용합니다.  

1. 논리적으로 삭제되었음을 Azure Search에 나타내도록 사용자 지정 메타데이터 속성을 Blob에 추가합니다.
2. 데이터 원본에서 일시 삭제 검색 정책을 구성합니다.
3. 인덱서에서 Blob을 처리하고 나면(인덱서 상태 API로 표시됨) 이 Blob을 물리적으로 삭제할 수 있습니다.

예를 들어 다음 정책은 `true` 값의 메타데이터 속성 `IsDeleted`가 있는 경우 Blob을 삭제해야 하는 것으로 간주합니다.

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>큰 데이터 세트 인덱싱

BLOB 인덱싱은 시간이 오래 걸리는 프로세스입니다. 인덱싱할 Blob이 수백만 개인 경우에는 데이터를 분할하고 데이터를 병렬로 처리하도록 여러 인덱서를 사용하여 인덱싱 속도를 높일 수 있습니다. 설정 방법은 다음과 같습니다.

- 데이터를 여러 BLOB 컨테이너 또는 가상 폴더로 분할합니다.
- Azure Search 데이터 원본을 컨테이너 또는 폴더당 하나씩 여러 개 설정합니다. BLOB 폴더를 가리키려면 `query` 매개 변수를 사용합니다.

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- 각 데이터 소스에 해당하는 인덱서를 만듭니다. 모든 인덱서가 동일한 대상 검색 인덱스를 가리킬 수 있습니다.  

- 서비스에서 하나의 검색 단위가 지정된 시점에 하나의 인덱서를 실행할 수 있습니다. 위에서 설명한 대로 여러 인덱서를 만들면 실제 병렬로 실행하는 경우에 유용합니다. 동시에 여러 인덱서를 실행하려면 적절한 수의 파티션 및 복제본을 만들어서 검색 서비스를 확장합니다. 예를 들어 검색 서비스에 6개의 검색 단위(예: 2개 파티션x3개 복제본)가 있으면 6개의 인덱서가 동시에 실행될 수 있고 그로 인해 인덱싱 처리량이 6배 증가합니다. 크기 조정 및 수용작업량 계획에 대해 자세히 알아보려면 [Azure Search에서 쿼리 및 인덱싱 워크로드에 대한 리소스 수준 크기 조정](search-capacity-planning.md)을 참조하세요.

## <a name="indexing-documents-along-with-related-data"></a>관련된 데이터와 함께 문서 인덱싱

인덱스에 있는 여러 원본의 문서를 "조합"할 수도 있습니다. 예를 들어 Cosmos DB에 저장된 다른 메타데이터와 BLOB의 텍스트를 병합할 수 있습니다. 푸시 인덱싱 API를 다양한 인덱서와 함께 사용하여 여러 부분에서 검색 문서를 구축할 수도 있습니다. 

이렇게 하려면 모든 인덱서 및 기타 구성 요소가 문서 키에 동의해야 합니다. 자세한 내용은 다음 외부 문서를 참조하세요. [Azure Search의 다른 데이터와 문서 결합](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)합니다.

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>일반 텍스트 인덱싱 

모든 Blob에 동일한 인코딩의 일반 텍스트가 포함된 경우 **텍스트 구문 분석 모드**를 사용하여 인덱싱 성능을 크게 향상시킬 수 있습니다. 텍스트 구문 분석 모드를 사용하려면 `parsingMode` 구성 속성을 `text`로 설정합니다.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

기본적으로 `UTF-8` 인코딩이 간주됩니다. 다른 인코딩을 지정하려면 `encoding` 구성 속성을 사용하세요. 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>콘텐츠 형식별 메타데이터 속성
다음 표에서는 각 문서 형식에 대해 수행된 처리를 요약하고 Azure Search에서 추출한 메타데이터 속성에 대해 설명합니다.

| 문서 형식/콘텐츠 형식 | 콘텐츠 형식별 메타데이터 속성 | 처리 세부 정보 |
| --- | --- | --- |
| HTML(`text/html`) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML 태그를 제거하고 텍스트 추출 |
| PDF(`application/pdf`) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출(이미지 제외) |
| DOCX(application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| DOC(application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| XLSX(application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| XLS(application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| PPTX(application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPT(application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| MSG(application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_message_bcc`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |첨부 파일을 비롯한 텍스트 추출 |
| ZIP(application/zip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| XML(application/xml) |`metadata_content_type`</br>`metadata_content_encoding`</br> |XML 태그를 제거하고 텍스트 추출 |
| JSON(application/json) |`metadata_content_type`</br>`metadata_content_encoding` |텍스트 추출<br/>참고:  JSON BLOB에서 여러 문서 필드를 추출해야 하는 경우 자세한 내용은 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md)을 참조하세요. |
| EML(메시지/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |첨부 파일을 비롯한 텍스트 추출 |
| RTF(application/rtf) |`metadata_content_type`</br>`metadata_author`</br>`metadata_character_count`</br>`metadata_creation_date`</br>`metadata_page_count`</br>`metadata_word_count`</br> | 텍스트 추출|
| 일반 텍스트(text/plain) |`metadata_content_type`</br>`metadata_content_encoding`</br> | 텍스트 추출|


## <a name="help-us-make-azure-search-better"></a>Azure Search 개선 지원
요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 알려주세요.
