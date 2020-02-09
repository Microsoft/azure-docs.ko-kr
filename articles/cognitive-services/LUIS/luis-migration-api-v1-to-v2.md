---
title: v1에서 v2 API 마이그레이션
titleSuffix: Azure Cognitive Services
description: 버전 1 엔드포인트 및 제작 Language Understanding Api는 더 이상 사용 되지 않습니다. 이 가이드를 사용하여 버전 2 엔드포인트 및 작성 API로 마이그레이션하는 방법을 이해합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932683"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>LUIS 앱에 대한 API v1에서 v2로 마이그레이션 가이드
버전 1 [엔드포인트](https://aka.ms/v1-endpoint-api-docs) 및 [작성](https://aka.ms/v1-authoring-api-docs) API는 더 이상 사용되지 않습니다. 이 가이드를 사용하여 버전 2 [엔드포인트](https://go.microsoft.com/fwlink/?linkid=2092356) 및 [작성](https://go.microsoft.com/fwlink/?linkid=2092087) API로 마이그레이션하는 방법을 이해합니다. 

## <a name="new-azure-regions"></a>새 Azure 지역
LUIS에는 LUIS API에 제공된 새로운 [지역](https://aka.ms/LUIS-regions)이 있습니다. LUIS는 지역 그룹에 대해 다른 포털을 제공 합니다. 애플리케이션은 쿼리할 것으로 예상되는 같은 지역에서 작성해야 합니다. 애플리케이션은 지역을 자동으로 마이그레이션하지 않습니다. 한 지역에서 앱을 내보낸 다음, 새 지역에서 사용할 수 있도록 다른 지역으로 가져옵니다.

## <a name="authoring-route-changes"></a>작성 경로 변경 내용
작성 API 경로가 **prog** 경로 사용에서 **api** 경로 사용으로 변경되었습니다.


| version | 경로(route) |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>엔드포인트 경로 변경 내용
엔드포인트 API에는 다른 응답 뿐만 아니라 새로운 쿼리 문자열 매개 변수가 있습니다. verbose 플래그가 true이면 점수에 관계없이 모든 의도가 topScoringIntent 외에 intents라는 배열로 반환됩니다.

| version | GET 경로 |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]|


v1 엔드포인트 성공 응답:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 엔드포인트 성공 응답:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>API에 더 이상 키 관리가 없음
구독 엔드포인트 키 API는 더 이상 사용되지 않으며 410 없음을 반환합니다.

| version | 경로(route) |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [엔드포인트 키](luis-how-to-azure-subscription.md)는 Azure Portal에서 생성됩니다. **[게시](luis-how-to-azure-subscription.md)** 페이지에서 LUIS 앱에 키를 할당합니다. 실제 키 값을 알 필요가 없습니다. LUIS는 구독 이름을 사용하여 키를 할당합니다. 

## <a name="new-versioning-route"></a>새 버전 관리 경로
이제 v2 모델이 [버전](luis-how-to-manage-versions.md)에 포함됩니다. 버전 이름은 경로에서 10자입니다. 기본 버전은 “0.1”입니다.

| version | 경로(route) |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities|

## <a name="metadata-renamed"></a>메타데이터 이름이 바뀜
LUIS 메타데이터를 반환하는 여러 API에 새 이름이 사용됩니다.

| v1 경로 이름 | v2 경로 이름 |
|--|--|
|PersonalAssistantApps |assistants|
|applicationcultures|cultures|
|applicationdomains|domains|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>“Sample”에서 “suggest”로 이름이 바뀜
LUIS는 모델을 향상시킬 수 있는 기존 [엔드포인트 발화](luis-how-to-review-endpoint-utterances.md)를 기반으로 발화를 제안합니다. 이전 버전에서는 이것을 **sample**이라고 했습니다. 새 버전에서는 이름이 sample에서 **suggest**로 변경됩니다. LUIS 웹 사이트에서는 이를 **[엔드포인트 발화 검토](luis-how-to-review-endpoint-utterances.md)** 라고 합니다.

| version | 경로(route) |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**sample**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**sample**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>미리 빌드된 도메인에서 앱 만들기
[미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)은 미리 정의된 도메인 모델을 제공합니다. 미리 빌드된 도메인을 사용하면 일반 도메인의 LUIS 애플리케이션을 신속하게 개발할 수 있습니다. 이 API를 사용하면 미리 빌드된 도메인을 기반으로 새 앱을 만들 수 있습니다. 응답은 새 appID입니다.

|v2 경로|verb|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |get, post|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>2\.x로 1.x 앱 가져오기
내보낸 1.x 앱의 JSON에는 [LUIS][LUIS] 2.0로 가져오기 전에 변경 해야 할 몇 가지 영역이 있습니다. 

### <a name="prebuilt-entities"></a>미리 빌드된 엔터티 
[미리 빌드된 엔터티](luis-prebuilt-entities.md)가 변경되었습니다. V2 미리 빌드된 엔터티를 사용하고 있는지 확인합니다. 여기에는 datetime 대신에 [datetimeV2](luis-reference-prebuilt-datetimev2.md) 사용이 포함됩니다. 

### <a name="actions"></a>동작
작업 속성이 더 이상 유효하지 않습니다. 비어 있어야 함 

### <a name="labeled-utterances"></a>레이블이 지정된 발화
V1은 레이블이 지정된 발화가 단어나 구문의 시작 또는 끝에 공백을 포함하도록 허용했습니다. 공백을 제거했습니다. 

## <a name="common-reasons-for-http-response-status-codes"></a>HTTP 응답 상태 코드의 일반적인 이유
[LUIS API 응답 코드](luis-reference-response-codes.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

v2 API 설명서를 사용하여 LUIS [엔드포인트](https://go.microsoft.com/fwlink/?linkid=2092356) 및 [작성](https://go.microsoft.com/fwlink/?linkid=2092087) API의 기존 REST 호출을 업데이트합니다. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
