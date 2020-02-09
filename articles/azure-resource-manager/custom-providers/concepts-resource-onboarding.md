---
title: 리소스 온보딩
description: Azure 사용자 지정 공급자를 사용 하 여 다른 Azure 리소스 유형에 관리 또는 구성을 적용 하 여 리소스 온 보 딩을 수행 하는 방법을 알아봅니다.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650410"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Azure 사용자 지정 공급자 리소스 온 보 딩 개요

Azure 사용자 지정 공급자 리소스 온 보 딩은 Azure 리소스 종류에 대 한 확장성 모델입니다. 이를 통해 대규모의 기존 Azure 리소스에서 작업 또는 관리를 적용할 수 있습니다. 자세한 내용은 [Azure 사용자 지정 공급자가 azure를 확장 하는 방법](overview.md)을 참조 하세요. 이 문서에서는 다음을 설명합니다.

- 온 보 딩 할 수 있는 리소스입니다.
- 리소스 온 보 딩 기본 사항 및 사용 방법입니다.
- 시작 하는 데 도움이 되는 가이드 및 코드 샘플을 찾을 수 있는 위치입니다.

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 특정 기능은 지원 되지 않거나 제한 된 기능을 가질 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-can-resource-onboarding-do"></a>리소스 온 보 딩은 무엇을 할 수 있나요?

[Azure 사용자 지정 공급자 사용자 지정 리소스](./custom-providers-resources-endpoint-how-to.md)와 마찬가지로, 리소스 온 보 딩은 엔드포인트에 대 한 "온 보 딩" 요청을 프록시 하는 계약을 정의 합니다. 사용자 지정 리소스와 달리 리소스 온 보 딩은 새 리소스 종류를 만들지 않습니다. 대신 기존 리소스 종류의 확장을 허용 합니다. 및 리소스 온 보 딩은 Azure Policy에서 작동 하므로 리소스의 관리 및 구성이 대규모로 수행 될 수 있습니다. 리소스 온 보 딩 워크플로의 몇 가지 예:

- 가상 머신 확장을 설치 하 고 관리 합니다.
- Azure storage 계정에 대 한 기본값을 업로드 하 고 구성 합니다.
- 대규모로 기준 진단 설정을 사용 하도록 설정 합니다.

## <a name="resource-onboarding-basics"></a>리소스 온 보 딩 기본 사항

Microsoft. CustomProviders/resourceProviders 및 Microsoft. CustomProviders/association 리소스 유형을 사용 하 여 Azure 사용자 지정 공급자를 통해 리소스 온 보 딩을 구성 합니다. 사용자 지정 공급자에 대 한 리소스 온 보 딩을 사용 하도록 설정 하려면 구성 프로세스 중에 "Extension"을 포함 하는 **Routingtype** 을 사용 하 여 "association" 이라는 **resourceType** 을 만듭니다. Microsoft. CustomProviders/association 및 Microsoft. CustomProviders/resourceProviders는 동일한 리소스 그룹에 속할 필요가 없습니다.

샘플 Azure 사용자 지정 공급자는 다음과 같습니다.

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

속성 | 필수 여부 | Description
---|---|---
name | 예 | 엔드포인트 정의의 이름입니다. 리소스 온 보 딩의 경우 이름은 "연결" 이어야 합니다.
routingType | 예 | 엔드포인트을 사용 하 여 계약 유형을 결정 합니다. 리소스 온 보 딩의 경우 유효한 **Routingtypes** 는 "프록시, 캐시, 확장" 및 "Webhook, 캐시, 확장"입니다.
엔드포인트(endpoint) | 예 | 요청을 라우팅하는 엔드포인트입니다. 이렇게 하면 응답 및 요청의 부작용이 처리 됩니다.

연결 리소스 형식을 사용 하 여 사용자 지정 공급자를 만든 후에는 Microsoft. CustomProviders/association을 사용 하 여 대상으로 지정할 수 있습니다. Microsoft. CustomProviders/association은 다른 Azure 리소스를 확장할 수 있는 확장 리소스입니다. Microsoft. CustomProviders/association의 인스턴스를 만든 경우에는 **targetResourceId**속성을 사용 합니다 .이 속성은 유효한 Microsoft Customproviders/resourceproviders 또는 Microsoft. Solutions/APPLICATIONS 리소스 ID 여야 합니다. 이러한 경우, 사용자가 만든 Microsoft CustomProviders/resourceProviders 인스턴스의 연결 리소스 형식으로 요청이 전달 됩니다.

> [!NOTE]
> **TargetResourceId**로 제공 되는 Microsoft. Solutions/APPLICATIONS 리소스 ID는 "public" 이름의 관리 되는 리소스 그룹에 배포 되어 있어야 합니다.

샘플 Azure 사용자 지정 공급자 연결:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

속성 | 필수 여부 | Description
---|---|---
targetResourceId | 예 | Microsoft. CustomProviders/resourceProviders 또는 Microsoft. Solutions/applications의 리소스 ID입니다.

## <a name="how-to-use-resource-onboarding"></a>리소스 온 보 딩 사용 방법

리소스 등록은 Microsoft. CustomProviders/association 확장 리소스를 사용 하 여 다른 리소스를 확장 하는 방식으로 작동 합니다. 다음 샘플에서는 가상 머신에 대 한 요청이 수행 되지만 모든 리소스를 확장할 수 있습니다.

먼저 연결 리소스 형식을 사용 하 여 사용자 지정 공급자 리소스를 만들어야 합니다. 이렇게 하면 사용자 지정 공급자를 대상으로 하는 해당 Microsoft CustomProviders/association 리소스를 만들 때 사용 되는 콜백 URL이 선언 됩니다.

샘플 Microsoft. CustomProviders/resourceProviders에서 요청을 만듭니다.

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

사용자 지정 공급자를 만든 후 다른 리소스를 대상으로 지정 하 고 사용자 지정 공급자의 의도 하지 않은 결과를 적용할 수 있습니다.

샘플 Microsoft. CustomProviders/association 만들기 요청:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

그런 다음이 요청은 사용자가 만든 사용자 지정 공급자에 지정 된 엔드포인트으로 전달 됩니다 .이는 **targetResourceId** 에서이 폼에 의해 참조 됩니다.

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

엔드포인트은 application/json `Content-Type` 및 유효한 JSON 응답 본문으로 응답 해야 합니다. JSON의 **properties** 개체 아래에서 반환 되는 필드는 연결 반환 응답에 추가 됩니다.

## <a name="getting-help"></a>도움말 보기

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)에 요청 해 보세요. 이미 비슷한 질문과 그에 대한 답변이 게시되었을 수도 있으므로, 먼저 확인한 후 게시하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 자세히 알아보려면 다음 문서를 참조 하세요.

- [자습서: 사용자 지정 공급자를 사용 하 여 리소스 온 보 딩](./tutorial-resource-onboarding.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [빠른 시작: 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
