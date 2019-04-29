---
title: Azure StorageAccountSelector UI 요소 | Microsoft Docs
description: Azure Portal의 Microsoft.Storage.StorageAccountSelector UI 요소에 대해 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: c6d4ef50645902aecd57ceb9fc48b7d99bf22d53
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104869"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector UI 요소
새 또는 기존 저장소 계정을 선택하는 컨트롤입니다.

## <a name="ui-sample"></a>UI 샘플

컨트롤에는 기본값이 표시됩니다.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

컨트롤을 사용하면 사용자는 새 Storage 계정을 만들거나 기존 Storage 계정을 선택할 수 있습니다.

![Microsoft.Storage.StorageAccountSelector new](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>스키마

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="remarks"></a>설명
- 지정하는 경우 `defaultValue.name`의 고유성에 대한 유효성 검사가 자동으로 수행됩니다. 저장소 계정 이름이 고유하지 않으면 사용자가 다른 이름을 지정하거나 기존 저장소 계정을 선택해야 합니다.
- `defaultValue.type`의 기본값은 **Premium_LRS**입니다.
- `constraints.allowedTypes`에 지정되지 않은 형식은 숨겨지며, `constraints.excludedTypes`에 지정되지 않은 형식이 표시됩니다. `constraints.allowedTypes` 및 `constraints.excludedTypes`는 모두 선택 사항이지만 동시에 사용할 수는 없습니다.
- `options.hideExisting`이 **true**이면 사용자가 기존 저장소 계정을 선택할 수 없습니다. 기본값은 **false**입니다.

## <a name="sample-output"></a>샘플 출력

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="next-steps"></a>다음 단계
* UI 정의 만들기에 대한 소개는 [CreateUiDefinition 시작](create-uidefinition-overview.md)을 참조하세요.
* UI 요소의 공용 속성에 대한 설명은 [CreateUiDefinition 요소](create-uidefinition-elements.md)를 참조하세요.
