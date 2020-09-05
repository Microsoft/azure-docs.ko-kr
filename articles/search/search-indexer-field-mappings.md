---
title: 인덱서의 필드 매핑
titleSuffix: Azure Cognitive Search
description: 필드 이름과 데이터 표현의 차이를 고려 하 여 인덱서에 필드 매핑을 구성 합니다.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fe4d42fd74b4efd67a01f32611bd170862ec84d0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007131"
---
# <a name="field-mappings-and-transformations-using-azure-cognitive-search-indexers"></a>Azure Cognitive Search 인덱서를 사용 하 여 필드 매핑 및 변환

![인덱서 단계](./media/search-indexer-field-mappings/indexer-stages-field-mappings.png "인덱서 단계")

Azure Cognitive Search 인덱서를 사용 하는 경우 입력 데이터가 대상 인덱스의 스키마와 일치 하지 않는 경우가 종종 있습니다. 이러한 경우 인덱싱 프로세스 중에 **필드 매핑을** 사용 하 여 데이터의 모양을 변경할 수 있습니다.

필드 매핑이 유용한 일부 상황:

* 데이터 원본에 라는 필드가 `_id` 있지만 Azure Cognitive Search는 밑줄로 시작 하는 필드 이름을 허용 하지 않습니다. 필드 매핑을 사용 하면 필드를 효과적으로 바꿀 수 있습니다.
* 동일한 데이터 원본 데이터에서 인덱스의 여러 필드를 채워야 합니다. 예를 들어 이러한 필드에 다른 분석기를 적용 하는 것이 좋습니다.
* 두 개 이상의 데이터 원본에 있는 데이터를 사용 하 여 인덱스 필드를 채우면 서로 다른 필드 이름을 사용 합니다.
* 데이터를 Base64 인코딩 또는 디코딩해야 합니다. 필드 매핑은 Base64 인코딩 및 디코딩에 대한 함수를 포함한 여러 **매핑 함수**를 지원합니다.

> [!NOTE]
> 인덱서의 필드 매핑은 간단한 데이터 변환 기능을 사용 하 여 데이터 필드를 인덱스 필드에 매핑하는 간단한 방법입니다. 더 복잡 한 데이터는 인덱싱에 취약 형식으로 변형 하기 위해 전처리가 필요할 수 있습니다. 고려할 수 있는 한 가지 옵션은 [Azure Data Factory](../data-factory/index.yml)입니다.

## <a name="set-up-field-mappings"></a>필드 매핑 설정

필드 매핑은 다음 세 부분으로 구성됩니다.

