---
title: 사용자 지정 공급자 개요
description: Azure 사용자 지정 리소스 공급자 및 워크플로에 맞게 Azure API 평면을 확장 하는 방법에 대해 알아봅니다.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650488"
---
# <a name="azure-custom-resource-providers-overview"></a>Azure 사용자 지정 리소스 공급자 개요

Azure 사용자 지정 리소스 공급자는 Azure에 대 한 확장성 플랫폼입니다. 기본 Azure 환경을 보강 하는 데 사용할 수 있는 사용자 지정 Api를 정의할 수 있습니다. 이 문서에서는 다음에 대해 설명 합니다.

- Azure 사용자 지정 리소스 공급자를 빌드하고 배포 하는 방법을 설명 합니다.
- Azure 사용자 지정 리소스 공급자를 활용 하 여 기존 워크플로를 확장 하는 방법입니다.
- 시작 하는 데 도움이 되는 가이드 및 코드 샘플을 찾을 수 있는 위치입니다.

![사용자 지정 공급 기업 개요](./media/overview/overview.png)

> [!IMPORTANT]
> 사용자 지정 공급자는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="what-can-custom-resource-providers-do"></a>사용자 지정 리소스 공급자가 수행할 수 있는 작업

Azure 사용자 지정 리소스 공급자를 사용 하 여 달성할 수 있는 작업의 몇 가지 예는 다음과 같습니다.

- 내부 및 외부 서비스를 포함 하도록 Azure Resource Manager REST API를 확장 합니다.
- 기존 Azure 워크플로 위에 사용자 지정 시나리오를 사용 하도록 설정 합니다.
- Azure Resource Manager 템플릿 컨트롤 및 효과를 사용자 지정 합니다.

## <a name="what-is-a-custom-resource-provider"></a>사용자 지정 리소스 공급자 정의

Azure 사용자 지정 리소스 공급자는 Azure와 엔드포인트 간에 계약을 만들어 수행 합니다. 이 계약은 새 리소스 ( **Microsoft. customproviders/resourceProviders**)를 통해 새 리소스 및 작업 목록을 정의 합니다. 그러면 사용자 지정 리소스 공급자는 Azure에서 이러한 새 Api를 노출 합니다. Azure 사용자 지정 리소스 공급자는 사용자 지정 리소스 공급자, **엔드포인트**및 사용자 지정 리소스의 세 부분으로 구성 됩니다.

## <a name="how-to-build-custom-resource-providers"></a>사용자 지정 리소스 공급자를 빌드하는 방법

사용자 지정 리소스 공급자는 Azure와 엔드포인트 간의 계약 목록입니다. 이 계약은 Azure에서 엔드포인트과 상호 작용 하는 방법을 설명 합니다. 리소스 공급자는 프록시와 같은 역할을 하며 지정 된 **엔드포인트**에 요청 및 응답을 전달 합니다. 리소스 공급자는 두 가지 유형의 계약 ( [**Resourcetypes**](./custom-providers-resources-endpoint-how-to.md) 및 [**작업**](./custom-providers-action-endpoint-how-to.md))을 지정할 수 있습니다. 이러한 설정은 엔드포인트 정의를 통해 사용 하도록 설정 됩니다. 엔드포인트 정의는 **이름**, **routingtype**및 **엔드포인트**의 세 필드로 구성 됩니다.

샘플 엔드포인트:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

속성 | 필수 | Description
---|---|---
name | *예* | 엔드포인트 정의의 이름입니다. Azure는 이 이름을 API를 통해 '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}' 아래에 노출합니다.
routingType | *아니요* | **엔드포인트**을 사용 하 여 계약 형식을 결정 합니다. 지정하지 않는 경우 기본값은 "Proxy"입니다.
엔드포인트(endpoint) | *예* | 요청을 라우팅하는 엔드포인트입니다. 응답은 물론 요청의 부작용도 처리합니다.

### <a name="building-custom-resources"></a>사용자 지정 리소스 빌드

**Resourcetypes** 은 Azure에 추가 되는 새로운 사용자 지정 리소스를 설명 합니다. 이러한 메서드는 기본 RESTful CRUD 메서드를 노출 합니다. [사용자 지정 리소스 만들기에 대 한 자세한 정보](./custom-providers-resources-endpoint-how-to.md)

**Resourcetypes**을 사용 하는 샘플 사용자 지정 리소스 공급자:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

위의 샘플에 대해 Azure에 추가 된 Api:

HttpMethod | 샘플 URI | Description
---|---|---
PUT | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API를 호출 하 여 새 리소스를 만듭니다.
Delete | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API 호출 하 여 기존 리소스를 삭제 합니다.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | Azure REST API 호출 하 여 기존 리소스를 검색 합니다.
GET | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | Azure REST API 호출 하 여 기존 리소스 목록을 검색 합니다.

### <a name="building-custom-actions"></a>사용자 지정 작업 빌드

**작업** 은 Azure에 추가 되는 새 작업을 설명 합니다. 이러한 리소스는 리소스 공급자의 맨 위에 노출 되거나 **resourceType**아래에 중첩 될 수 있습니다. [사용자 지정 작업 만들기에 대 한 자세한 정보](./custom-providers-action-endpoint-how-to.md)

**작업**을 포함 하는 샘플 사용자 지정 리소스 공급자:

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
  "location": "eastus"
}
```

위의 샘플에 대해 Azure에 추가 된 Api:

HttpMethod | 샘플 URI | Description
---|---|---
POST | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | Azure REST API 호출 하 여 작업을 활성화 합니다.

## <a name="looking-for-help"></a>도움말 찾기

Azure 사용자 지정 리소스 공급자 개발에 대 한 질문이 있는 경우 [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers)를 요청 하세요. 이미 비슷한 질문을 받고 답변했을 수 있으므로 게시하기 전에 먼저 확인하세요. 빠른 응답을 얻으려면 ```azure-custom-providers``` 태그를 추가하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자 지정 공급 기업에 대해 알아보았습니다. 다음 문서로 이동하면 사용자 지정 공급 기업을 만들 수 있습니다.

- [빠른 시작: Azure 사용자 지정 리소스 공급자 만들기 및 사용자 지정 리소스 배포](./create-custom-provider.md)
- [자습서: Azure에서 사용자 지정 작업 및 리소스 만들기](./tutorial-get-started-with-custom-providers.md)
- [방법: Azure REST API에 사용자 지정 작업 추가](./custom-providers-action-endpoint-how-to.md)
- [방법: Azure REST API에 사용자 지정 리소스 추가](./custom-providers-resources-endpoint-how-to.md)
