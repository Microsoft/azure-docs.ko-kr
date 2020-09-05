---
title: Azure Blob storage 콘텐츠를 검색 합니다.
titleSuffix: Azure Cognitive Search
description: Azure Blob Storage에서 문서를 인덱싱하고 Azure Cognitive Search를 사용 하 여 문서에서 텍스트를 추출 하는 방법을 알아봅니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 9caa377ebcdff5b0ae379f1b0b8269dac5b8f499
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924098"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Azure Cognitive Search를 사용 하 여 Azure Blob Storage에서 문서를 인덱싱하는 방법

이 문서에서는 azure Cognitive Search를 사용 하 여 Azure Blob storage에 저장 된 문서 (예: Pdf, Microsoft Office 문서 및 다른 몇 가지 일반적인 형식)를 인덱싱하는 방법을 보여 줍니다. 먼저, blob 인덱서 설정 및 구성의 기본 사항을 설명합니다. 그런 다음, 동작 및 발생할 수 있는 시나리오의 심층적 탐색을 제공합니다.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>지원되는 문서 형식
BLOB 인덱서는 다음과 같은 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>BLOB 인덱싱 설정
다음을 사용하여 Azure Blob Storage 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/overview/azure/search)

> [!NOTE]
> 일부 기능(예: 필드 매핑)은 포털에서 아직 사용할 수 없으며 프로그래밍 방식으로 사용해야 합니다.
>

여기에서는 REST API를 사용하여 흐름을 설명합니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기
데이터 원본은 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명 및 데이터 변경 내용(예: 수정되거나 삭제된 행)을 효율적으로 식별할 수 있도록 해주는 정책을 지정합니다. 데이터 원본을 동일한 검색 서비스의 여러 인덱서에서 사용할 수 있습니다.

데이터 원본에는 BLOB 인덱싱을 위한 다음과 같은 필수 속성이 있어야 합니다.

* **name**은 검색 서비스 내 데이터 원본의 고유 이름입니다.
* **type**은 `azureblob`여야 합니다.
* **credentials**는 스토리지 계정 연결 문자열을 `credentials.connectionString` 매개 변수로 제공합니다. 자세한 내용은 아래에서 [자격 증명을 지정하는 방법](#Credentials)을 참조하세요.
* **container**는 스토리지 계정에 있는 컨테이너를 지정합니다. 기본적으로 컨테이너 내의 모든 BLOB은 검색 가능합니다. 특정 가상 디렉터리의 BLOB만 인덱싱하려면 선택 사항인 **query** 매개 변수를 사용하여 해당 디렉터리를 지정할 수 있습니다,

데이터 원본을 만드는 방법:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   
```

데이터 원본 만들기 API에 대한 자세한 내용은 [데이터 원본 만들기](/rest/api/searchservice/create-data-source)를 참조하세요.

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>자격 증명을 지정하는 방법 ####

Blob 컨테이너에 대한 자격 증명을 제공하는 방법은 다음 중 하나입니다.

- **전체 액세스 저장소 계정 연결 문자열**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` 저장소 계정 블레이드 > 설정 > 키 (클래식 저장소 계정) 또는 설정 > 액세스 키 (Azure Resource Manager 저장소 계정)로 이동 하 여 Azure Portal에서 연결 문자열을 가져올 수 있습니다.
- **Storage 계정 SAS(공유 액세스 서명)** 연결 문자열: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` SAS에 컨테이너 및 개체(이 경우 Blob)에 대한 읽기 권한 및 목록이 있어야 합니다.
-  **컨테이너 공유 액세스 서명**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` SAS에 컨테이너에 대 한 읽기 권한 및 목록이 있어야 합니다.

스토리지 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 사용](../storage/common/storage-sas-overview.md)을 참조하세요.

> [!NOTE]
> SAS 자격 증명을 사용하는 경우 자격 증명이 만료되는 것을 방지하기 위해 갱신된 서명을 사용하여 데이터 원본 자격 증명을 주기적으로 업데이트해야 합니다. SAS 자격 증명이 만료되면 `Credentials provided in the connection string are invalid or have expired.`와 유사한 오류 메시지가 표시되면서 인덱서가 실행되지 못합니다.  

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기
인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

다음은 검색 가능한`content` 필드가 있는 인덱스를 만들어 blob에서 추출된 텍스트를 저장하는 방법입니다.   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

인덱스 만들기에 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.

