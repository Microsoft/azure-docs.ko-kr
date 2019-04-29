---
title: Video Indexer API를 사용하여 언어 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer API를 사용하여 언어 모델을 사용자 지정하는 방법을 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: ca1e66d20b19c1a5b85a4f4ff1c433331116bee7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553745"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Video Indexer API로 언어 모델 사용자 지정

Video Indexer를 사용하면 엔진을 적응시킬 어휘가 포함된 도메인의 텍스트인 적응 텍스트를 업로드하여 음성 인식을 사용자 지정하기 위한 사용자 지정 언어 모델을 만들 수 있습니다. 모델을 학습하면 적응 텍스트에 나타나는 새 단어가 인식됩니다. 

사용자 지정 언어 모델의 자세한 개요와 모범 사례에 대해서는 [Video Indexer로 언어 모델 사용자 지정](customize-language-model-overview.md)을 참조하세요.

이 항목에 설명된 대로 Video Indexer API를 사용하여 계정에서 사용자 지정 언어 모델을 만들고 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)에 설명된 대로 웹 사이트를 사용할 수도 있습니다.

## <a name="create-a-language-model"></a>언어 모델 만들기

다음 명령은 지정된 계정에 새 사용자 지정 언어 모델을 만듭니다. 이 호출에서 언어 모델에 대한 파일을 업로드할 수 있습니다. 또는 여기에서 언어 모델을 만들고, 나중에 해당 언어 모델을 업데이트하여 모델에 대한 파일을 업로드할 수 있습니다.

> [!NOTE]
> 모델이 파일 내용을 파악하려면 활성화된 파일을 사용하여 모델을 학습해야 합니다. 언어 학습 지침은 다음 섹션에 나와 있습니다.

### <a name="request-url"></a>요청 URL

POST 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|
|modelName|문자열|예|언어 모델의 이름입니다.|
|언어|문자열|예|언어 모델의 언어입니다. <br/>**language** 매개 변수에는 BCP-47 형식 'language tag-region'(예: 'en-US')으로 언어를 지정해야 합니다. 지원되는 언어는 영어(en-US), 독일어(de-DE), 스페인어(es-SP), 아랍어(ar-EG), 프랑스어(fr-FR), 힌두어(hi-HI), 이탈리아어(it-IT), 일본어(ja-JP), 포르투갈어(pt-BR), 러시아어(ru-RU) 및 중국어(zh-CN)입니다.  |

### <a name="request-body"></a>요청 본문

언어 모델에 추가할 파일을 업로드하려면 위의 필수 매개 변수 값을 제공하는 것 외에도 폼 데이터를 사용하여 본문에 파일을 업로드해야 합니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다. 

1. 키는 파일 이름이고 값은 txt 파일입니다.
2. 키는 파일 이름이고 값은 txt 파일의 URL입니다.

### <a name="response"></a>response

응답은 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 함께, 새로 만든 언어 모델의 메타데이터를 제공합니다.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>언어 모델 학습

다음 명령은 언어 모델에서 업로드되고 사용하도록 설정된 파일의 내용을 사용하여 지정된 계정의 사용자 지정 언어 모델을 학습합니다. 

> [!NOTE]
> 먼저 언어 모델을 생성한 후 해당 파일을 업로드해야 합니다. 언어 모델을 만들거나 언어 모델을 업데이트할 때 파일을 업로드할 수 있습니다. 

### <a name="request-url"></a>요청 URL

PUT 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 함께, 새로 학습한 언어 모델의 메타데이터를 제공합니다.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

그런 후 [인덱스에 비디오를 업로드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)할 때 **linguisticModelId** 매개 변수에 대해, [비디오를 다시 인덱싱](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)할 때는 **languageModelId** 매개 변수에 대해 언어 모델의 **id** 값을 사용합니다.

## <a name="delete-a-language-model"></a>언어 모델 삭제

다음 명령은 지정된 계정에서 사용자 지정 언어 모델을 삭제합니다. 삭제된 언어 모델을 사용하던 모든 비디오는 비디오를 다시 인덱싱할 때까지 동일한 인덱스를 유지합니다. 비디오를 다시 인덱싱하는 경우 비디오에 새 언어 모델을 할당할 수 있습니다. 그렇지 않으면 Video Indexer는 기본 모델을 사용하여 비디오를 다시 인덱싱합니다.

### <a name="request-url"></a>요청 URL

DELETE 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

언어 모델을 성공적으로 삭제할 경우 반환되는 콘텐츠가 없습니다.

## <a name="update-a-language-model"></a>언어 모델 업데이트

다음 명령은 지정된 계정에서 사용자 지정 언어 모델을 업데이트합니다.

> [!NOTE]
> 언어 모델을 미리 만들었어야 합니다. 이 호출을 사용하여 해당 모델 내의 모든 파일을 사용하거나 사용하지 않도록 설정하고, 언어 모델의 이름을 업데이트하고, 언어 모델에 추가할 파일을 업로드할 수 있습니다.

### <a name="request-url"></a>요청 URL

PUT 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

