---
title: Azure API Management 정책에서 명명 된 값을 사용 하는 방법
description: Azure API Management 정책에서 명명 된 값을 사용 하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 7c25455e28e57ff40664a69718a2e406b52b7632
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834305"
---
# <a name="how-to-use-named-values-in-azure-api-management-policies"></a>Azure API Management 정책에서 명명 된 값을 사용 하는 방법

API Management 정책은 구성을 통해 Azure Portal에서 API 동작을 변경하도록 하는 시스템의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 정책 설명은 리터럴 텍스트 값, 정책 식 및 명명된 값을 사용하여 생성할 수 있습니다.

각 API Management 서비스 인스턴스에는 서비스 인스턴스에 대한 전역 인 키/값 쌍의 컬렉션 (명명 된 값 이라고 함)이 있습니다. 컬렉션의 항목 수에는 제한이 없습니다. 명명 된 값은 모든 API 구성 및 정책에서 상수 문자열 값을 관리 하는 데 사용할 수 있습니다. 각 명명 된 값에는 다음과 같은 특성이 있을 수 있습니다.

| attribute      | 유형            | Description                                                                                                                            |
| -------------- | --------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| `Display name` | 문자열          | 정책에서 명명 된 값을 참조 하는 데 사용 됩니다. 1 ~ 256 자의 문자열입니다. 문자, 숫자, 점 및 대시만 사용할 수 있습니다. |
| `Value`        | 문자열          | 실제 값입니다. 비어 있거나 공백으로만 구성 되지 않아야 합니다. 최대 4096 문자 길이입니다.                                        |
| `Secret`       | boolean         | 값이 암호인지, 그리고 암호화해야 하는지 여부를 결정합니다.                                                               |
| `Tags`         | 문자열의 배열 | 명명 된 값 목록을 필터링 하는 데 사용 됩니다. 최대 32 태그입니다.                                                                                    |

![명명된 값](./media/api-management-howto-properties/named-values.png)

명명 된 값에는 리터럴 문자열과 [정책 식이](/azure/api-management/api-management-policy-expressions)포함 될 수 있습니다. 예를 들어 `Expression` 값은 현재 날짜 및 시간이 포함된 문자열을 반환하는 정책 식입니다. `Credential` 명명 된 값은 암호로 표시 되므로 해당 값은 기본적으로 표시 되지 않습니다.

| 이름       | 값                      | 비밀 | 태그          |
| ---------- | -------------------------- | ------ | ------------- |
| 값      | 42                         | 거짓  | 중요 번호 |
| 자격 증명 | ••••••••••••••••••••••     | 참   | 보안      |
| Expression | @(DateTime.Now.ToString()) | 거짓  |               |

> [!NOTE]
> API Management 서비스 내에 저장 된 명명 된 값 대신이 [예제](https://github.com/Azure/api-management-policy-snippets/blob/master/examples/Look%20up%20Key%20Vault%20secret%20using%20Managed%20Service%20Identity.policy.xml)에서 보여 주는 것 처럼 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 서비스에 저장 된 값을 사용할 수 있습니다.

## <a name="to-add-and-edit-a-named-value"></a>명명 된 값을 추가 하 고 편집 하려면

![명명 된 값 추가](./media/api-management-howto-properties/add-property.png)

1. **API Management**에서 **API**를 선택합니다.
2. **명명된 값**을 선택합니다.
3. **+추가**를 누릅니다.

    이름 및 값은 필수 값입니다. Value가 secret 인 경우 _비밀입니다_ . 확인란을 선택 합니다. 명명된 값을 구성하는 데 도움이 되도록 하나 이상의 선택적 태그를 입력하고 저장을 클릭합니다.

4. **만들기**를 클릭합니다.

명명 된 값을 만든 후에는 해당 값을 클릭 하 여 편집할 수 있습니다. 명명 된 값 이름을 변경 하면 해당 명명 된 값을 참조 하는 모든 정책이 새 이름을 사용 하도록 자동으로 업데이트 됩니다.

REST API 사용 하 여 명명 된 값을 편집 하는 방법에 대한 자세한 내용은 [REST API를 사용 하 여 명명 된 값 편집](/rest/api/apimanagement/2019-01-01/property?patch)을 참조 하세요.

## <a name="to-delete-a-named-value"></a>명명 된 값을 삭제 하려면

명명 된 값을 삭제 하려면 명명 된 값 옆에 있는 **삭제** 를 클릭 하 여 삭제 합니다.

> [!IMPORTANT]
> 명명 된 값을 정책에서 참조 하는 경우 해당 값을 사용 하는 모든 정책에서 명명 된 값을 제거할 때까지 해당 값을 삭제할 수 없습니다.

REST API 사용 하 여 명명 된 값을 삭제 하는 방법에 대한 자세한 내용은 [REST API를 사용 하 여 명명 된 값 삭제](/rest/api/apimanagement/2019-01-01/property/delete)를 참조 하세요.

## <a name="to-search-and-filter-named-values"></a>명명 된 값을 검색 하 고 필터링 하려면

**명명된 값** 탭에는 명명된 값을 관리하는 데 유용한 검색 및 필터링 기능이 있습니다. 이름으로 명명 된 값 목록을 필터링 하려면 **검색 속성** 텍스트 상자에 검색 용어를 입력 합니다. 모든 명명된 값을 표시하려면 **속성 검색** 텍스트 상자를 지우고 Enter 키를 누릅니다.

태그를 기준으로 목록을 필터링 하려면 태그로 **필터링** 텍스트 상자에 태그를 하나 이상 입력 합니다. 모든 명명된 값을 표시하려면 **태그로 필터링** 텍스트 상자를 지우고 Enter 키를 누릅니다.

## <a name="to-use-a-named-value"></a>명명 된 값을 사용 하려면

정책에 명명 된 값을 사용 하려면 다음 예제와 같이 `{{ContosoHeader}}`와 같이 이중 중괄호 안에 이름을 입력 합니다.

```xml
<set-header name="{{ContosoHeader}}" exists-action="override">
  <value>{{ContosoHeaderValue}}</value>
</set-header>
```

이 예제에서 `ContosoHeader`는 `set-header` 정책의 헤더 이름으로 사용되고, `ContosoHeaderValue`는 해당 헤더의 값으로 사용됩니다. 이 정책이 API Management 게이트웨이에 대한 요청 또는 응답 중에 평가 되 면 `{{ContosoHeader}}` 및 `{{ContosoHeaderValue}}` 해당 값으로 대체 됩니다.

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

명명 된 값을 사용 하 여 이전의 두 샘플 정책을 포함 하는 호출에 대한 [API 검사기 추적](api-management-howto-api-inspector.md) 을 살펴보면 명명 된 값이 삽입 된 두 개의 `set-header` 정책과 정책 식을 포함 하는 명명 된 값에 대한 정책 식 평가를 확인할 수 있습니다.

![API 검사기 추적][api-management-api-inspector-trace]

명명 된 값에는 정책 식이 포함 될 수 있지만 다른 명명 된 값은 포함할 수 없습니다. `Text: {{MyProperty}}`와 같은 명명 된 값 참조를 포함 하는 텍스트가 값에 사용 되는 경우 해당 참조를 확인 하 고 대체 하지 않습니다.

## <a name="next-steps"></a>다음 단계

-   정책 작업에 대한 자세한 정보
    -   [API Management의 정책](api-management-howto-policies.md)
    -   [정책 참조](/azure/api-management/api-management-policies)
    -   [정책 식](/azure/api-management/api-management-policy-expressions)

[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png