1. `sourceFieldName`은(는) 데이터 원본의 필드를 나타냅니다. 이 속성은 필수입니다.
2. 선택 사항 `targetFieldName`은(는) 검색 인덱스의 필드를 나타냅니다. 생략된 경우 데이터 원본과 동일한 이름이 사용됩니다.
3. 선택 사항 `mappingFunction`은(는) 미리 정의된 여러 함수 중 하나를 사용하여 데이터를 변환할 수 있습니다. 이는 입력 및 출력 필드 매핑 모두에 적용 될 수 있습니다. 함수의 전체 목록은 [아래](#mappingFunctions)입니다.

필드 매핑은 `fieldMappings` 인덱서 정의의 배열에 추가 됩니다.

> [!NOTE]
> 필드 매핑이 추가 되지 않은 경우 인덱서는 데이터 원본 필드가 동일한 이름의 인덱스 필드에 매핑되어야 한다고 가정 합니다. 필드 매핑을 추가 하면 원본 및 대상 필드에 대 한 기본 필드 매핑이 제거 됩니다. [Blob 저장소 인덱서와](search-howto-indexing-azure-blob-storage.md)같은 일부 인덱서는 인덱스 키 필드에 대 한 기본 필드 매핑을 추가 합니다.

## <a name="map-fields-using-the-rest-api"></a>REST API를 사용 하 여 필드 매핑

[인덱서 만들기](/rest/api/searchservice/create-Indexer) API 요청을 사용 하 여 새 인덱서를 만들 때 필드 매핑을 추가할 수 있습니다. [Update 인덱서](/rest/api/searchservice/update-indexer) API 요청을 사용 하 여 기존 인덱서의 필드 매핑을 관리할 수 있습니다.

예를 들어 소스 필드를 다른 이름으로 대상 필드에 매핑하는 방법은 다음과 같습니다.

```JSON

PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
Content-Type: application/json
api-key: [admin key]
{
    "dataSourceName" : "mydatasource",
    "targetIndexName" : "myindex",
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ]
}
```

원본 필드는 여러 필드 매핑에서 참조할 수 있습니다. 다음 예에서는 필드를 "포크" 하 여 동일한 원본 필드를 두 개의 서로 다른 인덱스 필드로 복사 하는 방법을 보여 줍니다.

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Cognitive Search는 대/소문자를 구분 하지 않는 비교를 사용 하 여 필드 매핑의 필드 및 함수 이름을 확인 합니다. 이는 편리(모든 대/소문자를 올바르게 할 필요가 없음)하지만 데이터 원본 또는 인덱스가 대/소문자만으로 다른 필드를 가질 수 없음을 의미합니다.  
>
>

## <a name="map-fields-using-the-net-sdk"></a>.NET SDK를 사용 하 여 필드 매핑

속성과 및 선택적 참조를 포함 하는 [Fieldmapping](/dotnet/api/microsoft.azure.search.models.fieldmapping) 클래스를 사용 하 여 .net SDK에서 필드 매핑을 정의 `SourceFieldName` `TargetFieldName` `MappingFunction` 합니다.

인덱서를 생성할 때 또는 나중에 속성을 직접 설정 하 여 필드 매핑을 지정할 수 있습니다 `Indexer.FieldMappings` .

다음 c # 예제에서는 인덱서를 생성할 때 필드 매핑을 설정 합니다.

```csharp
  List<FieldMapping> map = new List<FieldMapping> {
    // removes a leading underscore from a field name
    new FieldMapping("_custId", "custId"),
    // URL-encodes a field for use as the index key
    new FieldMapping("docPath", "docId", FieldMappingFunction.Base64Encode() )
  };

  Indexer sqlIndexer = new Indexer(
    name: "azure-sql-indexer",
    dataSourceName: sqlDataSource.Name,
    targetIndexName: index.Name,
    fieldMappings: map,
    schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

  await searchService.Indexers.CreateOrUpdateAsync(indexer);
```

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>필드 매핑 함수

필드 매핑 함수는 인덱스에 저장 되기 전에 필드의 내용을 변환 합니다. 현재 지원 되는 매핑 함수는 다음과 같습니다.

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)
* [urlEncode](#urlEncodeFunction)
* [urlDecode](#urlDecodeFunction)

<a name="base64EncodeFunction"></a>

### <a name="base64encode-function"></a>base64Encode 함수

입력된 문자열의 *URL 안전* Base64 인코딩을 수행합니다. 입력이 UTF-8 인코딩되었다고 가정합니다.

#### <a name="example---document-key-lookup"></a>예제-문서 키 조회

고객이 [조회 API](/rest/api/searchservice/lookup-document) 를 사용 하 여 문서를 처리할 수 있어야 하기 때문에 URL 안전 문자만 Azure Cognitive Search 문서 키에 나타날 수 있습니다. 키의 원본 필드에 URL-안전 하지 않은 문자가 포함 된 경우 함수를 사용 하 여 인덱싱할 때 변환할 수 있습니다 `base64Encode` . 그러나 문서 키 (변환 전후)는 1024 자를 초과할 수 없습니다.

검색 시 인코딩된 키를 검색 하는 경우 함수를 사용 `base64Decode` 하 여 원래 키 값을 가져온 다음이를 사용 하 여 소스 문서를 검색할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "base64Encode",
      "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false }
    }
  }]
 ```

#### <a name="example---preserve-original-values"></a>예-원래 값 유지

[Blob 저장소 인덱서](search-howto-indexing-azure-blob-storage.md) 는 `metadata_storage_path` 필드 매핑이 지정 되지 않은 경우 blob의 URI에 있는 필드 매핑을 인덱스 키 필드에 자동으로 추가 합니다. 이 값은 b a s e 64로 인코딩되어 Azure Cognitive Search 문서 키로 사용 하는 것이 안전 합니다. 다음 예에서는의 *URL 안전* Base64 인코딩된 버전을 필드에 동시에 매핑하고 `metadata_storage_path` `index_key` 원래 값을 필드에 유지 하는 방법을 보여 줍니다 `metadata_storage_path` .

```JSON

