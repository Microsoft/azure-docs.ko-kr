---
title: 인덱서를 사용한 자동 인덱싱을 위한 필드 매핑 - Azure Search
description: 필드 이름 및 데이터 표현의 차이를 처리하도록 Azure Search 인덱서 필드 매핑을 구성합니다.
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 9bb8a168f54658ea14b174d19a1795e575646304
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749345"
---
# <a name="field-mappings-in-azure-search-indexers"></a>Azure Search 인덱서의 필드 매핑
Azure Search 인덱서를 사용할 때 입력 데이터가 대상 인덱스 스키마와 정확히 일치하지 않는 경우에 직면할 수 있습니다. 이러한 경우 **필드 매핑** 을 사용하여 데이터를 원하는 모양으로 변환할 수 있습니다.

필드 매핑이 유용한 일부 상황:

* 데이터 원본에 `_id`필드가 있지만 Azure Search가 밑줄로 시작하는 필드 이름을 허용하지 않습니다. 필드 매핑을 사용하면 필드 "이름 바꾸기"가 가능합니다.
* 예를 들어 이러한 필드에 다른 분석기를 적용하려고 하기 때문에 동일한 데이터 원본 데이터를 사용하여 인덱스에 여러 필드를 입력하려고 합니다. 필드 매핑을 사용하여 데이터 원본 필드를 "분기"할 수 있습니다.
* 데이터를 Base64 인코딩 또는 디코딩해야 합니다. 필드 매핑은 Base64 인코딩 및 디코딩에 대한 함수를 포함한 여러 **매핑 함수**를 지원합니다.   

