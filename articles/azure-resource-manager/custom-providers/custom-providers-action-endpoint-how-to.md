---
title: Azure REST API에 사용자 지정 작업 추가
description: Azure REST API에 사용자 지정 작업을 추가 하는 방법에 대해 알아봅니다. 이 문서에서는 사용자 지정 작업을 구현 하려는 엔드포인트에 대 한 요구 사항 및 모범 사례를 안내 합니다.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650397"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Azure REST API에 사용자 지정 작업 추가

이 문서에서는 사용자 지정 작업을 구현 하는 Azure 사용자 지정 리소스 공급자 엔드포인트을 만들기 위한 요구 사항 및 모범 사례를 살펴보겠습니다. Azure 사용자 지정 리소스 공급자에 대해 잘 모르는 경우 [사용자 지정 리소스 공급자에 대 한 개요](overview.md)를 참조 하세요.

## <a name="how-to-define-an-action-endpoint"></a>작업 엔드포인트을 정의 하는 방법

**엔드포인트** 은 서비스를 가리키는 URL로, 서비스와 Azure 간의 기본 계약을 구현 합니다. 엔드포인트은 사용자 지정 리소스 공급자에 정의 되며 공개적으로 액세스할 수 있는 URL 일 수 있습니다. 아래 샘플에는 `endpointURL`에서 구현 하는 `myCustomAction` 이라는 **작업이** 있습니다.

샘플 **ResourceProvider**:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>작업 엔드포인트 빌드

**작업** 을 구현 하는 **엔드포인트** 은 Azure의 새 API에 대 한 요청 및 응답을 처리 해야 합니다. **작업** 을 포함 하는 사용자 지정 리소스 공급자를 만들면 Azure에서 새로운 api 집합이 생성 됩니다. 이 경우 작업은 `POST` 호출에 대 한 새 Azure 작업 API를 생성 합니다.

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Azure API 들어오는 요청:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

이 요청은 다음 형식으로 **엔드포인트** 에 전달 됩니다.

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

마찬가지로, **엔드포인트** 의 응답은 고객에 게 다시 전달 됩니다. 엔드포인트의 응답은 다음을 반환 해야 합니다.

- 유효한 JSON 개체 문서입니다. 모든 배열 및 문자열은 최상위 개체 아래에 중첩 되어야 합니다.
- `Content-Type` 헤더를 "application/json;으로 설정 해야 합니다. charset = utf-8 ".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Azure 사용자 지정 리소스 공급자 응답:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>사용자 지정 동작 호출

사용자 지정 리소스 공급자에서 사용자 지정 작업을 호출 하는 두 가지 주요 방법이 있습니다.

- Azure CLI
- Azure 리소스 관리자 템플릿

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

매개 변수 | 필수 | Description
---|---|---
action | *예* | **ResourceProvider**에 정의 된 동작의 이름입니다.
ids | *예* | **ResourceProvider**의 리소스 ID입니다.
request-body | *아니요* | **엔드포인트**으로 전송 되는 요청 본문입니다.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

> [!NOTE]
> 작업은 Azure Resource Manager 템플릿에서 제한적으로 지원 됩니다. 템플릿 내에서 동작을 호출 하려면 해당 이름에 [`list`](../templates/template-functions-resource.md#list) 접두사를 포함 해야 합니다.

List 작업을 사용 하는 샘플 **ResourceProvider** :

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

샘플 Azure Resource Manager 템플릿:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

매개 변수 | 필수 | Description
---|---|---
resourceIdentifier | *예* | **ResourceProvider**의 리소스 ID입니다.
apiVersion | *예* | 리소스 런타임의 API 버전입니다. 이는 항상 "2018-09-01-preview" 여야 합니다.
functionValues | *아니요* | **엔드포인트**으로 전송 되는 요청 본문입니다.

## <a name="next-steps"></a>다음 단계

- [Azure 사용자 지정 리소스 공급자에 대 한 개요](overview.md)
- [빠른 시작: Azure 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
