---
title: Azure Event Grid에 대한 이벤트 필터링
description: Azure Event Grid 구독을 만들 때 이벤트를 필터링하는 방법을 설명합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: 87599b05a3569bf6f28880352185a131f48a7f52
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61436174"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid 구독에 대한 이벤트 필터링 이해

이 문서에서는 엔드포인트에 전송되는 이벤트를 필터링하는 다양한 방법을 설명합니다. 이벤트 구독을 만들 때 필터링에 대한 세 가지 옵션이 있습니다.

* 일정 유형
* 다음 값으로 시작하거나 끝나는 제목
* 고급 필드 및 연산자

## <a name="event-type-filtering"></a>이벤트 유형 필터링

기본적으로 이벤트 원본에 대한 모든 [이벤트 유형](event-schema.md)은 엔드포인트로 전송됩니다. 특정 이벤트 유형만 엔드포인트에 보내도록 결정할 수 있습니다. 예를 들어 리소스에 대한 업데이트 알림을 받을 수 있지만 삭제와 같은 다른 작업에 대한 알림을 받을 수 없습니다. 이런 경우 `Microsoft.Resources.ResourceWriteSuccess` 이벤트 유형별로 필터링합니다. 이벤트 유형으로 배열을 제공하거나 이벤트 원본에 대한 모든 이벤트 유형을 가져오도록 `All`을 지정합니다.

이벤트 유형별로 필터링에 대한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>제목 필터링

제목별 간단한 필터링을 위해 제목에 대한 시작 또는 끝 값을 지정합니다. 예를 들어 `.txt`로 끝나는 제목을 지정하여 저장소 계정에 텍스트 파일 업로드와 관련된 이벤트만을 가져올 수 있습니다. 또는 `/blobServices/default/containers/testcontainer`로 시작하는 제목을 필터링하여 저장소 계정에 다른 컨테이너가 아닌 해당 컨테이너에 대한 모든 이벤트를 가져올 수 있습니다.

사용자 지정 항목에 이벤트를 게시할 때 구독자가 이벤트에 관심이 있는지 더 쉽게 알 수 있도록 사용자 이벤트에 대한 제목을 만듭니다. 구독자는 제목 속성을 사용하여 이벤트를 필터링 및 라우팅합니다. 구독자가 해당 경로의 세그먼트를 기준으로 필터링할 수 있도록 이벤트가 발생하는 경로를 추가하는 것을 고려합니다. 구독자는 경로를 통해 이벤트를 제한적이거나 광범위하게 필터링할 수 있습니다. 제목에 `/A/B/C`와 같은 3개의 세그먼트 경로를 제공하는 경우 구독자는 첫 번째 세그먼트 `/A`를 기준으로 필터링하여 광범위한 이벤트 집합을 가져올 수 있습니다. 구독자는 `/A/B/C` 또는 `/A/D/E`와 같은 제목이 있는 이벤트를 가져옵니다. 다른 구독자는 `/A/B`를 기준으로 필터링하여 제한된 이벤트 집합을 얻을 수 있습니다.

이벤트 유형별로 필터링에 대한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>고급 필터링

데이터 필드에서 값을 기준으로 필터링하고 비교 연산자를 지정하려면 고급 필터링 옵션을 사용합니다. 고급 필터링에서 다음을 지정합니다.

* 연산자 형식 - 비교의 형식입니다.
* 키 - 필터링에 사용하는 이벤트 데이터의 필드입니다. 숫자, 부울 또는 문자열일 수 있습니다.
* 값 - 키와 비교할 값입니다.

고급 필터를 사용할 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "advancedFilters": [
    {
      "operatorType": "NumberGreaterThanOrEquals",
      "key": "Data.Key1",
      "value": 5
    },
    {
      "operatorType": "StringContains",
      "key": "Subject",
      "values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>연산자

숫자에 사용 가능한 연산자는 다음과 같습니다.

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

부울에 사용 가능한 연산자: BoolEquals

문자열에 사용 가능한 연산자는 다음과 같습니다.

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

모든 문자열 비교는 대/소문자를 구분하지 않습니다.

### <a name="key"></a>키

Event Grid 스키마의 이벤트의 경우 키에 대해 다음 값을 사용합니다.

* Id
* 항목
* Subject
* EventType
* DataVersion
* 이벤트 데이터(예: Data.key1)

클라우드 이벤트 스키마의 이벤트의 경우 키에 대해 다음 값을 사용합니다.

* EventId
* 원본
* EventType
* EventTypeVersion
* 이벤트 데이터(예: Data.key1)

사용자 지정 입력 스키마의 경우 이벤트 데이터 필드(예: Data.key1)를 사용합니다.

### <a name="values"></a>값

값은 다음이 될 수 있습니다.

* number
* 문자열
* 부울
* array

### <a name="limitations"></a>제한 사항

고급 필터링에는 다음과 같은 제한이 있습니다.

* 이벤트 그리드 구독당 5개의 고급 필터
* 문자열 값당 512자
* **in** 및 **not in** 연산자에 대한 5개의 값
* 키에는 한 가지 수준의 중첩만을 있을 수 있습니다(예: data.key1).
* 사용자 지정 이벤트 스키마는 최상위 필드에서만 필터링됩니다.

둘 이상의 필터에 동일한 키를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* PowerShell 및 Azure CLI를 사용하여 이벤트 필터링에 대해 알아보려면 [Event Grid에 대한 이벤트 필터링](how-to-filter-events.md)을 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
