---
title: 템플릿 구문 및 식
description: Azure Resource Manager 템플릿에 대 한 선언적 JSON 구문을 설명 합니다.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207403"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿의 구문 및 식

템플릿의 기본 구문은 JSON이지만, 그러나 식을 사용 하 여 템플릿 내에서 사용할 수 있는 JSON 값을 확장할 수 있습니다.  식의 시작과 끝은 각각 `[` 및 `]`입니다. 식의 값은 템플릿을 배포할 때 평가됩니다. 식은 문자열, 정수, 부울, 배열 또는 개체를 반환할 수 있습니다.

템플릿 식은 24576 자를 초과할 수 없습니다.

## <a name="use-functions"></a>함수 사용

Azure Resource Manager에서는 템플릿에서 사용할 수 있는 [함수](template-functions.md) 를 제공 합니다. 다음 예에서는 매개 변수의 기본값에서 함수를 사용 하는 식을 보여 줍니다.

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

식 내에서 구문은 템플릿 내에서 사용 하기 위해 리소스 관리자 제공 하는 함수 중 하나를 호출 `resourceGroup()` 합니다. 이 경우 [resourceGroup](template-functions-resource.md#resourcegroup) 함수입니다. JavaScript에서와 마찬가지로 함수 호출은 `functionName(arg1,arg2,arg3)`과 같이 형식이 지정됩니다. `.location` 구문은 해당 함수에서 반환 하는 개체에서 속성 하나를 검색 합니다.

템플릿 함수 및 해당 매개 변수는 대/소문자를 구분하지 않습니다. 예를 들어 Resource Manager에서 **variables('var1')** 와 **VARIABLES('VAR1')** 는 동일합니다. 계산될 때 함수는 대/소문자를 명시적으로 수정하지 않는 한(toUpper 또는 toLower 등) 대/소문자를 보존합니다. 특정 리소스 종류에는 함수 평가 방법과 별개의 사례 요구 사항이 있을 수 있습니다.

문자열 값을 매개 변수로 함수에 전달 하려면 작은따옴표를 사용 합니다.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

대부분의 함수는 리소스 그룹, 구독, 관리 그룹 또는 테 넌 트에 배포 되었는지 여부와 동일 하 게 작동 합니다. 다음 함수는 범위에 따라 제한이 있습니다.

* [resourceGroup](template-functions-resource.md#resourcegroup) -리소스 그룹에 대 한 배포에만 사용할 수 있습니다.
* [resourceId](template-functions-resource.md#resourceid) -모든 범위에서 사용할 수 있지만 유효한 매개 변수는 범위에 따라 변경 됩니다.
* [구독](template-functions-resource.md#subscription) -리소스 그룹 또는 구독에 대 한 배포에만 사용할 수 있습니다.

## <a name="escape-characters"></a>이스케이프 문자

리터럴 문자열을 왼쪽 대괄호 `[`로 시작 하 고 오른쪽 대괄호 `]`끝에 `[[`식으로 해석 되지 않도록 하려면 추가 대괄호를 추가 하 여 문자열을 시작 합니다. 예를 들어 변수는 다음과 같습니다.

```json
"demoVar1": "[[test value]"
```

`[test value]`를 확인 합니다.

그러나 리터럴 문자열이 대괄호로 끝나지 않는 경우에는 첫 번째 대괄호가 이스케이프 되지 않습니다. 예를 들어 변수는 다음과 같습니다.

```json
"demoVar2": "[test] value"
```

`[test] value`를 확인 합니다.

템플릿에서 JSON 개체를 추가 하는 것과 같이 식에서 큰따옴표를 이스케이프 하려면 백슬래시를 사용 합니다.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="null-values"></a>Null 값

속성을 null로 설정 하려면 **null** 또는 **[json (' null ')]** 을 사용할 수 있습니다. `null`를 매개 변수로 제공 하면 [json 함수](template-functions-array.md#json) 는 빈 개체를 반환 합니다. 두 경우 모두 리소스 관리자 템플릿에서는 속성이 없는 것 처럼 처리 합니다.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>다음 단계

* 템플릿 함수의 전체 목록을 보려면 [Azure 리소스 관리자 템플릿 함수](template-functions.md)를 참조하세요.
* 템플릿 파일에 대 한 자세한 내용은 [Azure Resource Manager 템플릿 구조 및 구문 이해](template-syntax.md)를 참조 하세요.
