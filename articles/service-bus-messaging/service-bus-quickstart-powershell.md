---
title: Azure PowerShell을 사용하여 Service Bus 큐 만들기
description: 이 빠른 시작에서는 Azure PowerShell을 사용하여 Service Bus 네임스페이스 및 네임스페이스에 큐를 만드는 방법에 대해 알아봅니다.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 178f990e46801cd51e9feb88bbd20181842e4400
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077690"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Azure PowerShell을 사용하여 Service Bus 네임스페이스 및 큐 만들기
이 빠른 시작에서는 Azure PowerShell을 사용하여 Service Bus 네임스페이스 및 큐를 만드는 방법을 보여줍니다. 또한 클라이언트 애플리케이션이 큐에 메시지를 보내고 받는 데 사용할 수 있는 권한 부여 자격 증명을 가져오는 방법을 보여줍니다. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 Azure 구독이 있는지 확인합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정][]을 만들 수 있습니다. 

이 빠른 시작에서는 Azure Portal에 로그인한 후에 시작할 수 있는 Azure Cloud Shell을 사용합니다. Azure Cloud Shell에 대한 자세한 내용은 [Azure Cloud Shell 개요](../cloud-shell/overview.md)를 참조하세요. 머신에 Azure PowerShell을 [설치](/powershell/azure/install-Az-ps)하고 사용할 수도 있습니다. 


## <a name="provision-resources"></a>리소스 프로비전
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 다음 이미지에 표시된 아이콘을 선택하여 Azure Cloud Shell을 시작합니다. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell 시작":::
3. 아래쪽 Cloud Shell 창의 **Bash**에서 **PowerShell**로 전환합니다. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="PowerShell 모드로 전환":::    
4. 다음 명령을 실행하여 Azure 리소스 그룹을 만듭니다. 원하는 경우 리소스 그룹 이름 및 위치를 업데이트합니다. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. 다음 명령을 입력하여 Service Bus 메시징 네임스페이스를 만듭니다. 이 예제에서 `ContosoRG`는 이전 단계에서 만든 리소스 그룹입니다. `ContosoSBusNS`는 Service 해당 리소스 그룹에서 만든 Bus 네임스페이스의 이름입니다. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. 다음을 실행하여 이전 단계에서 만든 네임스페이스에 큐를 만듭니다. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. 네임스페이스에 대한 기본 연결 문자열을 가져옵니다. 이 연결 문자열을 사용하여 큐에 연결하고 메시지를 보내고 받습니다. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    연결 문자열과 큐 이름을 적어 둡니다. 메시지를 보내고 받는 데 사용합니다. 


## <a name="next-steps"></a>다음 단계
이 문서에서는 Service Bus 네임스페이스와 네임스페이스에 큐를 만들었습니다. 큐에 메시지를 보내거나 받는 방법에 대한 자세한 내용은 **메시지 보내기 및 받기** 섹션에서 다음 빠른 시작 중 하나를 참조하세요. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues-new-package.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[무료 계정]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

