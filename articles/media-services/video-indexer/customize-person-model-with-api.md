---
title: Video Indexer API를 사용하여 개인 모델 사용자 지정 - Azure
titlesuffix: Azure Media Services
description: 이 문서에서는 Video Indexer API를 사용하여 개인 모델을 사용자 지정하는 방법을 설명합니다.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553711"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Video Indexer API를 사용하여 개인 모델 사용자 지정

Video Indexer는 비디오 콘텐츠에 대해 얼굴 감지 및 유명인 인식 기능을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. 유명인 인식 기능으로 인식되지 않는 얼굴은 감지되지만 이름 없이 유지됩니다. Video Indexer에 비디오를 업로드하고 결과를 얻은 후 돌아가서 인식되지 않은 얼굴에 이름을 지정할 수 있습니다. 얼굴에 이름 레이블을 지정하면 얼굴과 이름이 계정의 개인 모델에 추가됩니다. 그러면 Video Indexer가 향후 비디오 및 이전 비디오에서 이 얼굴을 인식합니다.

Video Indexer API를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. [Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)에 설명된 대로 Video Indexer 웹 사이트를 사용할 수도 있습니다.

## <a name="managing-multiple-person-models"></a>여러 개의 개인 모델 관리 

Video Indexer는 계정당 여러 개의 개인 모델을 지원합니다. 이 기능은 현재 Video Indexer API를 통해서만 사용할 수 있습니다.

계정이 다른 사용 사례 시나리오를 지원하는 경우 계정당 여러 개의 개인 모델을 만드는 것이 좋습니다. 예를 들어 콘텐츠가 스포츠와 관련된 경우 각 스포츠(미식 축구, 농구, 축구 등)에 대해 별도의 개인 모델을 만들 수 있습니다. 

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오에 대해 새로운 얼굴을 학습하면 비디오와 연결된 특정 사용자 지정 모델이 업데이트됩니다.

각 계정이 50개의 개인 모델로 제한됩니다. 다중 개인 모델 지원이 필요하지 않은 경우, 업로드/인덱싱하거나 다시 인덱싱할 때 비디오에 개인 모델 ID를 할당하지 마세요. 이 경우 Video Indexer는 사용자 계정의 기본 사용자 지정 개인 모델을 사용합니다.

## <a name="create-a-new-person-model"></a>새 개인 모델 만들기

지정된 계정에 새 개인 모델을 만듭니다. 

### <a name="request-url"></a>요청 URL

POST 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)합니다.

### <a name="request-parameters"></a>요청 매개 변수 

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|이름|문자열|예|개인 모델의 이름입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 방금 만든 개인 모델의 이름 및 생성된 모델 ID를 아래 예제 형식으로 제공합니다.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

그런 다음, [인덱싱할 비디오를 업로드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)하거나 [비디오를 다시 인덱싱](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)할 때 **personModelId** 매개 변수의 **ID** 값을 사용해야 합니다.

## <a name="delete-a-person-model"></a>개인 모델 삭제

지정된 계정에서 사용자 지정 개인 모델을 삭제합니다. 

개인 모델을 성공적으로 삭제한 후에도 삭제된 모델을 사용 중인 현재 비디오의 인덱스는 다시 인덱싱할 때까지 변경되지 않습니다. 다시 인덱싱하면 Video Indexer는 해당 모델을 사용하여 인덱싱된 현재 비디오에서 삭제된 모델에 이름이 지정된 얼굴을 인식하지 못하지만 이러한 얼굴은 계속 감지됩니다. 삭제된 모델을 사용하여 인덱싱된 현재 비디오는 이제 계정의 기본 개인 모델을 사용합니다. 삭제된 모델의 얼굴이 계정의 기본 모델에서도 이름이 지정된 경우에는 해당 얼굴이 비디오에서 계속 인식됩니다.

### <a name="request-url"></a>요청 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

다음은 Curl 요청입니다.
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|id|문자열|예|개인 모델 ID(개인 모델을 만들 때 생성됨)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

개인 모델을 성공적으로 삭제할 경우 반환되는 콘텐츠가 없습니다.

## <a name="get-all-person-models"></a>모든 개인 모델 가져오기

지정된 계정의 모든 개인 모델을 가져옵니다. 

### <a name="request-call"></a>요청 호출

GET 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

다음은 Curl 요청입니다.

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

응답은 사용자 계정에 있는 모든 개인 모델(지정된 계정의 기본 개인 모델 포함)의 목록과 각 이름 및 ID를 아래 예제의 형식으로 제공합니다.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

[인덱싱할 비디오를 업로드](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)하거나 [비디오를 다시 인덱싱](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)할 때 **personModelId** 매개 변수에 대한 개인 모델의 **ID** 값을 사용하여 비디오에 사용할 모델을 선택할 수 있습니다.

## <a name="update-a-face"></a>얼굴 업데이트

이 명령을 사용하면 비디오 ID와 얼굴 ID를 사용하는 이름으로 비디오의 얼굴을 업데이트할 수 있습니다. 그러면 업로드/인덱싱하거나 다시 인덱싱할 때 비디오와 연결된 개인 모델이 업데이트됩니다. 개인 모델이 할당되지 않은 경우 계정의 기본 개인 모델이 업데이트됩니다. 

이 경우 동일한 개인 모델을 공유하는 다른 현재 비디오에서 동일한 얼굴의 발생이 인식됩니다. 다른 현재 비디오의 얼굴 인식은 배치 프로세스이므로 적용되는 데 다소 시간이 걸릴 수 있습니다.

Video Indexer에서 유명인으로 인식된 얼굴을 새 이름으로 업데이트할 수 있습니다. 사용자가 지정한 새 이름이 기본 제공 유명인 인식보다 우선 적용됩니다.

### <a name="request-call"></a>요청 호출

POST 요청입니다.

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

다음은 Curl 요청입니다.

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[필수 매개 변수를 참조하고 Video Indexer 개발자 포털을 사용하여 테스트](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)합니다.

### <a name="request-parameters"></a>요청 매개 변수

|**Name**|**형식**|**필수**|**설명**|
|---|---|---|---|
|location|문자열|예|호출을 라우팅할 Azure 지역입니다. 자세한 내용은 [Azure 지역 및 Video Indexer](regions.md)를 참조하세요.|
|accountId|문자열|예|계정의 GUID(Globally Unique Identifier)입니다.|
|videoId|문자열|예|업데이트할 얼굴이 표시되는 비디오의 ID입니다. 비디오를 업로드하고 인덱싱할 때 생성됩니다.|
|faceId|정수|예|업데이트할 얼굴의 ID입니다. 비디오 인덱스에서 faceId를 가져올 수 있습니다.|
|accessToken|문자열|예|호출에 대해 인증할 액세스 토큰([계정 액세스 토큰](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?) 범위여야 함)입니다. 액세스 토큰은 1시간 내에 만료됩니다.|
|이름|문자열|예|얼굴을 업데이트할 새 이름입니다.|

이름은 개인 모델에서 고유하므로 동일한 개인 모델에 있는 두 개의 다른 얼굴에 동일한 **name** 매개 변수 값을 제공하는 경우 Video Indexer는 얼굴을 동일한 개인으로 보고, 비디오가 다시 인덱싱될 때 통합합니다. 

### <a name="request-body"></a>요청 본문

이 호출에 필요한 추가 요청 본문은 없습니다.

### <a name="response"></a>response

얼굴을 성공적으로 업데이트한 경우 반환되는 콘텐츠가 없습니다.

## <a name="next-steps"></a>다음 단계

[Video Indexer 웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-website.md)
