---
title: Azure Digital Twins에서 Blob을 개체에 추가하는 방법 | Microsoft Docs
description: Azure Digital Twins에서 Blob을 개체에 추가하는 방법을 알아봅니다.
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: ffd7d71c33b569b396b9f8babf8105968ee525b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926503"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Azure Digital Twins에서 개체에 Blob 추가

Blob은 그림 및 로그 같은 일반적인 파일 형식의 비정형 표현입니다. Blob은 MIME 형식(예: "image/jpeg") 및 메타데이터(이름, 설명, 형식 등)를 사용하여 표현하는 데이터의 종류를 추적합니다.

Azure Digital Twins는 Blobs를 디바이스, 공간 및 사용자에 연결할 수 있습니다. Blob은 사용자, 디바이스 사진, 비디오, 맵, 펌웨어 zip, JSON 데이터, 로그 등에 대한 프로필 사진을 나타낼 수 있습니다.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Blob 업로드 개요

다중 파트 요청을 사용하여 Blob을 특정 엔드포인트 및 해당 기능에 업로드할 수 있습니다.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob 메타데이터

**Content-Type** 및 **Content-Disposition** 외에도, Azure Digital Twins Blob 다중 파트 요청은 올바른 JSON 본문을 지정해야 합니다. 제출할 JSON 본문은 수행되는 HTTP 요청 작업의 종류에 따라 달라집니다.

네 가지 주요 JSON 스키마는 다음과 같습니다.

![JSON 스키마][1]

JSON Blob 메타데이터는 다음과 같은 모델을 준수합니다.

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| 특성 | Type | 설명 |
| --- | --- | --- |
| **parentId** | String | Blob을 연결할 부모 엔터티(공백, 디바이스 또는 사용자)입니다. |
| **name** |String | Blob의 이름입니다. |
| **type** | String | Blob의 형식으로, *type* 및 *typeId*를 사용할 수 없습니다.  |
| **typeId** | 정수  | Blob 형식 ID로, *type* 및 *typeId*를 사용할 수 없습니다. |
| **subtype** | String | Blob 하위 형식으로, *subtype* 및 *subtypeId*를 사용할 수 없습니다. |
| **subtypeId** | 정수  | Blob의 하위 형식 ID로, *subtype* 및 *subtypeId*를 사용할 수 없습니다. |
| **description** | String | Blob의 사용자 지정 설명입니다. |
| **sharing** | String | Blob을 공유할 수 있는지 여부로, 열거형 [`None`, `Tree`, `Global`]입니다. |

Blob 메타데이터는 항상 **Content-type** `application/json`을 포함하는 첫 번째 청크 또는 `.json` 파일로 제공됩니다. 파일 데이터는 두 번째 청크에 제공되고, 지원되는 임의 MIME 형식일 수 있습니다.

Swagger 설명서는 이러한 모델 스키마에 대해 매우 자세하게 설명합니다.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[Swagger를 사용하는 방법](./how-to-use-swagger.md)을 읽고 참조 설명서를 사용하는 방법을 알아보세요.

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>Blob 응답 데이터

개별적으로 반환되는 Blob은 다음 JSON 스키마를 준수합니다.

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| 특성 | Type | 설명 |
| --- | --- | --- |
| **id** | String | Blob의 고유한 식별자입니다. |
| **name** |String | Blob의 이름입니다. |
| **parentId** | String | Blob을 연결할 부모 엔터티(공백, 디바이스 또는 사용자)입니다. |
| **type** | String | Blob의 형식으로, *type* 및 *typeId*를 사용할 수 없습니다.  |
| **typeId** | 정수  | Blob 형식 ID로, *type* 및 *typeId*를 사용할 수 없습니다. |
| **subtype** | String | Blob 하위 형식으로, *subtype* 및 *subtypeId*를 사용할 수 없습니다. |
| **subtypeId** | 정수  | Blob의 하위 형식 ID로, *subtype* 및 *subtypeId*를 사용할 수 없습니다. |
| **sharing** | String | Blob을 공유할 수 있는지 여부로, 열거형 [`None`, `Tree`, `Global`]입니다. |
| **description** | String | Blob의 사용자 지정 설명입니다. |
| **contentInfos** | 배열 | 버전을 포함하는 구조화되지 않은 메타데이터 정보를 지정합니다. |
| **fullName** | String | Blob의 전체 이름입니다. |
| **spacePaths** | String | 공간 경로 |

