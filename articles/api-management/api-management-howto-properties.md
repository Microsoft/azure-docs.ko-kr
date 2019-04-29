---
title: Azure API Management 정책에 명명된 값을 사용하는 방법
description: Azure API Management 정책에 명명된 값을 사용하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: apimpm
ms.openlocfilehash: 478b80b021b4df36e2eccc37ac9c74f75e43a5bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60658038"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Azure API Management 정책에 명명된 값을 사용하는 방법
API Management 정책은 구성을 통해 Azure Portal에서 API 동작을 변경하도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 정책 설명은 리터럴 텍스트 값, 정책 식 및 명명된 값을 사용하여 생성할 수 있습니다. 

각 API Management 서비스 인스턴스에는 해당 서비스 인스턴스에 전역인, 명명된 값이라고 하는, 키/값 쌍의 속성 컬렉션이 있습니다. 이러한 명명된 값을 사용하여 모든 API 구성 및 정책에서 상수 문자열 값을 관리할 수 있습니다. 각 속성에는 다음과 같은 특성이 있습니다.

| 특성 | Type | 설명 |
| --- | --- | --- |
| 표시 이름 |문자열 |정책의 속성을 참조하는 데 사용되는 영숫자 문자열입니다. |
| 값 |문자열 |속성의 값입니다. 비워 두거나 공백만으로 구성될 수 없습니다. |
|Secret|부울|값이 암호인지, 그리고 암호화해야 하는지 여부를 결정합니다.|
| 태그들 |문자열의 배열 |제공된 경우 속성 목록을 필터링하는 데 사용할 수 있는 선택적 태그입니다. |

![명명된 값](./media/api-management-howto-properties/named-values.png)

속성 값은 리터럴 문자열 및 [정책 식](/azure/api-management/api-management-policy-expressions)을 포함할 수 있습니다. 예를 들어 `ExpressionProperty` 값은 현재 날짜 및 시간이 포함된 문자열을 반환하는 정책 식입니다. `ContosoHeaderValue` 속성은 암호 표식이 있으므로 해당 값이 표시되지 않습니다.

| 이름 | 값 | Secret | 태그들 |
| --- | --- | --- | --- |
| ContosoHeader |TrackingId |거짓 |Contoso |
| ContosoHeaderValue |•••••••••••••••••••••• |True  |Contoso |
| ExpressionProperty |@(DateTime.Now.ToString()) |거짓 | |

## <a name="to-add-and-edit-a-property"></a>속성을 추가하고 편집하려면

![속성 추가](./media/api-management-howto-properties/add-property.png)

1. **API Management**에서 **API**를 선택합니다.
2. **명명된 값**을 선택합니다.
3. **+추가**를 누릅니다.

   이름 및 값은 필수 값입니다. 이 속성 값이 비밀인 경우 암호입니다 확인란을 선택합니다. 명명된 값을 구성하는 데 도움이 되도록 하나 이상의 선택적 태그를 입력하고 저장을 클릭합니다.
4. **만들기**를 클릭합니다.

속성이 생성되면 속성을 클릭하여 속성을 편집할 수 있습니다. 속성 이름을 변경한 경우 해당 속성을 참조하는 모든 정책이 새 이름을 사용하도록 자동으로 업데이트됩니다.

REST API를 사용하여 속성을 편집하는 방법에 대한 자세한 내용은 [REST API를 사용하여 속성 편집](/rest/api/apimanagement/property?Patch)을 참조하세요.

## <a name="to-delete-a-property"></a>속성을 삭제하려면

속성을 삭제하려면 삭제할 속성 옆의 **삭제** 를 클릭합니다.

> [!IMPORTANT]
> 속성을 참조하는 정책이 있는 경우 이러한 모든 정책에서 속성을 제거할 때까지 속성을 성공적으로 삭제할 수 없습니다.
> 
> 

REST API를 사용하여 속성을 삭제하는 방법에 대한 자세한 내용은 [REST API를 사용하여 속성 삭제](/rest/api/apimanagement/property?Delete)를 참조하세요.

## <a name="to-search-and-filter-named-values"></a>명명된 값을 검색하고 필터링하려면

**명명된 값** 탭에는 명명된 값을 관리하는 데 유용한 검색 및 필터링 기능이 있습니다. 속성 이름으로 속성 목록을 필터링하려면 **속성 검색** 텍스트 상자에 검색 용어를 입력합니다. 모든 명명된 값을 표시하려면 **속성 검색** 텍스트 상자를 지우고 Enter 키를 누릅니다.

태그 값으로 속성 목록을 필터링하려면 **태그로 필터링** 텍스트 상자에 하나 이상의 태그를 입력합니다. 모든 명명된 값을 표시하려면 **태그로 필터링** 텍스트 상자를 지우고 Enter 키를 누릅니다.

## <a name="to-use-a-property"></a>속성을 사용하려면

정책에서 속성을 사용하려면 다음 예제와 같이 이중 중괄호 쌍 안에 속성 이름을 배치합니다(예: `{{ContosoHeader}}`).

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

이 예제에서 `ContosoHeader`는 `set-header` 정책의 헤더 이름으로 사용되고, `ContosoHeaderValue`는 해당 헤더의 값으로 사용됩니다. API Management 게이트웨이에 대한 요청이나 응답 중에 이 정책을 평가하는 경우 `{{ContosoHeader}}` 및 `{{ContosoHeaderValue}}`는 해당 속성 값으로 바뀝니다.

명명된 값은 위 예제에 표시된 대로 전체 특성 또는 요소 값으로 사용될 수 있지만 다음 예제와 같이 리터럴 텍스트 식의 일부로 삽입되거나 결합될 수도 있습니다. `<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

또한 명명된 값은 정책 식을 포함할 수 있습니다. 다음 예제에서는 `ExpressionProperty`가 사용됩니다.

```xml
<set-header name="CustomHeader" exists-action="override">
    <value>{{ExpressionProperty}}</value>
</set-header>
```

이 정책을 평가할 때 `{{ExpressionProperty}}`는 해당 값 `@(DateTime.Now.ToString())`으로 바뀝니다. 이 값은 정책 식이므로 식이 계산되고 정책이 계속 실행됩니다.

범위에 명명된 값이 포함된 정책이 있는 작업을 호출하여 개발자 포털에서 이를 테스트할 수 있습니다. 다음 예제에서는 명명된 값이 있는 이전 두 예제 `set-header` 정책으로 작업이 호출되었습니다. 응답에 명명된 값이 있는 정책을 사용하여 구성된 두 개의 사용자 지정 헤더가 포함되어 있습니다.

![개발자 포털][api-management-send-results]

명명된 값이 있는 이전 두 샘플 정책이 포함된 호출에 대한 [API 검사기 추적](api-management-howto-api-inspector.md)을 살펴보면 속성 값이 삽입된 두 개의 `set-header` 정책과 정책 식을 포함하는 속성에 대한 정책 식 계산을 볼 수 있습니다.

![API 검사기 추적][api-management-api-inspector-trace]

속성 값에 정책 식을 포함할 수 있지만 속성 값에 다른 명명된 값을 포함할 수는 없습니다. 속성 참조를 포함하는 텍스트가 속성 값에 사용된 경우(예: `Property value text {{MyProperty}}`) 해당 속성 참조는 바뀌지 않으며 속성 값의 일부로 포함됩니다.

## <a name="next-steps"></a>다음 단계
* 정책 작업에 대한 자세한 정보
  * [API Management의 정책](api-management-howto-policies.md)
  * [정책 참조](/azure/api-management/api-management-policies)
  * [정책 식](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png