다음은 Curl 요청입니다.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|
|modelName|문자열|아닙니다.|모델에 제공할 수 있는 새 이름입니다.|
|enable|부울|아닙니다.|이 모델의 모든 파일을 사용하도록 설정할지(true) 또는 사용하지 않도록 설정할지(false)를 선택합니다.|

### <a name="request-body"></a>요청 본문

언어 모델에 추가할 파일을 업로드하려면 위의 필수 매개 변수 값을 제공하는 것 외에도 폼 데이터를 사용하여 본문에 파일을 업로드해야 합니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다. 

1. 키는 파일 이름이고 값은 txt 파일입니다.
2. 키는 파일 이름이고 값은 txt 파일의 URL입니다.

### <a name="response"></a>response

응답은 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 함께, 새로 학습한 언어 모델의 메타데이터를 제공합니다.

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
여기에서 반환된 파일의 **id**를 사용하여 파일 내용을 다운로드할 수 있습니다.

## <a name="update-a-file-from-a-language-model"></a>언어 모델에서 파일 업데이트

다음 명령을 사용하면 지정된 계정의 사용자 지정 언어 모델에서 파일의 이름을 업데이트하고 상태를 **사용하도록 설정**할 수 있습니다.

### <a name="request-url"></a>요청 URL

PUT 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

다음은 Curl 요청입니다.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|파일을 포함하는 언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|fileId|문자열|예|업데이트하려는 파일의 ID(언어 모델을 만들거나 업데이트할 때 파일을 업로드하는 동안 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|
|fileName|문자열|아닙니다.|파일 이름을 업데이트할 새 이름입니다.|
|enable|부울|아닙니다.|언어 모델에서 이 파일이 사용 가능으로 설정되어 있는지(true) 또는 사용 불가능으로 설정되어 있는지(false)를 업데이트합니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 아래의 예제 JSON 출력 형식에 따라 업데이트한 파일의 메타데이터를 제공합니다.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
여기에서 반환된 파일의 **id**를 사용하여 파일 내용을 다운로드할 수 있습니다.

## <a name="get-a-specific-language-model"></a>특정 언어 모델 가져오기

다음 명령은 언어 모델에 있는 언어 및 파일과 같은 지정된 계정의 지정된 언어 모델에 대한 정보를 반환합니다. 

### <a name="request-url"></a>요청 URL

GET 요청입니다.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get)합니다.

### <a name="request-parameters-and-request-body"></a>매개 변수 및 요청 본문 요청

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 다음 예제 JSON 출력 형식에 따라 각 모델 파일의 메타데이터와 함께, 지정된 언어 모델의 메타데이터를 제공합니다.

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

여기에서 반환된 파일의 **id**를 사용하여 파일 내용을 다운로드할 수 있습니다.

## <a name="get-all-the-language-models"></a>모든 언어 모델 가져오기

다음 명령은 지정된 계정의 모든 사용자 지정 언어 모델을 목록으로 반환합니다.

### <a name="request-url"></a>요청 URL

GET 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 아래의 예제 JSON 출력 형식에 따라 계정의 모든 언어 모델 목록과 해당 메타데이터 및 파일 각각을 제공합니다.

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>언어 모델에서 파일 삭제

다음 명령은 지정된 계정의 지정된 언어 모델에서 지정된 파일을 삭제합니다. 

### <a name="request-url"></a>요청 URL

DELETE 요청입니다.
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|파일을 포함하는 언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|fileId|문자열|예|업데이트하려는 파일의 ID(언어 모델을 만들거나 업데이트할 때 파일을 업로드하는 동안 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

파일을 언어 모델에서 성공적으로 삭제할 경우 반환되는 콘텐츠가 없습니다.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>언어 모델에서 파일의 메타데이터 가져오기

이 경우 계정의 선택한 언어 모델에서 지정된 파일의 내용 및 메타데이터가 반환됩니다.

### <a name="request-url"></a>요청 URL

GET 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

다음은 Curl 요청입니다.
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|파일을 포함하는 언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|fileId|문자열|예|업데이트하려는 파일의 ID(언어 모델을 만들거나 업데이트할 때 파일을 업로드하는 동안 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 다음과 비슷하게 JSON 형식으로 파일 내용 및 메타데이터를 제공합니다.

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> 이 예제 파일의 내용은 별도의 두 줄에 단어 "hello"와 "world"를 포함합니다.

## <a name="download-a-file-from-a-language-model"></a>언어 모델에서 파일 다운로드

다음 명령은 지정된 계정의 지정된 언어 모델에서 지정된 파일의 내용을 포함하는 텍스트 파일을 다운로드합니다. 이 텍스트 파일은 원래 업로드한 텍스트 파일의 내용과 일치해야 합니다.

### <a name="request-url"></a>요청 URL
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountID|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|modelId|문자열|예|파일을 포함하는 언어 모델의 ID(언어 모델을 만들 때 생성)입니다.|
|fileId|문자열|예|업데이트하려는 파일의 ID(언어 모델을 만들거나 업데이트할 때 파일을 업로드하는 동안 생성)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문 

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 JSON 형식의 파일 내용을 포함하는 텍스트 파일의 다운로드입니다. 

## <a name="next-steps"></a>다음 단계

[웹 사이트를 사용하여 언어 모델 사용자 지정](customize-language-model-with-website.md)
