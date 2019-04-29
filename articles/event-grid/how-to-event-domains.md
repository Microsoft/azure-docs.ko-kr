---
title: 이벤트 도메인을 사용하여 Azure Event Grid에서 대규모 토픽 집합 관리
description: Azure Event Grid에서 이벤트 도메인을 사용하여 대규모 토픽 집합을 관리하고 토픽에 이벤트를 게시하는 방법을 보여줍니다.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: c49044d8bd96efb7e86cf54509c32033900be305
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561785"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>이벤트 도메인을 사용하여 토픽을 관리하고 이벤트를 게시하는 방법

이 문서는 다음 방법을 안내합니다.

* Event Grid 도메인 만들기
* Event Grid 토픽 구독
* 키 나열
* 도메인에 이벤트 게시

이벤트 도메인에 대해 알아보려면 [Event Grid 토픽을 관리하는 이벤트 도메인 이해](event-domains.md)를 참조하세요.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>미리 보기 기능 설치

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>이벤트 도메인 만들기

대규모 토픽 집합을 관리하려면 이벤트 도메인을 만듭니다.

Azure CLI의 경우 

```azurecli-interactive
# If you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name> \
  -l <location>
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain-name> `
  -Location <location>
```

성공적으로 만들어지면 다음 값이 반환됩니다.

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

`endpoint` 및 `id`를 기록해 둡니다. 도메인을 관리하고 이벤트를 게시하는 데 필요합니다.

## <a name="manage-access-to-topics"></a>토픽에 대한 액세스 관리

토픽에 대한 액세스 관리는 [역할 할당](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli)을 통해 수행됩니다. 역할 할당은 역할 기반 액세스 제어를 사용하여 Azure 리소스에 대한 작업을 특정 범위에서 권한이 부여된 사용자로 제한합니다.

Event Grid는 도메인 내 다양한 토픽에 대한 특정 사용자 액세스를 할당하는 데 사용할 수 있는 두 가지 기본 제공 역할을 제공합니다. 이러한 역할은 구독을 만들고 삭제할 수 있는 `EventGrid EventSubscription Contributor (Preview)` 및 이벤트 구독을 나열하는 것만 가능한 `EventGrid EventSubscription Reader (Preview)`입니다.

다음 Azure CLI 명령은 `demotopic1` 토픽에만 이벤트 구독을 만들고 삭제하도록 `alice@contoso.com`을 제한합니다.

```azurecli-interactive
az role assignment create \
  --assignee alice@contoso.com \
  --role "EventGrid EventSubscription Contributor (Preview)" \
  --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

다음 PowerShell 명령은 `demotopic1` 토픽에만 이벤트 구독을 만들고 삭제하도록 `alice@contoso.com`을 제한합니다.

```azurepowershell-interactive
New-AzureRmRoleAssignment `
  -SignInName alice@contoso.com `
  -RoleDefinitionName "EventGrid EventSubscription Contributor (Preview)" `
  -Scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1
```

Event Grid 작업에 대한 액세스를 관리하는 방법에 대한 자세한 내용은 [Event Grid 보안 및 인증](./security-authentication.md)을 참조하세요.

## <a name="create-topics-and-subscriptions"></a>토픽 및 구독 만들기

Event Grid 서비스는 도메인 토픽에 대한 이벤트 구독 만들기 호출에 따라 자동으로 도메인에 해당 토픽을 만들고 관리합니다. 도메인에 토픽을 만드는 별도의 단계가 없습니다. 마찬가지로, 토픽의 마지막 이벤트 구독을 삭제하면 토픽도 삭제됩니다.

도메인에서 토픽을 구독하는 방법은 다른 Azure 리소스를 구독하는 방법과 동일합니다. 원본 리소스 ID의 경우 이전에 도메인을 만들 때 반환된 이벤트 도메인 ID를 지정합니다. 구독할 토픽을 지정하려면 원본 리소스 ID의 끝에 `/topics/<my-topic>`을 추가합니다. 도메인의 모든 이벤트를 수신하는 도메인 범위 이벤트 구독을 만들려면 모든 토픽을 지정하지 않고 이벤트 도메인 ID를 지정합니다.

일반적으로 이전 섹션에서 액세스 권한을 부여한 사용자가 구독을 만듭니다. 이 문서를 간소화하기 위해 구독을 만듭니다. 

Azure CLI의 경우 

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --source-resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" \
  --endpoint https://contoso.azurewebsites.net/api/updates
```

PowerShell의 경우 다음을 사용합니다.

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/demotopic1" `
  -EventSubscriptionName <event-subscription> `
  -Endpoint https://contoso.azurewebsites.net/api/updates
```

이벤트를 구독할 테스트 엔드포인트가 필요한 경우 들어오는 이벤트를 표시하는 [미리 빌드된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 언제든지 배포할 수 있습니다. `https://<your-site-name>.azurewebsites.net/api/updates`에서 테스트 웹 사이트에 이벤트를 보낼 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>

토픽에 대해 설정되고 Azure Active Directory에 저장된 권한은 명시적으로 삭제해야 합니다. 구독이 토픽에 대한 쓰기 액세스 권한을 갖고 있는 경우 이벤트 구독을 삭제해도 이벤트 구독을 만드는 사용자 액세스 권한이 철회되지 않습니다.


## <a name="publish-events-to-an-event-grid-domain"></a>Event Grid 도메인에 이벤트 게시

도메인에 이벤트를 게시하는 방법은 [사용자 지정 토픽에 게시](./post-to-custom-topic.md)하는 방법과 동일합니다. 그러나 사용자 지정 항목에 게시하는 대신 모든 이벤트를 도메인 엔드포인트에 게시합니다. JSON 이벤트 데이터에서 이벤트를 이동할 항목을 지정합니다. 다음 이벤트 배열을 게시하면 `"id": "1111"`인 이벤트는 `demotopic1` 토픽으로 전송되고 `"id": "2222"`인 이벤트는 `demotopic2` 토픽으로 전송됩니다.

```json
[{
  "topic": "demotopic1",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "demotopic2",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Azure CLI로 도메인 엔드포인트를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid domain show \
  -g <my-resource-group> \
  -n <my-domain>
```

도메인에 사용할 키를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

PowerShell로 도메인 엔드포인트를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridDomain `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

도메인에 사용할 키를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzureRmEventGridDomainKey `
  -ResourceGroupName <my-resource-group> `
  -Name <my-domain>
```

그런 다음, 선호하는 방법으로 HTTP POST를 만들어서 Event Grid 도메인에 이벤트를 게시합니다.

## <a name="next-steps"></a>다음 단계

* 이벤트 도메인의 간략한 개념과 유용한 이유에 대한 내용은 [이벤트 도메인의 개념적 개요](event-domains.md)를 참조하세요.
