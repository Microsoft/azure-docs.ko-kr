---
title: REST API 콘솔에서 중재 작업 사용-Content Moderator
titleSuffix: Azure Cognitive Services
description: Review API의 작업 운영을 사용하여 Azure Content Moderator에서 이미지 또는 텍스트 콘텐츠에 대한 엔드투엔드 콘텐츠 조정 작업을 시작할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: pafarley
ms.openlocfilehash: 83ee8e0c0583cba72da8702e196f0f38128f8d8a
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935964"
---
# <a name="define-and-use-moderation-jobs-rest"></a>중재 작업 정의 및 사용 (REST)

중재 작업은 콘텐츠 조정, 워크플로 및 검토 기능을 위한 일종의 래퍼 역할을 합니다. 이 가이드에서는 작업 REST Api를 사용 하 여 콘텐츠 중재 작업을 시작 및 확인 하는 방법을 보여 줍니다. Api의 구조를 이해 하면 이러한 호출을 모든 REST 호환 플랫폼으로 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만드세요.
- 필드 작업과 함께 사용할 [사용자 지정 워크플로를 정의 합니다](./Review-Tool-User-Guide/Workflows.md) . 기본 워크플로를 사용할 수도 있습니다.

## <a name="create-a-job"></a>작업 만들기

중재 작업을 만들려면 [작업-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) API 참조 만들기 페이지로 이동 하 여 구독 영역에 대 한 단추를 선택 합니다 .이는 [검토 도구의](https://contentmoderator.cognitive.microsoft.com/) **자격 증명** 페이지에 있는 엔드포인트 URL에서 찾을 수 있습니다. 이렇게 하면 REST API 호출을 쉽게 구성 하 고 실행할 수 있는 API 콘솔이 시작 됩니다.

![작업-페이지 영역 선택 영역 만들기](images/test-drive-job-1.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

REST 호출을 생성 하려면 다음 값을 입력 합니다.

- **Teamname**: 검토 [도구](https://contentmoderator.cognitive.microsoft.com/) 계정 (검토 도구의 자격 증명 화면에서 **ID** 필드에 있음)을 설정할 때 만든 팀 ID입니다.
- **ContentType**: "Image", "Text" 또는 "Video" 일 수 있습니다.
- **ContentId**: 사용자 지정 식별자 문자열입니다. 이 문자열은 API로 전달되고 콜백을 통해 반환됩니다. 내부 식별자 또는 메타 데이터를 중재 작업의 결과와 연결 하는 데 유용 합니다.
- **Workflowname**: 이전에 만든 워크플로의 이름 (또는 기본 워크플로의 경우 "기본값")입니다.
- **Callbackendpoint**: (선택 사항) 검토가 완료 되 면 콜백 정보를 수신 하는 URL입니다.
- **Ocp-Apim-Subscription-키**: Content Moderator 키입니다. 이는 [검토 도구의](https://contentmoderator.cognitive.microsoft.com) **설정** 탭에서 찾을 수 있습니다.

### <a name="fill-in-the-request-body"></a>요청 본문을 입력 합니다.

REST 호출의 본문은 **Contentvalue**필드 하나를 포함 합니다. 텍스트를 중재 경우 원시 텍스트 내용에 붙여넣거나 이미지/비디오를 중재 하는 경우 이미지 또는 비디오 URL을 입력 합니다. 다음 샘플 이미지 URL을 사용할 수 있습니다. [https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)

![작업 - 콘솔 쿼리 매개 변수, 헤더 및 요청 본문 상자 만들기](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>요청 제출

**보내기**를 선택합니다. 작업에 성공 하면 **응답 상태가** `200 OK`이 고 **응답 콘텐츠** 상자에 작업에 대 한 ID가 표시 됩니다. 다음 단계에서 사용할 수 있도록 ID를 복사합니다.

![검토 - 콘솔 만들기 응답 콘텐츠 상자에 검토 ID 표시](images/test-drive-job-3.PNG)

## <a name="get-job-status"></a>작업 상태 가져오기

실행 중이거나 완료 된 작업의 상태 및 세부 정보를 가져오려면 [작업-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c3) API 참조 가져오기 페이지로 이동 하 여 해당 지역의 단추 (키가 관리 되는 지역)를 선택 합니다.

![작업-지역 선택 가져오기](images/test-drive-region.png)

위의 섹션과 같이 REST 호출 매개 변수를 입력 합니다. 이 단계에서 **JobId** 는 작업을 만들 때 받은 고유 ID 문자열입니다. **보내기**를 선택합니다. 작업에 성공 하면 **응답 상태** 는 `200 OK`이 고 **응답 콘텐츠** 상자는 다음과 같이 작업을 JSON 형식으로 표시 합니다.

```json
{  
  "Id":"2018014caceddebfe9446fab29056fd8d31ffe",
  "TeamName":"some team name",
  "Status":"Complete",
  "WorkflowId":"OCR",
  "Type":"Image",
  "CallBackEndpoint":"",
  "ReviewId":"201801i28fc0f7cbf424447846e509af853ea54",
  "ResultMetaData":[  
    {  
      "Key":"hasText",
      "Value":"True"
    },
    {  
      "Key":"ocrText",
      "Value":"IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
    }
  ],
  "JobExecutionReport":[  
    {  
      "Ts":"2018-01-07T00:38:29.3238715",
      "Msg":"Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
    },
    {  
      "Ts":"2018-01-07T00:38:29.2928416",
      "Msg":"Job marked completed and job content has been removed"
    },
    {  
      "Ts":"2018-01-07T00:38:29.0856472",
      "Msg":"Execution Complete"
    },
    {  
      "Ts":"2018-01-07T00:38:26.7714671",
      "Msg":"Successfully got hasText response from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:26.4181346",
      "Msg":"Getting hasText from Moderator"
    },
    {  
      "Ts":"2018-01-07T00:38:25.5122828",
      "Msg":"Starting Execution - Try 1"
    }
  ]
}
```

![작업-REST 호출 응답 가져오기](images/test-drive-job-5.png)

### <a name="examine-the-new-reviews"></a>새 검토를 검토 합니다.

콘텐츠 작업에서 검토를 만든 경우 [검토 도구](https://contentmoderator.cognitive.microsoft.com)에서 볼 수 있습니다. 사용 하는 내용에 따라  > **이미지** /**텍스트** /**비디오** 를 **검토** 합니다. 콘텐츠는 사람이 검토할 준비가 된 것으로 표시 되어야 합니다. 사용자 중재자가 자동 할당 된 태그 및 예측 데이터를 검토 하 고 최종 중재 결정을 제출한 후에는 작업 API가이 모든 정보를 지정 된 콜백 엔드포인트 엔드포인트으로 전송 합니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 REST API를 사용 하 여 콘텐츠 중재 작업을 만들고 쿼리 하는 방법을 배웠습니다. 그런 다음, 작업을 [전자 상거래 중재](./ecommerce-retail-catalog-moderation.md) 자습서와 같은 종단 간 조정 시나리오에 통합 합니다.