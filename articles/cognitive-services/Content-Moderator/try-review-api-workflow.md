---
title: REST API 콘솔을 사용 하 여 중재 워크플로 정의-Content Moderator
titleSuffix: Azure Cognitive Services
description: Azure Content Moderator 검토 Api를 사용 하 여 콘텐츠 정책을 기반으로 사용자 지정 워크플로 및 임계값을 정의할 수 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754177"
---
# <a name="define-and-use-moderation-workflows-rest"></a>중재 워크플로 정의 및 사용 (REST)

워크플로는 콘텐츠를 더 효율적으로 처리 하는 데 사용할 수 있는 클라우드 기반의 사용자 지정 필터입니다. 워크플로는 다양 한 서비스에 연결 하 여 다양 한 방법으로 콘텐츠를 필터링 한 다음 적절 한 조치를 취할 수 있습니다. 이 가이드에서는 API 콘솔을 통해 워크플로 REST Api를 사용 하 여 워크플로를 만들고 사용 하는 방법을 보여 줍니다. Api의 구조를 이해 하면 이러한 호출을 모든 REST 호환 플랫폼으로 쉽게 이식할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

- Content Moderator [검토 도구](https://contentmoderator.cognitive.microsoft.com/) 사이트에서 로그인 하거나 계정을 만드세요.

## <a name="create-a-workflow"></a>워크플로 만들기

워크플로를 만들거나 업데이트 하려면 **[워크플로-API 참조 만들기 또는 업데이트](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** 페이지로 이동 하 고 키 영역에 대 한 단추를 선택 합니다 .이는 [검토 도구의](https://contentmoderator.cognitive.microsoft.com/) **자격 증명** 페이지에 있는 엔드포인트 URL에서 찾을 수 있습니다. 이렇게 하면 REST API 호출을 쉽게 구성 하 고 실행할 수 있는 API 콘솔이 시작 됩니다.

![워크플로 - 만들기 또는 업데이트 페이지 영역 선택](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 호출 매개 변수 입력

**팀**, **Workflowname**및 **Ocp-구독 키**에 대 한 값을 입력 합니다.

- **팀**: 검토 [도구](https://contentmoderator.cognitive.microsoft.com/) 계정 (검토 도구의 자격 증명 화면에서 **ID** 필드에 있음)을 설정할 때 만든 팀 ID입니다.
- **workflowname**: 추가할 새 워크플로의 이름 (또는 기존 워크플로를 업데이트 하려는 경우 기존 이름)입니다.
- **Ocp-Apim-Subscription-키**: Content Moderator 키입니다. 이는 [검토 도구의](https://contentmoderator.cognitive.microsoft.com) **설정** 탭에서 찾을 수 있습니다.

![워크플로 - 만들기 또는 업데이트 콘솔 쿼리 매개 변수 및 헤더](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>워크플로 정의 입력

1. **요청 본문** 상자를 편집 하 여 **설명** 및 **유형** (`Image` 또는 `Text`)에 대 한 세부 정보가 포함 된 JSON 요청을 입력 합니다.
2. **식**의 경우 기본 워크플로 JSON 식을 복사 합니다. 최종 JSON 문자열은 다음과 같습니다.

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> 이 API를 사용 하 여 워크플로에 대 한 단순 하 고 복잡 하며 중첩 된 식도 정의할 수 있습니다. [워크플로 만들기 또는 업데이트](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) 설명서에는 보다 복잡 한 논리의 예가 포함 되어 있습니다.

### <a name="submit-your-request"></a>요청 제출
  
**보내기**를 선택합니다. 작업이 성공하면 **응답 상태**가 `200 OK`이고, **응답 콘텐츠** 상자에 `true`가 표시됩니다.

### <a name="examine-the-new-workflow"></a>새 워크플로를 검사 합니다.

[검토 도구](https://contentmoderator.cognitive.microsoft.com/)에서 **설정**  > **워크플로**를 선택 합니다. 새 워크플로가 목록에 표시 됩니다.

![워크플로의 검토 도구 목록](images/workflow-console-new-workflow.PNG)

워크플로에 대 한 **편집** 옵션을 선택 하 고 **디자이너** 탭으로 이동 합니다. 여기에서 JSON 논리의 직관적인 표현을 볼 수 있습니다.

![선택된 워크플로에 대한 디자이너 탭](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>워크플로 세부 정보 가져오기

기존 워크플로에 대 한 세부 정보를 검색 하려면 **[워크플로-](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API 참조 가져오기 페이지로 이동 하 여 해당 지역의 단추 (키가 관리 되는 지역)를 선택 합니다.

![워크플로 - 지역 선택 가져오기](images/test-drive-region.png)

위의 섹션과 같이 REST 호출 매개 변수를 입력 합니다. 이번에는 **workflowname** 이 기존 워크플로의 이름 인지 확인 합니다.

![쿼리 매개 변수 및 헤더 가져오기](images/workflow-get-default.PNG)

**보내기**를 선택합니다. 작업에 성공 하면 **응답 상태** 는 `200 OK`이 고 **응답 콘텐츠** 상자는 다음과 같이 워크플로를 JSON 형식으로 표시 합니다.

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>다음 단계

- [콘텐츠 조정 작업](try-review-api-job.md)을 통해 워크플로를 사용하는 방법을 알아봅니다.