### <a name="step-3-create-an-indexer"></a>3단계: 인덱서 만들기
인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 일정 지정 방법](search-howto-schedule-indexers.md)을 참조하세요.

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Azure Cognitive Search 인덱스 blob 방법

[인덱서 구성](#PartsOfBlobToIndex)에 따라, Blob 인덱서는 스토리지 메타데이터만 인덱싱하거나(메타데이터만 필요하고 Blob 콘텐츠를 인덱싱할 필요가 없는 경우에 유용함), 스토리지 및 콘텐츠 메타데이터를 인덱싱하거나, 메타데이터와 텍스트 콘텐츠 모두를 인덱싱할 수 있습니다. 기본적으로 인덱서는 메타데이터와 콘텐츠를 둘 다 추출합니다.

> [!NOTE]
> 기본적으로 JSON 또는 CSV와 같이 구조화된 콘텐츠를 포함하는 Blob은 단일 텍스트 청크로 인덱싱됩니다. 구조화 된 방식으로 JSON 및 CSV blob을 인덱싱하는 방법에 대 한 자세한 내용은 [json Blob 인덱싱](search-howto-index-json-blobs.md) 및 [csv blob 인덱싱](search-howto-index-csv-blobs.md) 을 참조 하세요.
>
> 복합 또는 포함 된 문서 (예: ZIP 보관 파일, 첨부 파일이 포함 된 Outlook 전자 메일이 포함 된 Word 문서) 또는입니다. 첨부 파일이 있는 메시지 파일)도 단일 문서로 인덱싱됩니다. 예를 들어의 첨부 파일에서 추출한 모든 이미지입니다. 메시지 파일은 normalized_images 필드에 반환 됩니다.

* 문서의 텍스트 콘텐츠가 `content`라는 문자열 필드로 추출됩니다.

> [!NOTE]
> Azure Cognitive Search는 가격 책정 계층에 따라 추출 되는 텍스트의 양을 제한 합니다. 무료 계층의 경우 32000 자, 400만 Basic의 경우 64000, standard S2의 경우 800만, 표준 S3의 경우 1600만입니다. 잘린 문서의 인덱서 상태 응답에는 경고가 포함되어 있습니다.  

* BLOB에 있는 사용자 지정 메타데이터 속성은 그대로 추출됩니다(있는 경우). 이렇게 하려면 blob의 메타 데이터 키와 동일한 이름의 인덱스에 필드가 정의 되어 있어야 합니다. 예를 들어 blob의 메타 데이터 키가 `Sensitivity` value 인 경우 `High` 검색 인덱스에 라는 필드를 정의 해야 `Sensitivity` 하며 값으로 채워집니다 `High` .
* 표준 BLOB 메타데이터 속성이 다음 필드로 추출됩니다.

  * **metadata\_storage\_name**(Edm.String) - BLOB의 파일 이름. 예를 들어 blob /my-container/my-folder/subfolder/resume.pdf를 포함하는 경우 이 필드의 값은 `resume.pdf`입니다.
  * **metadata\_storage\_path**(Edm.String - 스토리지 계정을 포함한 BLOB의 전체 URI. 예를 들어 `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type**(Edm.String) - BLOB를 업로드하기 위해 사용한 코드에 지정된 콘텐츠 형식. 예: `application/octet-stream`
  * **metadata\_storage\_last\_modified**(Edm.DateTimeOffset) - BLOB에 대해 마지막으로 수정된 타임스탬프. Azure Cognitive Search는이 타임 스탬프를 사용 하 여 초기 인덱싱 후 모든 항목을 다시 인덱싱하도록 방지 하기 위해 변경 된 blob를 식별 합니다.
  * **metadata\_storage\_size** (Edm.Int64) - BLOB 크기(바이트).
  * **metadata\_storage\_content\_md5**(Edm.String) - BLOB 콘텐츠의 MD5 해시(사용 가능한 경우).
  * **메타 데이터 \_ 저장소 \_ sas \_ 토큰** (Edm. 문자열)-blob에 대 한 액세스 권한을 얻기 위해 [사용자 지정 기술](cognitive-search-custom-skill-interface.md) 에서 사용할 수 있는 임시 sas 토큰입니다. 이 토큰은 만료 될 수 있으므로 나중에 사용할 수 있도록 저장 해서는 안 됩니다.

* 각 문서 형식과 관련된 메타데이터 속성이 [여기](#ContentSpecificMetadata) 나열된 필드로 추출됩니다.

검색 인덱스에서 위의 모든 속성에 대한 필드를 정의하지 않아도 되는 경우 애플리케이션에 필요한 속성만 캡처합니다.

> [!NOTE]
> 기존 인덱스의 필드 이름이 문서 추출 중에 생성된 필드 이름과 달라지는 경우가 있습니다. **필드 매핑을** 사용 하 여 Azure Cognitive Search에서 제공 하는 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 아래에 필드 매핑 사용 예제가 있습니다.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>문서 키 및 필드 매핑 정의
Azure Cognitive Search에서 문서 키는 문서를 고유 하 게 식별 합니다. 모든 검색 인덱스는 Edm.String 형식의 키 필드를 정확히 하나만 포함해야 합니다. 인덱스에 추가할 각 문서에는 키 필드가 필요합니다(이 필드는 실제로 유일한 필수 필드임).  

어떤 추출된 필드를 인덱스에 대한 키 필드에 매핑할지 신중하게 고려해야 합니다. 후보는 다음과 같습니다.

* **metadata\_storage\_name** - 편리한 후보일 수 있으나 1) 다른 폴더에 같은 이름을 가진 BLOB를 포함할 수 있으므로 이름이 고유하지 않을 수 있으며 2) 이름에 대시와 같은 문서 키로 유효하지 않은 문자가 포함될 수 있습니다. `base64Encode` [필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 유효하지 않은 문자를 처리할 수 있습니다. 이렇게 하면 Lookup과 같은 API 호출에 전달할 때 문서 키를 인코딩해야 합니다. 예를 들어 .NET에서 이러한 용도로 [UrlTokenEncode 메서드](/dotnet/api/system.web.httpserverutility.urltokenencode?view=netframework-4.8)를 사용할 수 있습니다.
* **metadata\_storage\_path** - 전체 경로를 사용하여 고유성을 보장할 수 있지만 해당 경로에 [문서 키로 유효하지 않은](/rest/api/searchservice/naming-rules)`/` 문자가 분명히 포함됩니다.  위와 같이 `base64Encode` [함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 키를 인코딩하는 옵션이 제공됩니다.
* 위의 옵션이 작동하지 않는 경우 BLOB에 사용자 지정 메타데이터 속성을 추가할 수 있습니다. 그러나 이 옵션에는 해당 메타데이터 속성을 모든 BLOB에 추가하는 BLOB 업로드 프로세스가 필요합니다. 키는 필수 속성이므로 해당 속성이 없는 모든 BLOB는 인덱싱에 실패합니다.

> [!IMPORTANT]
> 인덱스의 키 필드에 대 한 명시적 매핑이 없는 경우 Azure Cognitive Search는 자동으로를 `metadata_storage_path` 키로 사용 하 고 64을 기반으로 키 값을 인코딩합니다 (위의 두 번째 옵션).
>
>

이 예제에서는 문서 키로 `metadata_storage_name` 필드를 선택하겠습니다. 또한 인덱스에 `key` 키 필드와 문서 크기를 저장하는 `fileSize` 필드가 있다고 가정해보겠습니다. 원하는 대로 연결하려면 인덱서를 만들거나 업데이트할 때 다음 필드 매핑을 지정합니다.

```http
    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]
```

이를 모두 함께 연결하기 위해 필드 매핑을 추가하고 기존 인덱서에 대한 키의 base-64 인코딩을 사용하도록 설정하는 방법은 다음과 같습니다.

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
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
```

> [!NOTE]
> 필드 매핑에 대한 자세한 내용은 [이 문서](search-indexer-field-mappings.md)를 참조하세요.
>
>

#### <a name="what-if-you-need-to-encode-a-field-to-use-it-as-a-key-but-you-also-want-to-search-it"></a>키로 사용 하기 위해 필드를 인코딩해야 하지만 검색 하려면 어떻게 해야 하나요?

Metadata_storage_path와 같은 인코딩된 버전의 필드를 키로 사용 해야 하는 경우가 있지만 해당 필드를 검색할 수 있어야 합니다 (인코딩 없이). 이 문제를 해결 하기 위해 두 개의 필드에 매핑할 수 있습니다. 하나는 키에 사용 되 고 다른 하나는 검색 목적으로 사용 됩니다. 아래 예제에서 *키* 필드는 인코딩된 경로를 포함 하는 반면 *경로* 필드는 인코딩되지 않고 인덱스에서 검색 가능 필드로 사용 됩니다.

