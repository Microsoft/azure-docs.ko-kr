---
title: 워크플로 정의 언어-Azure Logic Apps에 대 한 스키마 참조
description: Azure Logic Apps에서 워크플로 정의 언어 스키마에 대 한 참조 가이드
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 04/30/2018
ms.openlocfilehash: d80ffa862546f56e93a338a7a1db031e2cb55990
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845757"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Azure Logic Apps의 워크플로 정의 언어에 대한 스키마 참조

논리 앱을 만들 때 [Azure Logic Apps](../logic-apps/logic-apps-overview.md), 논리 앱에 논리 앱에서 실행 되는 실제 논리를 설명 하는 기본 워크플로 정의가 있습니다. 워크플로 정의 사용 하 여 [JSON](https://www.json.org/) 및 워크플로 정의 언어 스키마에 의해 유효성이 검사 되는 구조를 따릅니다. 이 참조는이 구조 및 스키마 워크플로 정의의 요소를 정의 하는 방법에 대 한 개요를 제공 합니다.

## <a name="workflow-definition-structure"></a>워크플로 정의 구조

워크플로 정의 항상 논리 앱 및 트리거가 실행 후 실행 되는 하나 이상의 작업을 인스턴스화하기 위한 트리거를 포함 합니다.

워크플로 정의에 대한 고급 구조는 다음과 같습니다.

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| 요소 | 필수 | 설명 |
|---------|----------|-------------|
| 정의 | 예 | 워크플로 정의에 대한 시작 요소 |
| $schema | 외부에서 워크플로 정의를 참조하는 경우만 | 워크플로 정의 언어의 버전을 설명하는 JSON 스키마 파일의 위치를 여기서 찾을 수 있습니다. <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| contentVersion | 아닙니다. | 워크플로 정의의 버전 번호는 기본적으로 “1.0.0.0”입니다. 워크플로 배포하는 경우 올바른 정의를 식별하고 확인하려면 사용할 값을 지정합니다. |
| 매개 변수 | 아닙니다. | 데이터를 워크플로에 전달하는 하나 이상의 매개 변수에 대한 정의 <p><p>최대 매개 변수: 50 |
| 트리거 | 아닙니다. | 워크플로를 인스턴트화하는 하나 이상의 트리거에 대한 정의 Logic Apps Designer를 사용하여 시각적으로가 아닌, 워크플로 정의 언어를 통해서만 하나 초과 트리거를 정의할 수 있습니다. <p><p>최대 트리거: 10 |
| actions | 아닙니다. | 워크플로 런타임 시 실행하는 하나 이상의 작업에 대한 정의 <p><p>최대 작업: 250 |
| outputs | 아닙니다. | 워크플로 실행에서 반환되는 출력에 대한 정의 <p><p>최대 출력: 10 |
||||

## <a name="parameters"></a>매개 변수

에 `parameters` 섹션에서 입력을 수락 하기 위해 배포 시 워크플로 정의 사용 하는 모든 워크플로 매개 변수를 정의 합니다. 매개 변수 선언 및 매개 변수 값 모두 배포 시 필요합니다. 다른 워크플로 섹션에서 이러한 매개 변수를 사용할 수 있기 전에 이 섹션에서 모든 매개 변수를 선언하는지 확인합니다. 

매개 변수 정의에 대한 일반적인 구조는 다음과 같습니다.

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| 요소 | 필수 | 형식 | 설명 |
|---------|----------|------|-------------|
| 형식 | 예 | int, float, 문자열, securestring, bool, 배열, JSON 개체, secureobject <p><p>**참고**: 모든 암호, 키 및 비밀의 경우 `GET` 작업은 이러한 형식을 반환하지 않기 때문에 `securestring` 및 `secureobject` 형식을 사용합니다. 매개 변수를 보호 하는 방법에 대 한 자세한 내용은 참조 하세요. [논리 앱 보안](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | 매개 변수의 형식 |
| defaultValue | 예 | `type`과 동일 | 워크플로 인스턴스화할 때 아무 값도 지정하지 않는 경우의 기본 매개 변수 값 |
| allowedValues | 아닙니다. | `type`과 동일 | 매개 변수를 수용할 수 있는 값으로 배열 |
| metadata | 아닙니다. | JSON 개체 | 예를 들어, 이름 또는 논리 앱 또는 흐름을 Visual Studio 또는 다른 도구에서 사용 하는 디자인 타임 데이터에 대 한 읽을 수 있는 설명을 모든 다른 매개 변수 정보를 |
||||

## <a name="triggers-and-actions"></a>트리거 및 작업

워크플로 정의에서 `triggers` 및 `actions` 섹션은 워크플로 실행 중에 발생하는 호출을 정의합니다. 구문 및 이러한 섹션에 대한 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조합니다.

## <a name="outputs"></a>outputs

`outputs` 섹션에서 워크플로가 실행을 종료할 때 반환할 수 있는 데이터를 정의합니다. 예를 들어, 각 실행에서 특정 상태 또는 값을 추적하려면 워크플로 출력이 해당 데이터를 반환하도록 지정합니다.

> [!NOTE]
> 서비스의 REST API에서 들어오는 요청에 응답하는 경우 `outputs`을 사용하지 마세요. 대신 `Response` 작업 형식을 사용합니다. 자세한 내용은 [워크플로 트리거 및 작업](../logic-apps/logic-apps-workflow-actions-triggers.md)을 참조하세요.

출력 정의에 대한 일반적인 구조는 다음과 같습니다.

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| 요소 | 필수 | 형식 | 설명 |
|---------|----------|------|-------------|
| <*key-name*> | 예 | 문자열 | 출력 반환 값에 대한 키 이름 |
| 형식 | 예 | int, float, 문자열, securestring, bool, 배열, JSON 개체 | 출력 반환 값의 형식 |
| 값 | 예 | `type`과 동일 | 출력 반환 값 |
|||||

워크플로 실행에서 출력을 가져오려면, 논리 앱의 실행된 기록 및 Azure portal에서 세부 정보를 검토 하거나 사용 합니다 [워크플로 REST API](https://docs.microsoft.com/rest/api/logic/workflows)합니다. 또한 대시보드를 만들 수 있도록 외부 시스템, 예를 들어 PowerBI에 출력을 전달할 수 있습니다.

<a name="expressions"></a>

## <a name="expressions"></a>식

Json을 사용하면 예를 들어 디자인 타임 시 존재하는 리터럴 값이 있을 수 있습니다.

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

실행 시간까지 존재하지 않는 값이 있을 수 있습니다. 이러한 값을 표시하려면 실행 시간에 평가되는 *식*을 사용할 수 있습니다. 식은 하나 이상의 [함수](#functions), [연산자](#operators), 변수, 명시적인 값 또는 상수를 포함하는 시퀀스입니다. 워크플로 정의에서 식 앞에 at 기호(\@)를 붙여 JSON 문자열 값의 어디에서나 식을 사용할 수 있습니다. JSON 값을 나타내는 식을 계산하는 경우 식 본문은 다른 JSON 값에서 결과 및 \@ 문자를 제거하여 추출합니다.

예를 들어 이전에 정의된 `customerName` 속성의 경우 식에서 [매개 변수()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) 함수를 사용하여 속성 값을 얻고 해당 값을 `accountName` 속성에 할당할 수 있습니다.

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

*문자열 보간*은 \@ 문자와 중괄호({})로 래핑된 문자열 내에 여러 개의 식을 사용할 수 있게 합니다. 구문은 다음과 같습니다.

```json
@{ "<expression1>", "<expression2>" }
```

결과는 항상 문자열로서 예를 들어 이 기능을 `concat()` 함수와 유사하게 만듭니다. 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

\@ 문자로 시작하는 리터럴 문자열이 있는 경우 \@ 문자 앞에 이스케이프 문자(\@\@)로서 다른 \@ 문자를 붙입니다.

이러한 예제는 식의 평가 방식을 보여 줍니다.

| JSON 값 | 결과 |
|------------|--------|
| "Sophia Owen" | 이러한 문자를 반환: 'Sophia Owen' |
| "배열[1]" | 이러한 문자 '배열[1]'을 반환 |
| "\@\@" | 이러한 문자를 한 문자열 '\@'로 반환 |
| “\@” | 이러한 문자를 두 문자열 '\@'로 반환 |
|||

이 예제에서는 "myBirthMonth"를 "1월", "myAge"를 숫자 42와 동일하게 정의하는 것으로 가정합니다.

```json
"myBirthMonth": "January",
"myAge": 42
```

이러한 예제는 다음 식의 평가 방식을 보여 줍니다.

| JSON 식 | 결과 |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | 이 문자열을 반환: "1월" |
| "\@{parameters('myBirthMonth')}" | 이 문자열을 반환: "1월" |
| "\@parameters('myAge')" | 이 숫자를 반환: 42 |
| "\@{parameters('myAge')}" | 이 숫자를 문자열로 반환: "42" |
| "내 나이는 \@{parameters('myAge')}살" | 이 문자열을 반환: "내 나이는 42살" |
| "\@('내 나이는 ', string(parameters('myAge')))" | 이 문자열을 반환: "내 나이는 42살" |
| "내 나이는 \@\@{parameters('myAge')}살" | 식이 포함된 이 문자열을 반환: "내 나이는 \@{parameters('myAge')}살" |
|||

Logic Apps Designer에서 시각적으로 작업하는 경우 예를 통해 식 작성기를 통해 식을 만들 수 있습니다.

![Logic Apps Designer > 식 작성기](./media/logic-apps-workflow-definition-language/expression-builder.png)

완료되면 워크플로 정의에서 해당 속성, 예를 들어 여기서는 `searchQuery` 속성에 대해 식이 표시됩니다.

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>연산자

[식](#expressions) 및 [함수](#functions)에서 연산자는 속성 참조 또는 배열의 값 등의 특정 작업을 수행합니다.

| 연산자 | Task |
|----------|------|
| ' | 리터럴 문자열을 입력으로 또는 식 및 함수에서 사용하려면 작은따옴표, 예를 들어 `'<myString>'`만 사용하여 문자열을 래핑합니다. 전체 식에서 JSON 서식과 충돌하는 큰따옴표("")를 사용하지 마세요. 예를 들면 다음과 같습니다. <p>**예**: length('Hello') </br>**아니요**: length('Hello') <p>배열이나 숫자를 전달할 때 문장 부호를 래핑하지 않아도 됩니다. 예를 들면 다음과 같습니다. <p>**예**: length([1, 2, 3]) </br>**아니요**: length([1, 2, 3]) |
| [] | 배열의 특정 위치(인덱스)에서 값을 참조하려면 대괄호를 사용합니다. 예를 들어 배열에서 두 번째 항목을 가져오려면: <p>`myArray[1]` |
| . | 개체의 속성을 참조하려면 점 연산자를 사용합니다. 예를 들어 `customer` JSON 개체에 대한 `name` 속성을 가져오려면: <p>`"@parameters('customer').name"` |
| ? | 런타임 오류 없이 개체의 null 속성을 참조하려면 물음표 연산자를 사용합니다. 예를 들어 트리거에서 null 출력을 처리하려면 이 식을 사용할 수 있습니다. <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Functions

일부 식은 워크플로 정의 실행 하기 시작할 때 아직 존재 하지 않을 수 있는 런타임 작업에서 값을 가져옵니다. 식에서 이러한 값을 사용하거나 참조하려면 워크플로 정의 언어가 제공하는 [*함수*](../logic-apps/workflow-definition-language-functions-reference.md)를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [워크플로 정의 언어 작업 및 트리거](../logic-apps/logic-apps-workflow-actions-triggers.md)에 대해 알아봅니다
* [워크플로 REST API](https://docs.microsoft.com/rest/api/logic/workflows)를 사용하여 프로그래밍 방식으로 논리 앱을 만들고 관리하는 방법에 대해 알아봅니다
