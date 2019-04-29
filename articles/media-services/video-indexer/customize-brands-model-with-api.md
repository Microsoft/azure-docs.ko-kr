---
title: Azure Video Indexer를 사용하여 브랜드 모델 사용자 지정
titlesuffix: Azure Media Services
description: 이 문서에서는 Azure Video Indexer를 사용하여 브랜드 모델을 사용자 지정하는 방법을 보여 줍니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: df77a745ef6508b15b5a8bcde5eede0e06eb1afc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583726"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Video Indexer API를 사용하여 브랜드 모델 사용자 지정

Video Indexer는 비디오 및 오디오 콘텐츠의 인덱싱 및 재인덱싱 동안 연설 및 시각적 텍스트에서 브랜드를 검색하도록 지원합니다. 브랜드 검색 기능은 Bing의 브랜드 데이터베이스에서 제안하는 제품, 서비스 및 회사의 멘션을 식별합니다. 예를 들어 Microsoft가 비디오 또는 오디오 콘텐츠에 멘션되거나, 비디오의 시각적 텍스트에 표시되는 경우 Video Indexer는 콘텐츠에서 해당 항목을 브랜드로 검색합니다. 사용자 지정 브랜드 모델을 사용하면 특정 브랜드가 검색되지 않도록 제외하고, Bing 브랜드 데이터베이스에 없을 수 있으며 모델에 포함되어야 하는 브랜드를 포함할 수 있습니다.

자세한 개요를 보려면 [개요](customize-brands-model-overview.md)를 참조하세요.

Video Indexer API를 사용하여 이 항목에 설명된 것처럼 비디오에서 검색되는 사용자 지정 브랜드 모델을 생성, 사용 및 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-api.md)에 설명된 대로 Video Indexer 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-brand"></a>브랜드 만들기

새 사용자 지정 브랜드를 만들어 지정된 계정의 사용자 지정 브랜드 모델에 추가합니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이러한 매개 변수 외에도 새 브랜드에 대한 정보를 아래 예제의 형식으로 제공하는 요청 본문 JSON 개체를 제공해야 합니다.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

**enabled**를 true로 설정하면 Video Indexer에서 검색할 ‘포함’ 목록에 브랜드가 배치됩니다. **enabled**를 false로 설정하면 ‘제외’ 목록에 브랜드가 배치되므로 Video Indexer에서 검색하지 않습니다.

**referenceUrl** 값은 해당 Wikipedia 페이지의 링크처럼, 브랜드의 참조 웹 사이트일 수 있습니다.

**tags** 값은 브랜드의 태그 목록입니다. 이 값은 Video Indexer 웹 사이트의 브랜드 ‘범주’ 필드에 표시됩니다. 예를 들어 “Azure” 브랜드는 “클라우드”로 태그를 지정하거나 분류할 수 있습니다.

### <a name="response"></a>response

응답은 방금 만든 브랜드에 대한 정보를 아래 예제 형식으로 제공합니다.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>브랜드 삭제

지정된 계정의 사용자 지정 브랜드 모델에서 브랜드를 제거합니다. 계정은 **accountId** 매개 변수에 지정됩니다. 성공적으로 호출하면 브랜드가 더 이상 ‘포함’ 또는 ‘제외’ 브랜드 목록에 없습니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|id|정수|예|브랜드 ID(브랜드를 만들 때 생성됨)|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

브랜드를 성공적으로 삭제한 경우 반환되는 콘텐츠가 없습니다.

## <a name="get-a-specific-brand"></a>특정 브랜드 가져오기

브랜드 ID를 사용하여 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드 세부 정보를 검색할 수 있습니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|id|정수|예|브랜드 ID(브랜드를 만들 때 생성됨)|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 브랜드 ID를 사용하여 검색한 브랜드에 대한 정보를 아래 예제 형식으로 제공합니다.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **enabled**를 **true**로 설정하면 브랜드가 Video Indexer에서 검색할 ‘포함’ 목록에 배치되고, **enabled**를 false로 설정하면 브랜드가 ‘제외’ 목록에 배치되어 Video Indexer에서 검색하지 않습니다.

## <a name="update-a-specific-brand"></a>특정 브랜드 업데이트

브랜드 ID를 사용하여 지정된 계정의 사용자 지정 브랜드 모델에서 브랜드 세부 정보를 검색할 수 있습니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|id|정수|예|브랜드 ID(브랜드를 만들 때 생성됨)|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이러한 매개 변수 외에도 업데이트할 브랜드에 대한 업데이트된 정보를 아래 예제의 형식으로 제공하는 요청 본문 JSON 개체를 제공해야 합니다.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> 이 예제에서는 **브랜드 만들기** 섹션에서 예제 요청 본문에 만든 브랜드가 새 태그 및 새 설명으로 업데이트됩니다. 또한 ‘제외’ 목록에 배치하기 위해 **enabled** 값이 false로 변경되었습니다.

### <a name="response"></a>response

응답은 업데이트한 브랜드에 대한 업데이트된 정보를 아래 예제 형식으로 제공합니다.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>모든 브랜드 가져오기

브랜드가 ‘포함’ 또는 ‘제외’ 브랜드 목록에 배치되는지에 관계없이 지정된 계정의 사용자 지정 브랜드 모델에 있는 모든 브랜드를 반환합니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 계정의 모든 브랜드 목록과 각 세부 정보를 아래 예제의 형식으로 제공합니다.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> *Example* 브랜드는 Video Indexer에서 검색할 ‘포함’ 목록에 있고, *Example2* 브랜드는 ‘제외’ 목록에 있으므로 Video Indexer에서 검색하지 않습니다.

## <a name="get-brands-model-settings"></a>브랜드 모델 설정 가져오기

지정된 계정의 브랜드 모델 설정을 반환합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드가 사용되지 않는 경우, Video Indexer는 지정된 계정의 사용자 지정 브랜드 모델에서만 브랜드를 검색합니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 Bing 브랜드의 사용 여부를 아래 예제 형식으로 표시합니다.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn**을 true로 설정하면 Bing 브랜드가 사용됩니다. *useBuiltin*이 false이면 Bing 브랜드가 사용되지 않습니다. **state** 값은 사용되지 않으므로 무시해도 됩니다.

## <a name="update-brands-model-settings"></a>브랜드 모델 설정 업데이트

지정된 계정의 브랜드 모델 설정을 업데이트합니다. 브랜드 모델 설정은 Bing 브랜드 데이터베이스에서 검색을 사용할지 여부를 나타냅니다. Bing 브랜드가 사용되지 않는 경우, Video Indexer는 지정된 계정의 사용자 지정 브랜드 모델에서만 브랜드를 검색합니다.

### <a name="request-url"></a>요청 URL:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이러한 매개 변수 외에도 새 브랜드에 대한 정보를 아래 예제의 형식으로 제공하는 요청 본문 JSON 개체를 제공해야 합니다.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn**을 true로 설정하면 Bing 브랜드가 사용됩니다. *useBuiltin*이 false이면 Bing 브랜드가 사용되지 않습니다.

### <a name="response"></a>response

브랜드 모델 설정을 성공적으로 업데이트한 경우 반환되는 콘텐츠가 없습니다.

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 브랜드 모델 사용자 지정](customize-brands-model-with-website.md)
