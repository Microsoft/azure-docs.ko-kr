---
title: Create UI definition 참조 함수
description: 다른 개체를 참조하는 Azure Portal용 UI 정의를 생성하는 경우 사용할 함수에 대해 설명합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: ad21c5b34a58c35b2cef5e430be7cb8cd1296402
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87098176"
---
# <a name="createuidefinition-referencing-functions"></a>CreateUiDefinition 참조 함수

CreateUiDefinition 파일의 속성 또는 컨텍스트에서 출력을 참조하는 경우 사용할 함수입니다.

## <a name="basics"></a>기본 사항

[기본 사항](create-uidefinition-overview.md#basics) 단계에서 정의된 요소의 출력 값을 반환합니다. 요소 이름을 매개 변수로 이 함수에 전달합니다.

다른 단계에서 요소의 출력 값을 가져오려면 [steps()](#steps) 함수를 사용합니다.

다음 예제에서는 기본 사항 단계의 `clusterName` 요소 출력을 반환합니다.

```json
"[basics('clusterName')]"
```

반환되는 값은 검색되는 요소의 유형에 따라 달라집니다.

## <a name="location"></a>위치

기본 사항 단계 또는 현재 컨텍스트에서 선택한 위치를 반환합니다.

다음 예제에서는 `"westus"` 같은 값을 반환합니다.

```json
"[location()]"
```

## <a name="resourcegroup"></a>resourceGroup

기본 사항 단계 또는 현재 컨텍스트에서 선택한 resourceGroup에 대한 세부 정보를 반환합니다.

다음 예제가 하는 일:

```json
"[resourceGroup()]"
```

다음 속성을 반환합니다.

```json
{
    "mode": "New" or "Existing",
    "name": "{resourceGroupName}",
    "location": "{resourceGroupLocation}"
}
```

점 표기법을 사용하여 특정 값을 가져올 수 있습니다.

```json
"[resourceGroup().name]"
```

## <a name="steps"></a>단계

지정된 단계에 대한 요소를 반환합니다. 단계 이름을 매개 변수로 이 함수에 전달합니다. 반환된 요소에서 특정 속성 값을 가져올 수 있습니다.

기본 사항 단계에서 요소의 출력 값을 가져오려면 [basics()](#basics) 함수를 사용합니다.

다음 예제에서는 `vmParameters`라는 단계를 반환합니다. 해당 단계에 `adminUsername`이라는 요소가 있습니다.

```json
"[steps('vmParameters').adminUsername]"
```

## <a name="subscription"></a>subscription

기본 사항 단계 또는 현재 컨텍스트에서 선택한 구독의 속성을 반환합니다.

다음 예제가 하는 일:

```json
"[subscription()]"
```

다음 속성을 반환합니다.

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

## <a name="next-steps"></a>다음 단계

* 포털 인터페이스 개발에 대한 개요는 [Azure의 관리되는 애플리케이션 만들기 환경을 위한 CreateUiDefinition.json](create-uidefinition-overview.md)을 참조하세요.