```http
    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
      ]
    }
```
<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>인덱싱할 Blob 제어
인덱싱할 Blob과 건너뛸 Blob을 제어할 수 있습니다.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>특정 파일 확장명을 가진 Blob만 인덱싱
`indexedFileNameExtensions` 인덱서 구성 매개 변수를 사용하여 지정한 파일 이름 확장명을 가진 Blob만 인덱싱할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PDF 및 .DOCX Blob만을 인덱싱하려면 다음을 수행합니다.

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }
```

### <a name="exclude-blobs-with-specific-file-extensions"></a>특정 파일 확장명으로 Blob 제외
`excludedFileNameExtensions` 구성 매개 변수를 사용하여 인덱싱에서 특정 파일 이름 확장명으로 Blob를 제외할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PNG 및 .JPEG 확장명을 가진 Blob을 제외한 모든 Blob을 인덱싱하려면 다음을 수행합니다.

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }
```

`indexedFileNameExtensions`및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure Cognitive Search는 먼저를 확인 `indexedFileNameExtensions` 한 다음에서를 찾습니다 `excludedFileNameExtensions` . 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외되는 것을 의미합니다.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Blob에서 인덱싱할 부분 제어

`dataToExtract` 구성 매개 변수를 사용하여 Blob에서 인덱싱할 부분을 제어할 수 있습니다. 사용되는 값은 다음과 같습니다.

* `storageMetadata` - [표준 BLOB 속성 및 사용자가 지정한 메타데이터](../storage/blobs/storage-blob-container-properties-metadata.md)만 인덱싱되도록 지정합니다.
* `allMetadata` - BLOB 콘텐츠에서 추출한 [Content-Type별 메타데이터](#ContentSpecificMetadata) 및 스토리지 메타데이터가 인덱싱되도록 지정합니다.
* `contentAndMetadata` - Blob에서 추출한 모든 메타데이터 및 텍스트 콘텐츠가 인덱싱되도록 지정합니다. 이것은 기본값입니다.

예를 들어 스토리지 메타데이터만 인덱싱하려면 다음을 사용합니다.

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }
```

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB 메타데이터를 사용하여 BLOB이 인덱싱되는 방식 제어

위에 설명된 구성 매개 변수는 모든 Blob에 적용됩니다. 어떤 때는 *개별 Blob*이 인덱싱되는 방식을 제어해야 하는 경우도 있습니다. 다음 Blob 메타데이터 속성 및 값을 추가하여 이 작업을 수행할 수 있습니다.

| 속성 이름 | 속성 값 | 설명 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |BLOB 인덱서가 BLOB을 완전히 건너뛰도록 지시합니다. 메타데이터와 콘텐츠 추출이 모두 시도되지 않습니다. 특정 BLOB이 반복적으로 실패하고 인덱싱 프로세스가 중단되는 경우에 유용합니다. |
| AzureSearch_SkipContent |"true" |특정 Blob으로 범위가 지정된 [위에](#PartsOfBlobToIndex) 설명된 `"dataToExtract" : "allMetadata"` 설정과 동일합니다. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>오류 처리

기본적으로 Blob 인덱서는 지원되지 않는 콘텐츠 형식(예: 이미지)을 포함하는 Blob을 발견하는 즉시 중지됩니다. 물론 `excludedFileNameExtensions` 매개 변수를 사용하여 특정 콘텐츠 형식을 건너뛸 수 있습니다. 하지만, 있을 수 있는 모든 콘텐츠 형식을 미리 알지 못하는 상태에서 Blob을 인덱싱해야 하는 경우도 있습니다. 지원되지 않는 콘텐츠 형식이 발견될 때 인덱싱을 계속하려면 `failOnUnsupportedContentType` 구성 매개 변수를 `false`로 설정합니다.

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }
```

일부 blob의 경우 Azure Cognitive Search에서 콘텐츠 형식을 확인할 수 없거나 지원 되지 않는 콘텐츠 형식의 문서를 처리할 수 없습니다. 이 오류 모드를 무시하려면 `failOnUnprocessableDocument` 구성 매개 변수를 False로 설정합니다.

```http
      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Cognitive Search은 인덱싱되는 blob의 크기를 제한 합니다. 이러한 한도는 [Azure Cognitive Search의 서비스 제한](./search-limits-quotas-capacity.md)사항에 설명 되어 있습니다. 너무 큰 Blob은 기본적으로 오류로 처리됩니다. 그러나 `indexStorageMetadataOnlyForOversizedDocuments` 구성 매개 변수를 true로 설정한 경우 너무 큰 Blob의 스토리지 메타데이터를 여전히 인덱싱할 수 있습니다. 

```http
    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

또한 Blob을 구문 분석하거나 문서를 인덱스를 추가할 때 임의 처리 지점에서 오류가 발생하는 경우에도 인덱싱을 계속할 수 있습니다. 설정 개수의 오류를 무시하려면 `maxFailedItems` 및 `maxFailedItemsPerBatch` 구성 매개 변수를 원하는 값으로 설정합니다. 예를 들면

```http
    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }
```

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 감지

BLOB 인덱서가 일정에 따라 실행되도록 설정하는 경우 BLOB의 `LastModified` 타임스탬프에 지정된 대로 변경된 BLOB만 다시 인덱싱합니다.

> [!NOTE]
> 변경 감지 정책을 지정하지 않아도 됩니다. 증분 인덱싱이 자동으로 사용됩니다.

문서 삭제를 지원하려면 "일시 삭제" 방법을 사용합니다. Blob을 완전히 삭제해도 해당 문서는 검색 인덱스에서 제거되지 않습니다.

일시 삭제 방식을 구현 하는 방법에는 두 가지가 있습니다. 두 가지 모두 아래에 설명 되어 있습니다.

### <a name="native-blob-soft-delete-preview"></a>네이티브 Blob 일시 삭제(미리 보기)

> [!IMPORTANT]
> 기본 blob 일시 삭제에 대 한 지원은 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. [REST API 버전 2020-06-30-미리 보기](./search-api-preview.md) 에서이 기능을 제공 합니다. 현재 포털 또는 .NET SDK가 지원 되지 않습니다.

> [!NOTE]
> 네이티브 blob 일시 삭제 정책을 사용 하는 경우 인덱스의 문서에 대 한 문서 키는 blob 속성 또는 blob 메타 데이터 여야 합니다.

이 방법에서는 Azure Blob 저장소에서 제공 하는 [기본 blob 일시 삭제](../storage/blobs/soft-delete-blob-overview.md) 기능을 사용 합니다. 저장소 계정에서 기본 blob 일시 삭제를 사용 하는 경우 데이터 원본에 기본 일시 삭제 정책이 설정 되어 있고 인덱서가 일시 삭제 된 상태로 전환 된 blob을 찾은 경우 인덱서는 인덱스에서 해당 문서를 제거 합니다. Azure Data Lake Storage Gen2에서 blob을 인덱싱하는 경우에는 네이티브 blob 일시 삭제 정책이 지원 되지 않습니다.

다음 단계를 사용합니다.
1. [Azure Blob storage에 대해 네이티브 일시 삭제를](../storage/blobs/soft-delete-blob-overview.md)사용 하도록 설정 합니다. 보존 정책을 인덱서 간격 일정 보다 훨씬 더 높은 값으로 설정 하는 것이 좋습니다. 이러한 방식으로 인덱서를 실행 하는 데 문제가 있거나 인덱싱할 문서 수가 많은 경우 인덱서는 결국 일시 삭제 된 blob을 처리 하는 데 많은 시간이 발생 합니다. Azure Cognitive Search 인덱서는 blob이 일시 삭제 된 상태에 있는 동안 blob을 처리 하는 경우에만 인덱스에서 문서를 삭제 합니다.
1. 데이터 원본에 대 한 기본 blob 일시 삭제 검색 정책을 구성 합니다. 아래에 예제가 나와 있습니다. 이 기능은 미리 보기 상태 이므로 미리 보기 REST API를 사용 해야 합니다.
1. 인덱서를 실행 하거나 인덱서를 일정에 따라 실행 하도록 설정 합니다. 인덱서를 실행 하 고 blob을 처리 하면 문서가 인덱스에서 제거 됩니다.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>삭제 취소 한 blob 인덱스