Blob 메타데이터는 항상 **Content-type** `application/json`을 포함하는 첫 번째 청크 또는 `.json` 파일로 제공됩니다. 파일 데이터는 두 번째 청크에 제공되고, 지원되는 임의 MIME 형식일 수 있습니다.

### <a name="blob-multipart-request-examples"></a>Blob 다중 파트 요청 예제

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

텍스트 파일을 blob으로 업로드하고 공백을 삽입하려면 다음에 대해 인증된 HTTP POST 요청을 수행합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

다음 본문을 포함합니다.

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| USER_DEFINED_BOUNDARY | 다중 파트 콘텐츠 경계 이름 |

다음 코드는 동일한 Blob 업로드의 .NET 구현으로 [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) 클래스를 사용합니다.

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

마지막으로, [cURL](https://curl.haxx.se/) 사용자는 다음과 같이 동일한 방식으로 다중 파트 양식 요청을 수행할 수 있습니다.

![디바이스 Blob][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| 값 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_TOKEN | 유효한 OAuth 2.0 토큰 |
| YOUR_SPACE_ID | Blob을 연결할 공간의 ID |
| PATH_TO_FILE | 텍스트 파일의 경로 |

성공한 POST는 새 Blob의 ID를 반환합니다(이전에 빨간색으로 강조 표시됨).

## <a name="api-endpoints"></a>API 엔드포인트

다음 섹션에서는 핵심 Blob 관련 API 엔드포인트 및 해당 기능에 대해 설명합니다.

### <a name="devices"></a>디바이스

디바이스에 Blob을 연결할 수 있습니다. 다음 이미지는 관리 API의 Swagger 참조 설명서를 보여줍니다. Blob 사용량에 대한 디바이스 관련 API 엔드포인트와 이를 전달하는 데 필요한 경로 매개 변수를 지정합니다.

![디바이스 Blob][2]

예를 들어 Blob을 업데이트하거나 만들고 디바이스에 Blob을 연결하려면 다음에 대해 인증된 HTTP PATCH 요청을 만듭니다.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

성공한 요청은 [앞에서 설명한](#blobModel) 대로 JSON 개체를 반환합니다.

### <a name="spaces"></a>공백

Blob을 공간에 연결할 수도 있습니다. 다음 이미지는 Blob 처리를 담당하는 모든 공간 API 엔드포인트를 나열합니다. 이러한 엔드포인트를 전달할 경로 매개 변수도 나열합니다.

![공간 Blob][3]

예를 들어 공간에 연결된 Blob을 반환하려면 다음에 대한 인증된 HTTP GET 요청을 만듭니다.

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

성공한 요청은 [앞에서 설명한](#blobModel) 대로 JSON 개체를 반환합니다.

동일한 엔드포인트로의 PATCH 요청은 메타데이터 설명을 업데이트하고 Blob의 버전을 만듭니다. HTTP 요청은 필요한 메타 및 다중 파트 양식 데이터와 함께 PATCH 메서드를 통해 수행됩니다.

### <a name="users"></a>사용자

Blob을 사용자 모델에 연결할 수 있습니다(예: 프로필 사진 연결). 다음 이미지는 관련 사용자 API 엔드포인트 및 필요한 경로 매개 변수(예: `id`)를 보여줍니다.

![사용자 Blob][4]

예를 들어 사용자에게 연결된 Blob을 가져오려면 필요한 양식 데이터를 사용하여 인증된 HTTP GET 요청을 수행합니다.

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| 매개 변수 | 다음 항목으로 교체 |
| --- | --- |
| *YOUR_BLOB_ID* | 원하는 Blob ID |

성공한 요청은 [앞에서 설명한](#blobModel) 대로 JSON 개체를 반환합니다.

## <a name="common-errors"></a>일반 오류

일반 오류는 올바른 헤더 정보를 제공하지 않는 것과 관련됩니다.

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

이 오류를 해결하려면 전체 요청에 적절한 **Content-type** 헤더가 있는지 확인합니다.

* `multipart/mixed`
* `multipart/form-data`

또한 필요에 따라 각 다중 파트 청크에 해당 **Content-type**이 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- Azure Digital Twins에 대한 Swagger 참조 설명서의 자세한 내용은 [Azure Digital Twins Swagger 사용](how-to-use-swagger.md)을 참조하세요.

- Postman을 통해 Blob을 업로드하려면 [Postman을 구성하는 방법](./how-to-configure-postman.md)을 읽어보세요.

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG