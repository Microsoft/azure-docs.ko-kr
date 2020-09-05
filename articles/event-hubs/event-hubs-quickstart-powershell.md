---
title: '빠른 시작: PowerShell을 사용하여 이벤트 허브 만들기 - Azure Event Hubs'
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 이벤트 허브를 만든 다음, .NET 표준 SDK를 사용하여 이벤트를 수신하는 방법을 설명합니다.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 1c81024a9195e463eeaaf384d5cbbd1e0054c485
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88933956"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 이벤트 허브 만들기

Azure Event Hubs는 초당 수백만 개의 이벤트를 수신하여 처리할 수 있는 빅 데이터 스트리밍 플랫폼이자 이벤트 수집 서비스입니다. Event Hubs는 분산된 소프트웨어와 디바이스에서 생성된 이벤트, 데이터 또는 원격 분석을 처리하고 저장할 수 있습니다. Event Hub로 전송된 데이터는 실시간 분석 공급자 또는 일괄 처리/스토리지 어댑터를 사용하여 변환하고 저장할 수 있습니다. Event Hubs에 대한 자세한 개요는 [Event Hubs 개요](event-hubs-about.md) 및 [Event Hubs 기능](event-hubs-features.md)을 참조하세요.

빠른 시작에서 Azure PowerShell을 사용하여 이벤트 허브를 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 완료하려면 다음이 설치되어 있어야 합니다.

- 동작합니다. 구독이 없으면 시작하기 전에 [계정을 만드세요][].
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 버전 2.0 이상

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 사용 중인 경우이 빠른 시작을 완료하려면 PowerShell의 최신 버전을 실행해야 합니다. 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스의 논리적 컬렉션으로, 이벤트 허브를 만들려면 리소스 그룹이 필요합니다. 

다음 예제에서는 미국 동부 지역의 리소스 그룹을 만듭니다. 사용할 리소스 그룹의 이름과 위치로 `myResourceGroup`을 바꿉니다.

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs 네임스페이스 만들기

리소스 그룹을 만든 후에는 해당 리소스 그룹 안에 Event Hubs 네임스페이스를 만듭니다. Event Hubs 네임스페이스는 이벤트 허브를 만들 수 있는 정규화된 고유의 도메인 이름을 제공합니다. `namespace_name`을 네임스페이스의 고유한 이름으로 바꿉니다.

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

Event Hubs 네임스페이스가 있으므로 이제 해당 네임스페이스 안에 이벤트 허브를 만듭니다.  
`MessageRetentionInDays`에 대해 허용된 기간은 1에서 7일 사이입니다.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

축하합니다! Azure PowerShell을 사용하여 Event Hubs 네임스페이스와, 그 네임스페이스 안에 이벤트 허브를 만들었습니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Event Hubs 네임스페이스를 만들었으며, 이벤트 허브에서 이벤트를 보내고 받기 위해 애플리케이션 예제를 사용했습니다. 이벤트 허브에서 이벤트를 보내거나 받기 위한 단계별 지침은 **이벤트 보내기 및 받기** 자습서를 참조하세요. 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C(보내기 전용)](event-hubs-c-getstarted-send.md)
- [Apache Storm(받기 전용)](event-hubs-storm-getstarted-receive.md)


[계정을 만드세요]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
