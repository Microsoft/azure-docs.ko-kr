---
title: REST API 콘솔을 사용 하 여 중재 검토 만들기-Content Moderator
titleSuffix: Azure Cognitive Services
description: Azure Content Moderator 검토 Api를 사용 하 여 휴먼 중재를 위한 이미지 또는 텍스트 검토를 만듭니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: a9726e41a84926d00d48b51e31f534a3d8c2fe0c
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757134"
---
# <a name="create-human-reviews-rest"></a>사용자 리뷰 만들기 (REST)

[평가를](./review-api.md#reviews) 위해 인적 중재자의 콘텐츠를 저장 하 고 표시 합니다. 사용자가 검토를 완료 하면 결과가 지정 된 콜백 엔드포인트으로 전송 됩니다. 이 가이드에서는 API 콘솔을 통해 REST Api 검토를 사용 하 여 리뷰를 설정 하는 방법을 알아봅니다. Api의 구조를 이해 하면 이러한 호출을 모든 REST 호환 플랫폼으로 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만드세요.

## <a name="create-a-review"></a>검토 만들기

검토를 만들려면 검토-API 참조 **[만들기](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** 페이지로 이동 하 고 키 영역에 대 한 단추를 선택 합니다 .이는 [검토 도구의](https://contentmoderator.cognitive.microsoft.com/) **자격 증명** 페이지에 있는 엔드포인트 URL에서 찾을 수 있습니다. 이렇게 하면 REST API 호출을 쉽게 구성 하 고 실행할 수 있는 API 콘솔이 시작 됩니다.

![검토-지역 선택 가져오기](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

**Teamname**및 **Ocp-Apim-Subscription 키**에 대 한 값을 입력 합니다.

- **Teamname**: 검토 [도구](https://contentmoderator.cognitive.microsoft.com/) 계정 (검토 도구의 자격 증명 화면에서 **ID** 필드에 있음)을 설정할 때 만든 팀 ID입니다.
- **Ocp-Apim-Subscription-키**: Content Moderator 키입니다. 이는 [검토 도구의](https://contentmoderator.cognitive.microsoft.com) **설정** 탭에서 찾을 수 있습니다.

### <a name="enter-a-review-definition"></a>검토 정의 입력

**요청 본문** 상자를 편집 하 여 다음 필드를 포함 하는 JSON 요청을 입력 합니다.

- **메타 데이터**: 콜백 엔드포인트에 반환할 사용자 지정 키-값 쌍입니다. 키가 [검토 도구](https://contentmoderator.cognitive.microsoft.com)에 정의 된 짧은 코드 이면 태그로 표시 됩니다.
- **콘텐츠**: 이미지 및 비디오 콘텐츠의 경우 콘텐츠를 가리키는 URL 문자열입니다. 텍스트 콘텐츠의 경우 실제 텍스트 문자열입니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 내부 식별자 또는 메타 데이터를 중재 작업의 결과와 연결 하는 데 유용 합니다.
- **Callbackendpoint**: (선택 사항) 검토가 완료 되 면 콜백 정보를 수신 하는 URL입니다.

기본 요청 본문은 만들 수 있는 다양 한 유형의 검토 예를 보여 줍니다.

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>요청 제출
  
**보내기**를 선택합니다. 작업에 성공 하면 **응답 상태** 는 `200 OK`이 고 **응답 콘텐츠** 상자에는 검토에 대 한 ID가 표시 됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>새 검토를 검토 합니다.

[검토 도구](https://contentmoderator.cognitive.microsoft.com)에서 사용 하는 내용에 **따라  > ** **이미지** /**텍스트** /**비디오** 를 선택 합니다. 업로드 한 콘텐츠는 사용자가 검토할 준비가 된 상태로 표시 되어야 합니다.

![축구공 이미지 검토 도구](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>검토 세부 정보 가져오기

기존 검토에 대 한 세부 정보를 검색 하려면 [검토-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API 참조 가져오기 페이지로 이동 하 여 해당 지역의 단추 (키가 관리 되는 지역)를 선택 합니다.

![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션과 같이 REST 호출 매개 변수를 입력 합니다. 이 단계에서 **reviewId** 은 검토를 만들 때 받은 고유 ID 문자열입니다.

![검토 - 콘솔 만들기 결과 가져오기](images/test-drive-review-3.PNG)
  
**보내기**를 선택합니다. 작업에 성공 하면 **응답 상태** 는 `200 OK`이 고 **응답 콘텐츠** 상자는 다음과 같이 JSON 형식으로 검토 세부 정보를 표시 합니다.

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

응답에서 다음 필드를 기록해 둡니다.

- **status**
- **reviewerResultTags**: 인간 리뷰 팀에서 수동으로 추가 된 태그 ( **createdBy** 필드 표시)가 있는 경우 표시 됩니다.
- **메타 데이터**: 사용자 검토 팀이 변경 내용을 적용 하기 전에 검토에서 처음 추가 된 태그를 보여 줍니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 REST API를 사용 하 여 콘텐츠 중재 검토를 만드는 방법을 알아보았습니다. 그런 다음 리뷰를 [전자 상거래 중재](./ecommerce-retail-catalog-moderation.md) 자습서와 같은 종단 간 조정 시나리오에 통합 합니다.