저장소 계정에서 네이티브 일시 삭제를 사용 하도록 설정 하 여 Azure Blob storage에서 blob을 삭제 하는 경우 blob는 보존 기간 내에 해당 blob을 삭제 취소 하는 옵션을 제공 하는 일시 삭제 됨 상태로 전환 됩니다. Azure Cognitive Search 데이터 원본에 기본 blob 일시 삭제 정책이 있고 인덱서가 일시 삭제 된 blob을 처리 하는 경우 인덱스에서 해당 문서를 제거 합니다. 해당 blob이 나중에 삭제 취소 되는 경우 인덱서는 항상 해당 blob의 인덱스를 다시 만들지 않습니다. 이는 인덱서는 blob의 타임 스탬프에 따라 인덱싱할 blob을 결정 하기 때문입니다 `LastModified` . 일시 삭제 된 blob이 삭제 취소 되 면 `LastModified` 타임 스탬프가 업데이트 되지 않으므로 인덱서가 타임 스탬프를 포함 하는 blob을 이미 처리 한 경우에는 삭제 되지 않은 `LastModified` blob 보다 최신 blob을 다시 인덱싱 하지 않습니다. 삭제 되지 않은 blob을 인덱싱해야 하려면 blob의 타임 스탬프를 업데이트 해야 합니다 `LastModified` . 이 작업을 수행 하는 한 가지 방법은 해당 blob의 메타 데이터를 다시 저장는 것입니다. 메타 데이터를 변경할 필요는 없지만, `LastModified` 인덱서는이 blob을 다시 인덱싱 해야 한다는 것을 알 수 있도록 메타 데이터에서 blob의 타임 스탬프를 업데이트 하는 것을 다시 저장 합니다.

### <a name="soft-delete-using-custom-metadata"></a>사용자 지정 메타 데이터를 사용 하 여 일시 삭제

이 방법에서는 blob의 메타 데이터를 사용 하 여 검색 인덱스에서 문서를 제거 해야 하는 시기를 표시 합니다.

다음 단계를 사용합니다.

1. Blob에 사용자 지정 메타 데이터 키-값 쌍을 추가 하 여 논리적으로 삭제 되는 Azure Cognitive Search를 표시 합니다.
1. 데이터 원본에 대 한 일시 삭제 열 검색 정책을 구성 합니다. 아래에 예제가 나와 있습니다.
1. 인덱서가 blob을 처리 하 고 인덱스에서 문서를 삭제 한 후에는 Azure Blob 저장소에 대 한 blob을 삭제할 수 있습니다.

예를 들어 다음 정책은 `true` 값의 메타데이터 속성 `IsDeleted`가 있는 경우 Blob을 삭제해야 하는 것으로 간주합니다.

```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
```

#### <a name="reindexing-undeleted-blobs"></a>삭제 취소 한 blob 인덱스

데이터 원본에 대해 일시 삭제 열 검색 정책을 설정한 후에는 표식 값을 사용 하 여 blob에 사용자 지정 메타 데이터를 추가한 다음 인덱서를 실행 하면 인덱서가 인덱스에서 해당 문서를 제거 합니다. 해당 문서를 다시 인덱싱 하려면 해당 blob에 대 한 일시 삭제 메타 데이터 값을 변경 하 고 인덱서를 다시 실행 하면 됩니다.

## <a name="indexing-large-datasets"></a>큰 데이터 세트 인덱싱

BLOB 인덱싱은 시간이 오래 걸리는 프로세스입니다. 인덱싱할 Blob이 수백만 개인 경우에는 데이터를 분할하고 데이터를 병렬로 처리하도록 여러 인덱서를 사용하여 인덱싱 속도를 높일 수 있습니다. 설정 방법은 다음과 같습니다.

- 데이터를 여러 BLOB 컨테이너 또는 가상 폴더로 분할합니다.
- 컨테이너 또는 폴더 마다 하나씩 여러 Azure Cognitive Search 데이터 원본을 설정 합니다. BLOB 폴더를 가리키려면 `query` 매개 변수를 사용합니다.

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- 각 데이터 소스에 해당하는 인덱서를 만듭니다. 모든 인덱서가 동일한 대상 검색 인덱스를 가리킬 수 있습니다.  

- 서비스에서 하나의 검색 단위가 지정된 시점에 하나의 인덱서를 실행할 수 있습니다. 위에서 설명한 대로 여러 인덱서를 만들면 실제 병렬로 실행하는 경우에 유용합니다. 동시에 여러 인덱서를 실행하려면 적절한 수의 파티션 및 복제본을 만들어서 검색 서비스를 확장합니다. 예를 들어 검색 서비스에 6개의 검색 단위(예: 2개 파티션x3개 복제본)가 있으면 6개의 인덱서가 동시에 실행될 수 있고 그로 인해 인덱싱 처리량이 6배 증가합니다. 크기 조정 및 용량 계획에 대 한 자세한 내용은 [Azure Cognitive Search의 쿼리 및 인덱싱 작업을 위한 리소스 수준 확장](search-capacity-planning.md)을 참조 하세요.

