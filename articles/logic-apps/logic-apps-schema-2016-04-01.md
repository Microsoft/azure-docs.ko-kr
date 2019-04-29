---
title: 2016년 6월 1일 스키마 업데이트 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps의 논리 앱 정의에 대한 업데이트된 스키마 버전 2016-06-01
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 349d57e8-f62b-4ec6-a92f-a6e0242d6c0e
ms.topic: article
ms.date: 07/25/2016
ms.openlocfilehash: 6df29543df2b7b2609582f7e8dd9a0629182760c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995798"
---
# <a name="schema-updates-for-azure-logic-apps---june-1-2016"></a>Azure Logic Apps에 대한 스키마 업데이트 - 2016년 6월 1일

Azure Logic Apps에 대한 [업데이트된 스키마](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json) 및 API 버전에 논리 앱의 안정성 및 사용 편의성을 개선하는 향상된 주요 기능이 포함됩니다.

* [범위](#scopes)를 통해 작업 컬렉션으로 작업을 그룹화 또는 중첩시킬 수 있습니다.
* [조건과 루프](#conditions-loops)는 최고 수준의 작업입니다.
* `dependsOn`를 대체하는 `runAfter` 속성이 있는 작업을 실행하기 위한 보다 정확한 정렬

2015년 8월 1일 미리 보기 스키마에서 2016년 6월 1일 스키마로 논리 앱을 업그레이드하려면 [업그레이드 섹션을 확인합니다.](#upgrade-your-schema)

<a name="scopes"></a>

## <a name="scopes"></a>범위

이 스키마는 작업을 함께 그룹화하거나 작업을 서로 중첩시킬 수 있는 범위를 포함합니다. 예를 들어 조건은 다른 조건을 포함할 수 있습니다. [범위 구문](../logic-apps/logic-apps-loops-and-scopes.md)에 대해 자세히 알아보거나 기본 범위 예제를 검토하세요.

```json
{
   "actions": {
      "Scope": {
         "type": "Scope",
         "actions": {                
            "Http": {
               "inputs": {
                   "method": "GET",
                   "uri": "https://www.bing.com"
               },
               "runAfter": {},
               "type": "Http"
            }
         }
      }
   }
}
```

<a name="conditions-loops"></a>

## <a name="conditions-and-loops-changes"></a>조건 및 루프 변경

이전 버전의 스키마에서 조건과 루프는 단일 작업과 관련된 매개 변수였습니다. 이 스키마는 이 제한 사항을 해제하므로 이제는 조건과 루프가 작업 유형으로 제공됩니다. [루프 및 범위](../logic-apps/logic-apps-loops-and-scopes.md), [조건](../logic-apps/logic-apps-control-flow-conditional-statement.md)에 대해 자세히 알아보거나 조건 작업을 보여 주는 기본 예제를 검토하세요.

```json
{
   "Condition - If trigger is some trigger": {
      "type": "If",
      "expression": "@equals(triggerBody(), '<trigger-name>')",
      "runAfter": {},
      "actions": {
         "Http_2": {
            "inputs": {
                "method": "GET",
                "uri": "https://www.bing.com"
            },
            "runAfter": {},
            "type": "Http"
         }
      },
      "else": 
      {
         "Condition - If trigger is another trigger": {}
      }  
   }
}
```

<a name="run-after"></a>

## <a name="runafter-property"></a>'runAfter' 속성

`runAfter` 속성은 `dependsOn`을 대체하며 이전 작업의 상태에 따라 작업의 실행 순서를 지정할 때 더 높은 정확도를 제공합니다. `dependsOn` 속성은 이전 작업을 실행하려 했던 횟수가 아닌 해당 작업의 결과(성공/실패/건너뜀)에 따라 작업을 실행했으며 성공했는지 여부를 표시했습니다. 반면 “개체” 개념으로 사용되는 `runAfter` 속성은 개체가 실행되는 모든 작업 이름을 지정하는 방식을 통해 유동적으로 실행됩니다. 또한 이 속성은 트리거로 허용 가능한 상태 배열도 정의합니다. 예를 들어 A 작업이 정상적으로 실행되고 B 작업이 정상적으로 실행되거나 실패한 후에 작업을 실행하려는 경우 다음 `runAfter` 속성을 설정합니다.

```json
{
   // Other parts in action definition
   "runAfter": {
      "A": ["Succeeded"],
      "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrade-your-schema"></a>스키마 업그레이드

몇 단계만 수행하면 [가장 최근의 스키마](https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json)로 업그레이드할 수 있습니다. 업그레이드 프로세스에는 업그레이드 스크립트를 실행하고 새 논리 앱으로 저장하며 원하는 경우 잠재적으로 이전 논리 앱을 덮어쓰는 과정이 포함됩니다.

1. Azure Portal에서 논리 앱을 엽니다.

2. **개요**로 이동합니다. 논리 앱 도구 모음에서 **스키마 업데이트**를 선택합니다.
   
   ![스키마 업데이트 선택][1]
   
   업그레이드된 정의가 반환되며 필요할 경우 리소스 정의에 복사 및 붙여넣을 수 있습니다. 

   > [!IMPORTANT]
   > 업그레이드된 논리 앱에서 모든 연결 참조가 유효한 상태로 유지되도록 **다른 이름으로 저장**을 선택하는 것이 좋습니다.

3. 업그레이드 블레이드 도구 모음에서 **다른 이름으로 저장**을 선택합니다.

4. 논리 앱 및 상태를 입력합니다. 업그레이드된 논리 앱을 배포하려면 **만들기**를 선택합니다.

5. 업그레이드된 논리 앱이 예상대로 작동하는지 확인합니다.
   
   > [!NOTE]
   > 수동 또는 요청 트리거를 사용하는 경우 콜백 URL이 새 논리 앱에서 변경됩니다. 새 URL을 테스트하여 종단 간 환경이 작동하는지 확인합니다. 이전 URL을 유지하기 위해 기존 논리 앱에 복제할 수 있습니다.

6. *선택 사항* 이전 논리 앱을 새 스키마 버전으로 덮어쓰려면 도구 모음에서 **스키마 업데이트** 옆에 있는 **복제**를 선택합니다. 동일한 리소스 ID를 유지하거나 논리 앱의 트리거 URL을 요청하려는 경우에만 이 단계가 필요합니다.

## <a name="upgrade-tool-notes"></a>업그레이드 도구 정보

### <a name="mapping-conditions"></a>매핑 조건

업그레이드된 정의에서 도구는 최상의 노력을 통해 true 및 false 분기 작업을 범위로 그룹화합니다. 구체적으로 설명하자면, 디자이너 패턴 `@equals(actions('a').status, 'Skipped')`는 `else` 작업으로 표시됩니다. 그러나 이 도구가 인식할 수 없는 패턴을 검색하는 경우 true와 false 분기 모두에 별도의 조건을 만들 수 있습니다. 필요에 따라 업그레이드 후 작업을 다시 매핑할 수 있습니다.

#### <a name="foreach-loop-with-condition"></a>조건이 있는 'foreach' 루프

새 스키마에서는 필터 작업을 사용해 항목당 조건이 하나인 **For each** 루프를 사용하는 패턴을 복제할 수 있습니다. 그러나 업그레이드할 때는 변경이 자동으로 수행됩니다. 즉, **For each** 앞에 나오는 조건이 필터 작업이 되어 조건과 일치하는 항목 배열만 반환하며 **For each** 작업으로 해당 배열을 전달합니다. 이에 대한 예제는 [루프 및 범위](../logic-apps/logic-apps-loops-and-scopes.md)를 참조하세요.

### <a name="resource-tags"></a>리소스 태그

업그레이드한 후에는 리소스 태그가 제거되므로 업그레이드된 워크플로에 대해 재설정해야 합니다.

## <a name="other-changes"></a>기타 변경 내용

### <a name="renamed-manual-trigger-to-request-trigger"></a>'manual' 트리거를 'request' 트리거로 이름 바꾸기

`manual` 트리거 형식이 사용되지 않고 `http` 종류를 사용하여 이름이 `request`로 변경되었습니다. 이러한 변경은 트리거가 구축하는 데 사용되는 패턴의 종류와 더욱 일치합니다.

### <a name="new-filter-action"></a>새 '필터' 작업

대규모 배열을 소규모 항목 집합으로 하위 필터링하려면 새 `filter` 형식은 배열 및 조건을 허용하고 각 항목에 대해 조건을 평가한 후 해당 조건에 맞는 항목이 포함된 배열을 반환합니다.

### <a name="restrictions-for-foreach-and-until-actions"></a>'foreach' 및 'until' 작업에 대한 제한 사항

`foreach` 및 `until` 루프는 한 번의 작업으로 제한됩니다.

### <a name="new-trackedproperties-for-actions"></a>작업에 대한 새 'trackedProperties'

작업에는 `runAfter` 및 `type` 속성의 형제인 `trackedProperties`이라는 추가 속성이 있을 수 있습니다. 이 개체는 워크플로의 일부로 내보내고 Azure 진단 원격 분석에 포함할 특정 작업 입력 또는 출력을 지정합니다. 예를 들면 다음과 같습니다.

``` json
{
   "Http": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com"
      },
      "runAfter": {},
      "type": "Http",
      "trackedProperties": {
         "responseCode": "@action().outputs.statusCode",
         "uri": "@action().inputs.uri"
      }
   }
}
```

## <a name="next-steps"></a>다음 단계
* [논리 앱용 워크플로 정의 만들기](../logic-apps/logic-apps-author-definitions.md)
* [논리 앱용 배포 템플릿 만들기](../logic-apps/logic-apps-create-deploy-template.md)

<!-- Image references -->
[1]: ./media/logic-apps-schema-2016-04-01/upgradeButton.png
