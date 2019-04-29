---
title: Azure Event Grid 구독 스키마
description: Azure Event Grid를 사용하여 이벤트에 대한 구독의 속성을 설명합니다.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/02/2019
ms.author: babanisa
ms.openlocfilehash: 9464ab89e08f53f61cb6f5a4b1e91da35b785af0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822794"
---
# <a name="event-grid-subscription-schema"></a>Event Grid 구독 스키마

Event Grid 구독을 만들려면 이벤트 만들기 구독 작업에 요청을 보냅니다. 이때 다음 형식을 사용합니다.

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

예를 들어 `examplegroup`이라는 리소스 그룹의 `examplestorage`라는 저장소 계정에 대한 이벤트 구독을 만들려면 다음 형식을 사용합니다.

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

이벤트 구독 이름은 길이가 3-64자이어야 하며 a-z, A-Z, 0-9 및 "-"만을 포함할 수 있습니다. 문서는 요청 본문에 대한 속성 및 스키마를 설명합니다.
 
## <a name="event-subscription-properties"></a>이벤트 구독 속성

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| destination | object | 엔드포인트를 정의하는 개체입니다. |
| filter | object | 이벤트 유형을 필터링하기 위한 선택적 필드입니다. |

### <a name="destination-object"></a>대상 개체

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| endpointType | 문자열 | 구독(웹후크/HTTP, Event Hub 또는 큐)에 대한 엔드포인트의 형식입니다. | 
| endpointUrl | 문자열 | 이 이벤트 구독에서 이벤트의 대상 URL입니다. | 

### <a name="filter-object"></a>필터 개체

| 자산 | Type | 설명 |
| -------- | ---- | ----------- |
| includedEventTypes | array | 이벤트 메시지의 이벤트 유형이 이러한 이벤트 유형 이름 중 하나와 정확하게 일치할 때 일치합니다. 이벤트 이름이 이벤트 원본에 대해 등록된 이벤트 유형 이름과 일치하지 않는 경우 오류가 발생합니다. 기본값은 모든 이벤트 유형과 일치합니다. |
| subjectBeginsWith | 문자열 | 이벤트 메시지의 제목 필드에 대한 접두사-일치 필터입니다. 기본값 또는 빈 문자열은 모두 일치합니다. | 
| subjectEndsWith | 문자열 | 이벤트 메시지의 제목 필드에 대한 접미사-일치 필터입니다. 기본값 또는 빈 문자열은 모두 일치합니다. |
| isSubjectCaseSensitive | 문자열 | 필터에 대한 대/소문자 구분 일치를 제어합니다. |


## <a name="example-subscription-schema"></a>예제 구독 스키마

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* Event Grid에 대한 소개는 [Event Grid란?](overview.md)을 참조하세요.