## <a name="indexing-documents-along-with-related-data"></a>관련된 데이터와 함께 문서 인덱싱

인덱스에 있는 여러 원본의 문서를 "조합"할 수도 있습니다. 예를 들어 Cosmos DB에 저장된 다른 메타데이터와 BLOB의 텍스트를 병합할 수 있습니다. 푸시 인덱싱 API를 다양한 인덱서와 함께 사용하여 여러 부분에서 검색 문서를 구축할 수도 있습니다. 

이렇게 하려면 모든 인덱서 및 기타 구성 요소가 문서 키에 동의해야 합니다. 이 항목에 대 한 자세한 내용은 [여러 Azure 데이터 원본 인덱싱](./tutorial-multiple-data-sources.md)을 참조 하세요. 자세한 연습을 보려면이 외부 문서: [Azure Cognitive Search의 다른 데이터와 문서 결합](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)을 참조 하세요.

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>일반 텍스트 인덱싱 

모든 Blob에 동일한 인코딩의 일반 텍스트가 포함된 경우 **텍스트 구문 분석 모드**를 사용하여 인덱싱 성능을 크게 향상시킬 수 있습니다. 텍스트 구문 분석 모드를 사용하려면 `parsingMode` 구성 속성을 `text`로 설정합니다.

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

기본적으로 `UTF-8` 인코딩이 간주됩니다. 다른 인코딩을 지정하려면 `encoding` 구성 속성을 사용하세요. 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>콘텐츠 형식별 메타데이터 속성
다음 표에서는 각 문서 형식에 대 한 처리 작업을 요약 하 고 Azure Cognitive Search에서 추출 된 메타 데이터 속성을 설명 합니다.

| 문서 형식/콘텐츠 형식 | 콘텐츠 형식별 메타데이터 속성 | 처리 세부 정보 |
| --- | --- | --- |
| HTML (텍스트/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML 태그를 제거하고 텍스트 추출 |
| PDF (응용 프로그램/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출(이미지 제외) |
| DOCX(application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| DOC(application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| .DOCM (application/vnd.ms-word.document macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| WORD XML (application/vnd word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML 태그를 제거하고 텍스트 추출 |
| WORD 2003 XML (application/vnd. ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML 태그를 제거하고 텍스트 추출 |
| XLSX(application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| XLS(application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| .XLSM (application/vnd. vnd.ms-excel. macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| PPTX(application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPT(application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| PPTM (application/vnd. ms-powerpoint macroenabled) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |포함된 문서를 비롯한 텍스트 추출 |
| MSG(application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |첨부 파일에서 추출한 텍스트를 포함 하는 텍스트를 추출 합니다. `metadata_message_to_email``metadata_message_cc_email`및 `metadata_message_bcc_email` 는 문자열 컬렉션입니다. 나머지 필드는 문자열입니다.|
| ODT (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |포함된 문서를 비롯한 텍스트 추출 |
| ODS (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |포함된 문서를 비롯한 텍스트 추출 |
| ODP (application/vnd. oasis) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |포함된 문서를 비롯한 텍스트 추출 |
| ZIP(application/zip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| RELEASE.TAR.GZ (응용 프로그램/gzip) |`metadata_content_type` |보관 파일의 모든 문서에서 텍스트 추출 |
| EPUB (application/EPUB + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |보관 파일의 모든 문서에서 텍스트 추출 |
| XML(application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML 태그를 제거하고 텍스트 추출 |
| JSON(application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |텍스트 추출<br/>참고: JSON BLOB에서 여러 문서 필드를 추출해야 하는 경우 자세한 내용은 [JSON BLOB 인덱싱](search-howto-index-json-blobs.md)을 참조하세요. |
| EML(메시지/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |첨부 파일을 비롯한 텍스트 추출 |
| RTF(application/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | 텍스트 추출|
| 일반 텍스트(text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | 텍스트 추출|


## <a name="help-us-make-azure-cognitive-search-better"></a>Azure Cognitive Search 향상에 도움을 주세요.
요청할 기능이 있거나 개선을 위한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 알려주세요.