"fieldMappings": [
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "metadata_storage_path"
  },
  {
    "sourceFieldName": "metadata_storage_path",
    "targetFieldName": "index_key",
    "mappingFunction": {
       "name": "base64Encode"
    }
  }
]
```

매핑 함수에 대 한 매개 변수 속성을 포함 하지 않는 경우 기본값은 value `{"useHttpServerUtilityUrlTokenEncode" : true}` 입니다.

Azure Cognitive Search는 두 개의 다른 Base64 인코딩을 지원 합니다. 동일한 필드를 인코딩 및 디코딩할 때 동일한 매개 변수를 사용 해야 합니다. 자세한 내용은 사용할 매개 변수를 결정 하는 [base64 인코딩 옵션](#base64details) 을 참조 하세요.

<a name="base64DecodeFunction"></a>

### <a name="base64decode-function"></a>base64Decode 함수

입력 문자열의 Base64 디코딩을 수행합니다. 입력은 *URL 안전* Base64 인코딩된 문자열로 간주 됩니다.

#### <a name="example---decode-blob-metadata-or-urls"></a>예제-blob 메타 데이터 또는 Url 디코딩

원본 데이터에는 일반 텍스트로 검색 하려는 blob 메타 데이터 문자열 또는 웹 Url과 같은 Base64 인코딩 문자열이 포함 될 수 있습니다. 함수를 사용 하 여 `base64Decode` 검색 인덱스를 채울 때 인코딩된 데이터를 일반 문자열로 다시 전환할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { 
      "name" : "base64Decode", 
      "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false }
    }
  }]
```

매개 변수 속성을 포함 하지 않는 경우 기본값은 value `{"useHttpServerUtilityUrlTokenEncode" : true}` 입니다.

