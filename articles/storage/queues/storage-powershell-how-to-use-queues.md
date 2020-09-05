---
title: PowerShell에서 Azure Queue storage 작업 수행
description: PowerShell을 사용 하 여 Azure Queue storage에 대 한 작업을 수행 합니다. Azure Queue storage를 사용 하 여 HTTP/HTTPS에서 액세스할 수 있는 많은 수의 메시지를 저장할 수 있습니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f45a4de2e13f936556f8dd99aa107110edc6e91
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077928"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure Queue Storage 작업 수행

Azure Queue Storage는 HTTP 또는 HTTPS를 통해 전 세계 어디에서나 액세스할 수 있는 다수의 메시지를 저장하기 위한 서비스입니다. 자세한 내용은 [Azure 큐 소개](storage-queues-introduction.md)를 참조하세요. 이 방법 문서에서는 일반 Queue Storage 작업을 설명합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 큐 만들기
> * 큐 검색
> * 메시지 추가
> * 메시지 읽기
> * 메시지 삭제
> * 큐 삭제

이 방법에는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

큐에 대한 데이터 평면의 PowerShell cmdlet는 없습니다. 메시지 추가, 메시지 읽기, 메시지 삭제 등과 같은 데이터 평면 작업을 수행하려면 PowerShell에서 노출되는 .NET 스토리지 클라이언트 라이브러리를 사용해야 합니다. 메시지 개체를 만든 다음 AddMessage 등의 명령을 사용하여 해당 메시지에 대한 작업을 수행합니다. 이 문서에서 이 작업을 수행하는 방법을 보여 줍니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>위치의 목록 검색

사용하려는 위치를 모르는 경우 사용 가능한 위치를 나열할 수 있습니다. 목록이 표시되면 사용할 위치를 찾습니다. 이 연습에서는 **eastus**를 사용합니다. 이를 나중에 사용할 수 있도록 변수 **위치**에 저장합니다.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 리소스 그룹 이름을 나중에 사용할 수 있도록 변수에 저장합니다. 이 예제에서는 *eastus* 지역에 *howtoqueuesrg*라는 리소스 그룹을 만듭니다.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>스토리지 계정 만들기

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)를 사용하여 LRS(로컬 중복 스토리지)에 표준 범용 스토리지 계정을 만듭니다. 사용할 스토리지 계정을 정의하는 스토리지 계정 컨텍스트를 가져옵니다. 스토리지 계정에서 작업할 때 반복적으로 자격 증명을 제공하는 대신 컨텍스트를 참조합니다.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>큐 만들기

다음 예제는 먼저 Storage 계정 이름 및 해당 액세스 키를 포함하는 Storage 계정 컨텍스트를 사용하여 Azure Storage에 대한 연결을 설정합니다. 그런 다음 [AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) cmdlet을 호출 하 여 ' howtoqueue ' 라는 큐를 만듭니다.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Azure 큐 서비스에 대한 명명 규칙에 대해서는 [큐 및 메타데이터 이름 지정](https://msdn.microsoft.com/library/azure/dd179349.aspx)을 참조하세요.

## <a name="retrieve-a-queue"></a>큐 검색

Storage 계정의 특정 큐 또는 모든 큐의 목록을 쿼리하고 검색할 수 있습니다. 다음 예제에서는 스토리지 계정의 모든 큐 및 특정 큐를 검색하는 방법을 보여 줍니다. 두 명령 모두 [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) cmdlet을 사용합니다.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>큐에 메시지 추가

큐의 실제 메시지에 영향을 미치는 작업은 PowerShell에 노출된 대로 .NET 스토리지 클라이언트 라이브러리를 사용합니다. 큐에 메시지를 추가 하려면 [CloudQueueMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) 클래스와 같은 메시지 개체의 새 인스턴스를 만듭니다. 다음으로 [Addmessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) 메서드를 호출 합니다. 문자열(UTF-8 형식) 또는 바이트 배열에서 CloudQueueMessage를 만들 수 있습니다.

다음 예제에서는 큐에 메시지를 추가하는 방법을 보여 줍니다.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

[Azure Storage Explorer](https://storageexplorer.com)를 사용하는 경우 Azure 계정에 연결하여 스토리지 계정에서 큐를 확인하고, 큐에서 메시지를 보도록 하나로 드릴다운할 수 있습니다.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>큐에서 메시지를 읽은 다음, 삭제합니다.

메시지는 선입선출(FIFO) 순서로 읽힙니다. 이는 보장되지 않습니다. 큐에서 메시지를 읽으면 큐를 보는 모든 다른 프로세스는 표시되지 않습니다. 이는 하드웨어 또는 소프트웨어 실패로 인해 코드가 메시지 처리에 실패하는 경우 코드의 다른 인스턴스가 동일한 메시지를 가져와 다시 시도하도록 합니다.  

이 **표시 안 함 시간 제한**은 메시지를 다시 처리할 수 있을 때까지 해당 메시지를 표시하지 않을 시간을 정의합니다. 기본값은 30초입니다.

코드는 2단계를 거쳐 큐에서 메시지를 읽습니다. Microsoft Azure. .cdca [queue. GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) 메서드를 호출 하면 큐에서 다음 메시지를 가져올 수 있습니다. **Getmessage** 에서 반환 된 메시지는이 큐에서 메시지를 읽는 다른 코드에는 표시 되지 않습니다. 큐에서 메시지의 제거를 완료 하려면 [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) 메서드를 호출 합니다 .이 메서드는

다음 예제에서는 세 개의 큐 메시지를 통해 읽은 후 10초(표시 안 함 시간 제한) 동안 대기합니다. 그런 다음 세 개의 메시지를 다시 읽으면 **DeleteMessage**를 호출하여 읽은 후 메시지가 삭제됩니다. 메시지를 삭제한 후에 큐를 읽으려고 하면 $queueMessage가 NULL로 반환됩니다.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>큐 삭제

큐와 해당 큐에 포함된 모든 메시지를 삭제하려면 Remove-AzStorageQueue cmdlet을 호출합니다. 다음 예제에서는 Remove-AzStorageQueue cmdlet을 사용하여 이 연습에서 사용된 특정 큐를 삭제하는 방법을 보여 줍니다.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>리소스 정리

이 연습에서 만든 자산을 모두 제거하려면 리소스 그룹을 제거합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 사례에서는 만든 스토리지 계정 및 리소스 그룹 자체가 제거됩니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이 방법 문서에서는 다음 방법을 포함하여 PowerShell과 함께 기본 Queue Storage 관리에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 큐 만들기
> * 큐 검색
> * 메시지 추가
> * 다음 메시지 읽기
> * 메시지 삭제
> * 큐 삭제

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage cmdlet

* [스토리지 PowerShell cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
