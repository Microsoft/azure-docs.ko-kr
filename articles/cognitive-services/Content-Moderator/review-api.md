---
title: 검토, 워크플로 및 작업 개념-Content Moderator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 검토 도구의 핵심 개념에 대해 설명 합니다. 검토, 워크플로 및 작업.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 1aba86efb9ea76fbf060e80b47f9f2f6cdf8ee71
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73744414"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>콘텐츠 중재 검토, 워크플로 및 작업

Content Moderator은 기계 지원 조정을 사용자의 루프 기능과 결합 하 여 실제 시나리오에 대 한 최적의 중재 프로세스를 만듭니다. 클라우드 기반 [검토 도구](https://contentmoderator.cognitive.microsoft.com)를 통해이를 수행 합니다. 이 가이드에서는 검토 도구, 워크플로 및 작업의 핵심 개념에 대해 알아봅니다.

## <a name="reviews"></a>검토

검토에서 콘텐츠는 검토 도구에 업로드 되 고 **검토** 탭 아래에 나타납니다. 여기에서 사용자는 적용 된 태그를 변경 하 고 적절 하 게 사용자 지정 태그를 적용할 수 있습니다. 사용자가 검토를 제출 하면 결과가 지정 된 콜백 엔드포인트으로 전송 되 고 사이트에서 콘텐츠가 제거 됩니다.

![검토 도구 웹 사이트를 브라우저의 검토 탭에서 엽니다.](./Review-Tool-user-Guide/images/image-workflow-review.png)

[검토 도구 가이드](./review-tool-user-guide/review-moderated-images.md) 를 참조 하 여 검토 만들기를 시작 하거나 [REST API 가이드](./try-review-api-review.md) 를 참조 하 여 프로그래밍 방식으로 작업을 수행 하는 방법을 알아보세요.

## <a name="workflows"></a>워크플로

워크플로는 콘텐츠에 대 한 클라우드 기반의 사용자 지정 필터입니다. 워크플로는 다양 한 서비스에 연결 하 여 다양 한 방법으로 콘텐츠를 필터링 한 다음 적절 한 조치를 취할 수 있습니다. Content Moderator 커넥터를 사용 하 여 워크플로는 자동으로 중재 태그를 적용 하 고 제출 된 콘텐츠로 리뷰를 만들 수 있습니다.

### <a name="view-workflows"></a>워크플로 보기

기존 워크플로를 보려면 [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 로 이동 하 고 **설정** > **워크플로**를 선택 합니다.

![기본 워크플로](images/default-workflow-listed.PNG)

워크플로는 JSON 문자열로 완전히 설명할 수 있으며,이를 통해 프로그래밍 방식으로 액세스할 수 있습니다. 워크플로에 대해 **편집** 옵션을 선택한 다음 **json** 탭을 선택 하면 다음과 같은 json 식이 표시 됩니다.

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

워크플로 만들기 및 사용을 시작 하려면 [검토 도구 가이드](./review-tool-user-guide/workflows.md) 를 참조 하거나 프로그래밍 방식으로 작업을 수행 하는 방법을 알아보려면 [REST API 가이드](./try-review-api-workflow.md) 를 참조 하세요.

## <a name="jobs"></a>작업

중재 작업은 콘텐츠 조정, 워크플로 및 검토 기능을 위한 일종의 래퍼 역할을 합니다. 작업은 Content Moderator 이미지 조정 API 또는 텍스트 중재 API를 사용 하 여 콘텐츠를 검색 한 다음 지정 된 워크플로를 사용 하 여 확인 합니다. 워크플로 결과에 따라 [검토 도구](./review-tool-user-guide/human-in-the-loop.md)에서 콘텐츠에 대 한 검토가 생성 되거나 생성 되지 않을 수 있습니다. 검토와 워크플로를 각각의 해당 Api를 사용 하 여 만들고 구성할 수 있지만 작업 API를 사용 하면 지정 된 콜백 엔드포인트에 보낼 수 있는 전체 프로세스에 대 한 자세한 보고서를 가져올 수 있습니다.

작업 사용을 시작 하려면 [REST API 가이드](./try-review-api-job.md) 를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [작업 API 콘솔](try-review-api-job.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [작업 .NET 빠른 시작](moderation-jobs-quickstart-dotnet.md)을 확인하세요. 
* 검토의 경우 [검토 API 콘솔](try-review-api-review.md)로 시작하고 REST API 코드 샘플을 사용합니다. 그런 다음 [.net 빠른](dotnet-sdk-quickstart.md)시작의 검토 섹션을 참조 하세요.
* 비디오 검토의 경우 [비디오 검토 빠른 시작](video-reviews-quickstart-dotnet.md)을 사용하여 [비디오 검토에 전사를 추가](video-transcript-reviews-quickstart-dotnet.md)하는 방법을 알아봅니다.