Azure Cognitive Search는 두 개의 다른 Base64 인코딩을 지원 합니다. 동일한 필드를 인코딩 및 디코딩할 때 동일한 매개 변수를 사용 해야 합니다. 자세한 내용은 사용할 매개 변수를 결정 하는 [base64 인코딩 옵션](#base64details) 을 참조 하세요.

<a name="base64details"></a>

#### <a name="base64-encoding-options"></a>base64 인코딩 옵션

Azure Cognitive Search는 URL 안전 base64 인코딩 및 일반 base64 인코딩을 지원 합니다. 인덱싱을 수행 하는 동안 base64로 인코딩된 문자열은 나중에 동일한 인코딩 옵션을 사용 하 여 디코딩해야 합니다. 그렇지 않으면 결과가 원본과 일치 하지 않습니다.

`useHttpServerUtilityUrlTokenEncode` `useHttpServerUtilityUrlTokenDecode` 인코딩 및 디코딩에 대 한 또는 매개 변수가 각각로 설정 된 경우는 HttpServerUtility와 같은 동작을 수행 하 `true` `base64Encode` [HttpServerUtility.UrlTokenEncode](/dotnet/api/system.web.httpserverutility.urltokenencode?view=netframework-4.8) 고 `base64Decode` [HttpServerUtility UrlTokenDecode](/dotnet/api/system.web.httpserverutility.urltokendecode?view=netframework-4.8)처럼 동작 합니다.

> [!WARNING]
> `base64Encode`키 값을 생성 하는 데를 사용 하는 경우에는를 `useHttpServerUtilityUrlTokenEncode` true로 설정 해야 합니다. 키 값에는 URL 안전 base64 인코딩만 사용할 수 있습니다. 키 값의 문자에 대 한 전체 제한 사항 집합은 [Azure Cognitive Search&#41;&#40;명명 규칙 ](/rest/api/searchservice/naming-rules) 을 참조 하세요.

Azure Cognitive Search의 .NET 라이브러리는 기본 제공 인코딩을 제공 하는 전체 .NET Framework를 가정 합니다. `useHttpServerUtilityUrlTokenEncode`및 `useHttpServerUtilityUrlTokenDecode` 옵션은이 기본 제공 기능을 활용 합니다. .NET Core 또는 다른 프레임 워크를 사용 하는 경우 해당 옵션을로 설정 하 `false` 고 프레임 워크의 인코딩 및 디코딩 함수를 직접 호출 하는 것이 좋습니다.

다음 표에서는 문자열 `00>00?00`의 서로 다른 base64 인코딩을 비교합니다. base64 함수에 필요한 추가 처리를 판단하려면(있는 경우) `00>00?00` 문자열에서 라이브러리 인코딩 함수를 적용하고 출력을 `MDA-MDA_MDA` 예상 출력과 비교합니다.

| Encoding | Base64 인코딩 출력 | 라이브러리 인코딩 후 추가 처리 | 라이브러리 인코딩 전 추가 처리 |
| --- | --- | --- | --- |
| Base64(패딩 있음) | `MDA+MDA/MDA=` | URL 지원 문자 사용 및 패딩 제거 | 표준 base64 문자 사용 및 패딩 추가 |
| Base64(패딩 없음) | `MDA+MDA/MDA` | URL 지원 문자 사용 | 표준 base64 문자 사용 |
| URL 지원 Base64(패딩 있음) | `MDA-MDA_MDA=` | 패딩 제거 | 패딩 추가 |
| URL 지원 Base64(패딩 없음) | `MDA-MDA_MDA` | 없음 | 없음 |

<a name="extractTokenAtPositionFunction"></a>

### <a name="extracttokenatposition-function"></a>extractTokenAtPosition 함수

지정된 구분 기호를 사용하여 문자열 필드를 분할하고 결과 분할의 지정된 위치에서 토큰을 선택합니다.

이 함수는 다음 매개 변수를 사용 합니다.

* `delimiter`: 입력 문자열을 분할할 때 구분 기호로 사용할 문자열입니다.
* `position`: 입력 문자열을 분할한 후 선택할 정수 0부터 시작하는 토큰의 위치입니다.

예를 들어 입력은 `Jane Doe`, `delimiter`는 `" "`(공백), `position`은 0인 경우 결과는 `Jane`입니다. `position`이 1이면 결과는 `Doe`입니다. 위치가 없는 토큰을 참조하는 경우 오류가 반환됩니다.

#### <a name="example---extract-a-name"></a>예-이름 추출

데이터 원본이 `PersonName` 필드를 포함하고 두 개의 개별 `FirstName` 및 `LastName` 필드로 인덱싱하려고 합니다. 구분 기호로 공백 문자를 사용하여 입력을 분할하는 데 이 함수를 사용할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "FirstName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 0 } }
  },
  {
    "sourceFieldName" : "PersonName",
    "targetFieldName" : "LastName",
    "mappingFunction" : { "name" : "extractTokenAtPosition", "parameters" : { "delimiter" : " ", "position" : 1 } }
  }]