## <a name="setting-up-field-mappings"></a>필드 매핑 설정
[인덱서 만들기](https://msdn.microsoft.com/library/azure/dn946899.aspx) API를 사용하여 새 인덱서를 만들 때 필드 매핑을 추가할 수 있습니다. [인덱서 업데이트](https://msdn.microsoft.com/library/azure/dn946892.aspx) API를 사용하여 인덱싱 인덱서에서 필드 매핑을 관리할 수 있습니다.

필드 매핑은 다음 세 부분으로 구성됩니다.

1. `sourceFieldName`은(는) 데이터 원본의 필드를 나타냅니다. 이 속성은 필수입니다.
2. 선택 사항 `targetFieldName`은(는) 검색 인덱스의 필드를 나타냅니다. 생략된 경우 데이터 원본과 동일한 이름이 사용됩니다.
3. 선택 사항 `mappingFunction`은(는) 미리 정의된 여러 함수 중 하나를 사용하여 데이터를 변환할 수 있습니다. 함수의 전체 목록은 [아래](#mappingFunctions)입니다.

필드 매핑은 인덱서 정의의 `fieldMappings` 배열에 추가되었습니다.

예를 들어 필드 이름의 차이를 수용하는 방법은 다음과 같습니다.

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

인덱서는 여러 필드 매핑을 포함할 수 있습니다. 예를 들어 필드를 "분기"할 수 있는 방법은 다음과 같습니다.

```JSON

"fieldMappings" : [
    { "sourceFieldName" : "text", "targetFieldName" : "textStandardEnglishAnalyzer" },
    { "sourceFieldName" : "text", "targetFieldName" : "textSoundexAnalyzer" }
]
```

> [!NOTE]
> Azure Search는 대/소문자 구분 비교를 사용하여 필드 매핑의 필드 및 함수 이름을 확인합니다. 이는 편리(모든 대/소문자를 올바르게 할 필요가 없음)하지만 데이터 원본 또는 인덱스가 대/소문자만으로 다른 필드를 가질 수 없음을 의미합니다.  
>
>

<a name="mappingFunctions"></a>

## <a name="field-mapping-functions"></a>필드 매핑 함수
이러한 함수는 현재 지원됩니다.

* [base64Encode](#base64EncodeFunction)
* [base64Decode](#base64DecodeFunction)
* [extractTokenAtPosition](#extractTokenAtPositionFunction)
* [jsonArrayToStringCollection](#jsonArrayToStringCollectionFunction)

<a name="base64EncodeFunction"></a>

## <a name="base64encode"></a>base64Encode
입력된 문자열의 *URL 안전* Base64 인코딩을 수행합니다. 입력이 UTF-8 인코딩되었다고 가정합니다.

### <a name="sample-use-case---document-key-lookup"></a>샘플 사용 사례 - 문서 키 조회
고객은 예를 들어 [조회 API](https://docs.microsoft.com/rest/api/searchservice/lookup-document)를 사용하여 문서를 처리할 수 있어야 하기 때문에 URL 지원 문자만 Azure 검색 문서 키에 나타날 수 있습니다. 데이터가 URL로부터 안전하지 않은 문자를 포함하고 검색 인덱스의 키 필드를 채우는 데 사용하려는 경우 이 함수를 사용합니다. 키가 인코딩되면 base64 디코딩을 사용하여 원래 값을 검색할 수 있습니다. 자세한 내용은 [base64 인코딩 및 디코딩](#base64details) 섹션을 참조하세요.

#### <a name="example"></a>예
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode" }
  }]
```

### <a name="sample-use-case---retrieve-original-key"></a>샘플 사용 사례 - 원래 키 검색
BLOB 경로 메타데이터를 통해 BLOB을 문서 키로 인덱싱하는 BLOB 인덱서가 있습니다. 인코딩된 문서 키를 검색한 후 경로를 디코딩하고 BLOB을 다운로드하려 합니다.

#### <a name="example"></a>예
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "SourceKey",
    "targetFieldName" : "IndexKey",
    "mappingFunction" : { "name" : "base64Encode", "parameters" : { "useHttpServerUtilityUrlTokenEncode" : false } }
  }]
 ```

키로 문서를 검색할 필요가 없고 인코딩된 콘텐츠도 디코딩할 필요가 없는 경우 매핑 함수에 대해 `parameters`를 그대로 둡니다. `useHttpServerUtilityUrlTokenEncode`의 기본값은 `true`입니다. 그렇지 않으면 [base64 세부 정보](#base64details) 섹션을 참조하여 사용할 설정을 결정합니다.

<a name="base64DecodeFunction"></a>

## <a name="base64decode"></a>base64Decode
입력 문자열의 Base64 디코딩을 수행합니다. 입력은 *URL 안전* Base64 인코딩된 문자열로 간주됩니다.

### <a name="sample-use-case"></a>샘플 사용 사례
Blob 사용자 지정 메타데이터 값은 ASCII 인코딩되어야 합니다. Base64 인코딩을 사용하여 BLOB 사용자 지정 메타데이터에서 임의의 UTF-8 문자열을 나타낼 수 있습니다. 그러나 의미 있는 검색을 만들려면 검색 인덱스를 채울 때 이 함수를 사용하여 인코딩된 데이터를 “일반" 문자열로 변환시킬 수 있습니다.

#### <a name="example"></a>예
```JSON

"fieldMappings" : [
  {
    "sourceFieldName" : "Base64EncodedMetadata",
    "targetFieldName" : "SearchableMetadata",
    "mappingFunction" : { "name" : "base64Decode", "parameters" : { "useHttpServerUtilityUrlTokenDecode" : false } }
  }]
```

`parameters`를 지정하지 않으면 `useHttpServerUtilityUrlTokenDecode`의 기본값은 `true`입니다. [base64 세부 정보](#base64details) 섹션을 참조하여 사용할 설정을 결정합니다.

<a name="base64details"></a>

### <a name="details-of-base64-encoding-and-decoding"></a>base64 인코딩 및 디코딩 세부 정보
Azure Search에서는 HttpServerUtility URL 토큰과 URL 지원 Base64 인코딩(패딩 제외) 등, 두 가지 Base64 인코딩을 지원합니다. 검색을 위해 문서 키를 인코딩하려면 매핑 함수와 동일한 인코딩을 사용하고, 인덱서가 디코딩할 값을 인코딩하거나, 인덱서가 인코딩한 필드로 디코딩해야 합니다.

인코딩 및 디코딩을 위한 `useHttpServerUtilityUrlTokenEncode` 또는 `useHttpServerUtilityUrlTokenDecode` 매개 변수가 각각 `true`로 설정되면 `base64Encode`는 [HttpServerUtility.UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)처럼 작동하고, `base64Decode`는 [HttpServerUtility.UrlTokenDecode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokendecode.aspx)처럼 작동합니다.

Azure Search 동작을 에뮬레이트하는 데 전체 .NET Framework를 사용하지 않는 경우(즉, .NET Core 또는 다른 프로그래밍 환경을 사용하는 경우) `useHttpServerUtilityUrlTokenEncode` 및 `useHttpServerUtilityUrlTokenDecode`를 `false`로 설정해야 합니다. 사용하는 라이브러리에 따라 base64 인코딩 및 디코딩 유틸리티 함수는 Azure Search와 다를 수 있습니다. 

다음 표에서는 문자열 `00>00?00`의 서로 다른 base64 인코딩을 비교합니다. base64 함수에 필요한 추가 처리를 판단하려면(있는 경우) `00>00?00` 문자열에서 라이브러리 인코딩 함수를 적용하고 출력을 `MDA-MDA_MDA` 예상 출력과 비교합니다.

| Encoding | Base64 인코딩 출력 | 라이브러리 인코딩 후 추가 처리 | 라이브러리 인코딩 전 추가 처리 |
| --- | --- | --- | --- |
| Base64(패딩 있음) | `MDA+MDA/MDA=` | URL 지원 문자 사용 및 패딩 제거 | 표준 base64 문자 사용 및 패딩 추가 |
| Base64(패딩 없음) | `MDA+MDA/MDA` | URL 지원 문자 사용 | 표준 base64 문자 사용 |
| URL 지원 Base64(패딩 있음) | `MDA-MDA_MDA=` | 패딩 제거 | 패딩 추가 |
| URL 지원 Base64(패딩 없음) | `MDA-MDA_MDA` | 없음 | 없음 |

<a name="extractTokenAtPositionFunction"></a>

## <a name="extracttokenatposition"></a>extractTokenAtPosition
지정된 구분 기호를 사용하여 문자열 필드를 분할하고 결과 분할의 지정된 위치에서 토큰을 선택합니다.

예를 들어 입력은 `Jane Doe`, `delimiter`는 `" "`(공백), `position`은 0인 경우 결과는 `Jane`입니다. `position`이 1이면 결과는 `Doe`입니다. 위치가 없는 토큰을 참조하는 경우 오류가 반환됩니다.

### <a name="sample-use-case"></a>샘플 사용 사례
데이터 원본이 `PersonName` 필드를 포함하고 두 개의 개별 `FirstName` 및 `LastName` 필드로 인덱싱하려고 합니다. 구분 기호로 공백 문자를 사용하여 입력을 분할하는 데 이 함수를 사용할 수 있습니다.

### <a name="parameters"></a>매개 변수
* `delimiter`: 입력 문자열을 분할할 때 구분 기호로 사용할 문자열입니다.
* `position`: 입력 문자열을 분할한 후 선택할 정수 0부터 시작하는 토큰의 위치입니다.    

### <a name="example"></a>예
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

## <a name="jsonarraytostringcollection"></a>jsonArrayToStringCollection
JSON 문자열 배열 형식으로 생성된 문자열을 인덱스의 `Collection(Edm.String)` 필드를 채우는 데 사용할 수 있는 문자열 배열로 변환합니다.

예를 들어 입력 문자열이 `["red", "white", "blue"]`이면 `Collection(Edm.String)` 형식의 대상 필드는 세 개의 값 `red`, `white` 및 `blue`로 채워집니다. JSON 문자열 배열로 구문 분석할 수 없는 입력 값의 경우 오류가 반환됩니다.

### <a name="sample-use-case"></a>샘플 사용 사례
Azure SQL 데이터베이스는 Azure Search의 `Collection(Edm.String)` 필드에 자연스럽게 매핑하는 기본 제공 데이터 형식이 없습니다. 문자열 컬렉션 필드를 채우려면 JSON 문자열 배열로 원본 데이터의 서식을 지정하고 이 함수를 사용합니다.

### <a name="example"></a>예
```JSON

"fieldMappings" : [
  { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } }
]
```


## <a name="help-us-make-azure-search-better"></a>Azure Search 개선 지원
기능 요청 또는 개선에 대한 아이디어가 있는 경우 [UserVoice 사이트](https://feedback.azure.com/forums/263029-azure-search/)를 통해 연락해 주세요.
