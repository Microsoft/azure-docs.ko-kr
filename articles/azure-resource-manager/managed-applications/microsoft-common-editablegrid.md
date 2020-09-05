---
title: EditableGrid UI 요소
description: Azure Portal에 대 한 EditableGrid UI 요소에 대해 설명 합니다. 사용자가 테이블 형식 입력을 수집할 수 있도록 합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: tomfitz
ms.openlocfilehash: 04f86883a75110985d1cbe050fe3fd3e0582986a
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893771"
---
# <a name="microsoftcommoneditablegrid-ui-element"></a>EditableGrid UI 요소

테이블 형식 입력을 수집 하기 위한 컨트롤입니다. 표 안의 모든 필드는 편집할 수 있으며 행 수는 다를 수 있습니다.

## <a name="ui-sample"></a>UI 샘플

:::image type="content" source="./media/managed-application-elements/microsoft-common-editablegrid.png" alt-text="EditableGrid":::

## <a name="schema"></a>스키마

```json
{
  "name": "people",
  "type": "Microsoft.Common.EditableGrid",
  "ariaLabel": "Enter information per person",
  "label": "People",
  "constraints": {
    "width": "Full",
    "rows": {
      "count": {
        "min": 1,
        "max": 10
      }
    },
    "columns": [
      {
        "id": "colName",
        "header": "Name",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.TextBox",
          "placeholder": "Full name",
          "constraints": {
            "required": true,
            "validations": [
              {
                "isValid": "[startsWith(last(take(steps('grid').people, $rowIndex)).colName, 'contoso')]",
                "message": "Must start with 'contoso'."
              },
              {
                "regex": "^[a-z0-9A-Z]{1,30}$",
                "message": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
              }
            ]
          }
        }
      },
      {
        "id": "colGender",
        "header": "Gender",
        "width": "1fr",
        "element": {
          "name": "dropDown1",
          "type": "Microsoft.Common.DropDown",
          "placeholder": "Select a gender...",
          "constraints": {
            "allowedValues": [
              {
                "label": "Female",
                "value": "female"
              },
              {
                "label": "Male",
                "value": "male"
              },
              {
                "label": "Other",
                "value": "other"
              }
            ],
            "required": true
          }
        }
      },
      {
        "id": "colContactPreference",
        "header": "Contact preference",
        "width": "1fr",
        "element": {
          "type": "Microsoft.Common.OptionsGroup",
          "constraints": {
            "allowedValues": [
              {
                "label": "Email",
                "value": "email"
              },
              {
                "label": "Text",
                "value": "text"
              }
            ],
            "required": true
          }
        }
      }
    ]
  }
}
```

## <a name="sample-output"></a>샘플 출력

```json
{
  "colName": "contoso",
  "colGender": "female",
  "colContactPreference": "email"
}
```

## <a name="remarks"></a>설명

- Columns 배열 내에서 유효한 유일한 컨트롤은 [TextBox](microsoft-common-textbox.md), 옵션만 [group](microsoft-common-optionsgroup.md)및 [DropDown](microsoft-common-dropdown.md)입니다.
- `$rowIndex`변수는 그리드 열의 자식에 포함 된 식 에서만 유효 합니다. 요소의 상대 행 인덱스를 나타내는 정수 이며 개수는 1에서 시작 하 여 1 씩 증가 합니다. 스키마 섹션에 표시 된 것 처럼 `"columns":` 는 `$rowIndex` 유효성 검사에 사용 됩니다.
- 변수를 사용 하 여 유효성 검사를 수행 하는 경우 `$rowIndex` 및 명령을 결합 하 여 현재 행의 값을 가져올 수 있습니다 `last()` `take()` .

  예를 들면

  `last(take(<reference_to_grid>, $rowIndex))`

- `label`속성은 컨트롤의 일부로 표시 되지 않지만 최종 탭 요약에 표시 됩니다.
- `ariaLabel`속성은 표의 접근성 레이블입니다. 화면 판독기를 사용 하는 사용자에 게 유용한 텍스트를 지정 합니다.
- `constraints.width`속성은 표의 전체 너비를 설정 하는 데 사용 됩니다. 옵션은 _Full_, _Medium_, _Small_입니다. 기본값은 _Full_입니다.
- 열 `width` 자식의 속성에 따라 열 너비가 결정 됩니다. 너비는 _3fr_과 같은 분수 단위를 사용 하 여 지정 되며, 해당 단위에 비례 하는 열에 전체 공간이 할당 됩니다. 열 너비를 지정 하지 않으면 기본값은 _1fr_입니다.

## <a name="next-steps"></a>다음 단계

- UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
- UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