```

<a name="jsonArrayToStringCollectionFunction"></a>

### <a name="jsonarraytostringcollection-function"></a>jsonArrayToStringCollection 함수

JSON 문자열 배열 형식으로 생성된 문자열을 인덱스의 `Collection(Edm.String)` 필드를 채우는 데 사용할 수 있는 문자열 배열로 변환합니다.

예를 들어 입력 문자열이 `["red", "white", "blue"]`이면 `Collection(Edm.String)` 형식의 대상 필드는 세 개의 값 `red`, `white` 및 `blue`로 채워집니다. JSON 문자열 배열로 구문 분석할 수 없는 입력 값의 경우 오류가 반환됩니다.

#### <a name="example---populate-collection-from-relational-data"></a>예-관계형 데이터에서 컬렉션 채우기

Azure SQL Database는 `Collection(Edm.String)` Azure Cognitive Search의 필드에 자연스럽 게 매핑되는 기본 제공 데이터 형식이 없습니다. 문자열 컬렉션 필드를 채우기 위해 소스 데이터를 JSON 문자열 배열로 미리 처리 한 다음 매핑 함수를 사용할 수 있습니다 `jsonArrayToStringCollection` .

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "tags", 
    "mappingFunction" : { "name" : "jsonArrayToStringCollection" }
  }]
```

<a name="urlEncodeFunction"></a>

### <a name="urlencode-function"></a>urlEncode 함수

이 함수를 사용 하 여 문자열을 "URL safe"로 인코딩할 수 있습니다. URL에 허용 되지 않는 문자가 포함 된 문자열을 사용 하는 경우이 함수는 "unsafe" 문자를 해당 하는 문자 엔터티로 변환 합니다. 이 함수는 UTF-8 인코딩 형식을 사용 합니다.

#### <a name="example---document-key-lookup"></a>예제-문서 키 조회

`urlEncode``base64Encode`URL 안전 하지 않은 문자만 변환 되 고 다른 문자는 그대로 유지 하는 경우 함수에 대 한 대 안으로 함수를 사용할 수 있습니다.

즉, 입력 문자열이 이면 `<hello>` 형식의 대상 필드에 값이 채워집니다. `(Edm.String)``%3chello%3e`

검색 시 인코딩된 키를 검색 하는 경우 함수를 사용 `urlDecode` 하 여 원래 키 값을 가져온 다음이를 사용 하 여 소스 문서를 검색할 수 있습니다.

```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : {
      "name" : "urlEncode"
    }
  }]
 ```

 <a name="urlDecodeFunction"></a>

 ### <a name="urldecode-function"></a>urlDecode 함수

 이 함수는 UTF-8 인코딩 형식을 사용 하 여 URL로 인코딩된 문자열을 디코딩된 문자열로 변환 합니다.

 ### <a name="example---decode-blob-metadata"></a>예제-blob 메타 데이터 디코딩

 일부 Azure storage 클라이언트는 ASCII가 아닌 문자를 포함 하는 경우 blob 메타 데이터를 자동으로 인코딩합니다. 그러나 이러한 메타 데이터를 검색할 수 있게 하려면 (일반 텍스트) 함수를 사용 하 여 `urlDecode` 검색 인덱스를 채울 때 인코딩된 데이터를 일반 문자열로 다시 변환할 수 있습니다.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "UrlEncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : {
      "name" : "urlDecode"
    }
  }]
 ```
 
 <a name="fixedLengthEncodeFunction"></a>
 
 ### <a name="fixedlengthencode-function"></a>fixedLengthEncode 함수
 
 이 함수는 임의의 길이의 문자열을 고정 길이 문자열로 변환 합니다.
 
 ### <a name="example---map-document-keys-that-are-too-long"></a>예제-너무 긴 문서 키 매핑
 
오류가 발생 하는 경우 문서 키가 1024 자 보다 불만이이 함수를 적용 하 여 문서 키의 길이를 줄일 수 있습니다.

 ```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "metadata_storage_path",
    "targetFieldName" : "your key field",
    "mappingFunction" : {
      "name" : "fixedLengthEncode"
    }
  }]
 ```