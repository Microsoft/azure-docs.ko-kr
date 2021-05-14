---
title: InfoBox UI 요소
description: Azure Portal의 Microsoft.Common.InfoBox UI 요소에 대해 설명합니다. 관리되는 애플리케이션을 배포하는 경우 텍스트 또는 경고를 추가하는 데 사용합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 7580ac0650706d6aee49bbf0e8235e8c5dab33f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87033356"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI 요소

정보 상자를 추가하는 컨트롤입니다. 이 상자에는 사용자가 입력하는 값을 파악하는 데 도움이 되는 중요한 텍스트나 경고가 포함됩니다. 상자를 자세한 정보를 제공하는 URI에 연결할 수도 있습니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft-common-infobox.png)


## <a name="schema"></a>스키마

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>샘플 출력

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>설명

* `icon`의 경우 **없음**, **정보**, **경고** 또는 **오류** 를 사용합니다.
* `uri` 속성은 선택 사항입니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
