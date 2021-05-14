---
title: UserNameTextBox UI 요소
description: Azure Portal의 Microsoft.Compute.UserNameTextBox UI 요소에 대해 설명합니다. 사용자가 Windows 또는 Linux 사용자 이름을 제공할 수 있습니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 82478f322e1df22bde50769b90f0424140920e9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87063572"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Microsoft.Compute.UserNameTextBox UI 요소

Windows 및 Linux 사용자 이름에 대한 기본 제공 유효성 검사가 포함된 텍스트 상자 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft-compute-usernametextbox.png)

## <a name="schema"></a>스키마

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>샘플 출력

```json
"Example name"
```

## <a name="remarks"></a>설명

- `constraints.required`를 **true** 로 설정하면 텍스트 상자에서 유효성을 성공적으로 검사할 수 있는 값을 포함해야 합니다. 기본값은 **true** 입니다.
- `osPlatform`을 지정해야 하며 **Windows** 또는 **Linux** 일 수 있습니다.
- `constraints.regex`는 JavaScript 정규식 패턴입니다. 지정하면 텍스트 상자의 값이 유효성을 성공적으로 검사하기 위한 패턴과 일치해야 합니다. 기본값은 **null** 입니다.
- `constraints.validationMessage`는 텍스트 상자의 값이 `constraints.regex`에 지정된 유효성 검사에 실패할 때 표시할 문자열입니다. 지정하지 않으면 텍스트 상자의 기본 제공 유효성 검사 메시지가 사용됩니다. 기본값은 **null** 입니다.
- 이 요소에는 `osPlatform`에 지정된 값을 기반으로 하는 기본 제공 유효성 검사가 있습니다. 기본 제공 유효성 검사는 사용자 지정 정규식과 함께 사용할 수 있습니다. `constraints.regex`에 대한 값을 지정하면 기본 제공 및 사용자 지정 유효성 검사가 모두 트리거됩니다.

## <a name="next-steps"></a>다음 단계

* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
