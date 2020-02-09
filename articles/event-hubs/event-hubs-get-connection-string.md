---
title: 연결 문자열 가져오기 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 클라이언트가 Azure Event Hubs에 연결하는 데 사용할 수 있는 연결 문자열을 가져오기 위한 지침을 제공합니다.
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: 744151a1ce8cde630e26c17ccf06569ebd0efb61
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771005"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs 연결 문자열 가져오기

Event Hubs를 사용하려면 Event Hubs 네임스페이스를 만들어야 합니다. 네임스페이스는 여러 이벤트 허브 또는 Kafka 토픽에 대한 범위 지정 컨테이너입니다. 이 네임스페이스는 고유한 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)을 제공합니다. 네임스페이스가 만들어지면 Event Hubs와 통신하는 데 필요한 연결 문자열을 가져올 수 있습니다.

Azure Event Hubs에 대한 연결 문자열에 포함되는 구성 요소는 다음과 같습니다.

* FQDN = 만든 EventHubs 네임스페이스의 FQDN(EventHubs 네임스페이스 이름 뒤에 servicebus.windows.net을 추가해서 생성됨)
* SharedAccessKeyName = 애플리케이션의 SAS 키에 대해 선택한 이름
* SharedAccessKey = 키에 대해 생성된 값

연결 문자열 템플릿은 다음과 같습니다.
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

연결 문자열의 예제는 `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`와 같을 수 있습니다.

이 문서에서는 연결 문자열을 가져오는 다양한 방법을 안내합니다.

## <a name="get-connection-string-from-the-portal"></a>포털에서 연결 문자열 가져오기
1. [Azure 포털](https://portal.azure.com)에 로그인합니다. 
2. 왼쪽 탐색 메뉴에서 **모든 서비스**를 선택합니다. 
3. **Analytics** 섹션에서 **Event Hubs**를 선택합니다. 
4. 이벤트 허브 목록에서 해당 이벤트 허브를 선택합니다.
6. **Event Hubs 네임스페이스** 페이지의 왼쪽 메뉴에서 **공유 액세스 정책**을 선택합니다.

    ![공유 액세스 정책 메뉴 항목](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. 정책 목록에서 **공유 액세스 정책**을 선택합니다. 기본 이름은 **RootManageSharedAccessPolicy**입니다. 적절한 권한(읽기, 쓰기)으로 정책을 추가하고 해당 정책을 사용할 수 있습니다. 

    ![Event Hubs 공유 액세스 정책](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. **연결 문자열 기본 키** 필드 옆에 있는 **복사** 단추를 선택합니다. 

    ![Event Hubs - 연결 문자열 가져오기](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>Azure PowerShell을 사용하여 연결 문자열 가져오기

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Get-AzEventHubNamespaceKey](/powershell/module/az.eventhub/get-azeventhubkey)를 사용하여 아래와 같은 특정 정책/규칙 이름에 대한 연결 문자열을 가져올 수 있습니다.

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>Azure CLI를 사용하여 연결 문자열 가져오기
다음을 사용하여 네임스페이스에 대한 연결 문자열을 가져올 수 있습니다.

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

또는 다음을 사용 하 여 EventHub 엔터티에 대한 연결 문자열을 가져올 수 있습니다.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Event Hubs에 대한 Azure CLI 명령과 관련된 자세한 내용은 [Event Hubs용 Azure CLI](/cli/azure/eventhubs)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Event Hubs에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Event Hubs 개요](event-hubs-what-is-event-hubs.md)
* [이벤트 허브 만들기](event-hubs-create.md)
