---
title: Azure PowerShell을 사용하여 Azure IoT Hub에 대한 메시지 라우팅 구성
description: Azure PowerShell을 사용하여 Azure IoT Hub에 대한 메시지 라우팅을 구성합니다. 메시지의 속성에 따라 스토리지 계정 또는 Service Bus 큐로 라우팅합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: robinsh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 92c30287e836ccadda915dab70a4d40e9c4e851a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073406"
---
# <a name="tutorial-use-azure-powershell-to-configure-iot-hub-message-routing"></a>자습서: Azure PowerShell을 사용하여 IoT Hub 메시지 라우팅 구성

[!INCLUDE [iot-hub-include-routing-intro](../../includes/iot-hub-include-routing-intro.md)]

[!INCLUDE [iot-hub-include-routing-create-resources](../../includes/iot-hub-include-routing-create-resources.md)]

## <a name="download-the-script-optional"></a>스크립트 다운로드(선택 사항)

이 자습서의 두 번째 부분에서는 Visual Studio 애플리케이션을 다운로드하고 실행하여 IoT Hub에 메시지를 보냅니다. 이 다운로드에는 Azure Resource Manager 템플릿과 매개 변수 파일은 물론 Azure CLI와 PowerShell 스크립트가 포함된 폴더가 있습니다. 

완성된 스크립트를 보려면 [Azure IoT C# 샘플](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip)을 다운로드합니다. master.zip 파일의 압축을 풉니다. Azure CLI 스크립트는 /iot-hub/Tutorials/Routing/SimulatedDevice/resources/의 **iothub_routing_psh.ps1**에 있습니다.

## <a name="create-your-resources"></a>리소스 만들기

먼저 PowerShell로 리소스를 만듭니다.

### <a name="use-powershell-to-create-your-base-resources"></a>PowerShell을 사용하여 기본 리소스 만들기

아래 스크립트를 복사하여 Cloud Shell에 붙여넣고 Enter 키를 누릅니다. 스크립트가 한 번에 한 줄씩 실행됩니다. 스크립트의 첫 번째 섹션은 이 자습서의 기본 리소스(스토리지 계정, IoT Hub, Service Bus 네임스페이스 및 Service Bus 큐)를 만듭니다. 자습서를 진행하면서 스크립트의 각 블록을 복사하여 Cloud Shell에 붙여넣고 실행합니다.

몇 가지 리소스 이름(예: IoT Hub 이름 및 스토리지 계정 이름)은 전역적으로 고유해야 합니다. 이를 위해 해당 리소스 이름에는 *randomValue*라는 임의의 영숫자 값이 추가됩니다. randomValue는 스크립트의 맨 위에 한 번 생성되고 전체 스크립트에서 필요에 따라 리소스에 추가됩니다. 임의로 설정하지 않으려면 빈 문자열이나 특정 값으로 설정할 수 있습니다. 

> [!IMPORTANT]
> 초기 스크립트에 설정된 변수는 라우팅 스크립트에도 사용되기 때문에 모든 스크립트를 동일한 Cloud Shell 세션에서 실행합니다. 새 세션을 열어서 라우팅을 설정하기 위한 스크립트를 실행하면, 여러 변수가 누락된 값이 됩니다. 
>

```azurepowershell-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
$subscriptionID = (Get-AzContext).Subscription.Id

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves the first 6 digits of a random value.
$randomValue = "$(Get-Random)".Substring(0,6)

# Set the values for the resource names that don't have to be globally unique.
$location = "West US"
$resourceGroup = "ContosoResources"
$iotHubConsumerGroup = "ContosoConsumers"
$containerName = "contosoresults"

# Create the resource group to be used 
#   for all resources for this tutorial.
New-AzResourceGroup -Name $resourceGroup -Location $location

# The IoT hub name must be globally unique, 
#   so add a random value to the end.
$iotHubName = "ContosoTestHub" + $randomValue
Write-Host "IoT hub name is " $iotHubName

# Create the IoT hub.
New-AzIotHub -ResourceGroupName $resourceGroup `
    -Name $iotHubName `
    -SkuName "S1" `
    -Location $location `
    -Units 1 

# Add a consumer group to the IoT hub for the 'events' endpoint.
Add-AzIotHubEventHubConsumerGroup -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EventHubConsumerGroupName $iotHubConsumerGroup `
  -EventHubEndpointName "events"

# The storage account name must be globally unique, so add a random value to the end.
$storageAccountName = "contosostorage" + $randomValue
Write-Host "storage account name is " $storageAccountName

# Create the storage account to be used as a routing destination.
# Save the context for the storage account 
#   to be used when creating a container.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
    -Name $storageAccountName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind Storage
# Retrieve the connection string from the context. 
$storageConnectionString = $storageAccount.Context.ConnectionString
Write-Host "storage connection string = " $storageConnectionString 

# Create the container in the storage account.
New-AzStorageContainer -Name $containerName `
    -Context $storageAccount.Context

# The Service Bus namespace must be globally unique,
#   so add a random value to the end.
$serviceBusNamespace = "ContosoSBNamespace" + $randomValue
Write-Host "Service Bus namespace is " $serviceBusNamespace

# Create the Service Bus namespace.
New-AzServiceBusNamespace -ResourceGroupName $resourceGroup `
    -Location $location `
    -Name $serviceBusNamespace 

# The Service Bus queue name must be globally unique,
#  so add a random value to the end.
$serviceBusQueueName  = "ContosoSBQueue" + $randomValue
Write-Host "Service Bus queue name is " $serviceBusQueueName 

# Create the Service Bus queue to be used as a routing destination.
New-AzServiceBusQueue -ResourceGroupName $resourceGroup `
    -Namespace $serviceBusNamespace `
    -Name $serviceBusQueueName  `
    -EnablePartitioning $False 
```

### <a name="create-a-simulated-device"></a>시뮬레이션된 디바이스 만들기

[!INCLUDE [iot-hub-include-create-simulated-device-portal](../../includes/iot-hub-include-create-simulated-device-portal.md)]

기본 리소스가 설정되었으면, 메시지 라우팅을 구성할 수 있습니다.

## <a name="set-up-message-routing"></a>메시지 라우팅 설정

[!INCLUDE [iot-hub-include-create-routing-description](../../includes/iot-hub-include-create-routing-description.md)]

라우팅 엔드포인트를 만들려면 [Add-AzIotHubRoutingEndpoint](/powershell/module/az.iothub/Add-AzIotHubRoutingEndpoint)를 사용합니다. 엔드포인트에 대한 메시지 경로를 만들려면 [Add-AzIotHubRoute](/powershell/module/az.iothub/Add-AzIoTHubRoute)를 사용합니다.

### <a name="route-to-a-storage-account"></a>스토리지 계정으로 라우팅 

먼저 스토리지 계정에 대한 엔드포인트를 설정한 다음, 메시지 경로를 만듭니다.

[!INCLUDE [iot-hub-include-blob-storage-format](../../includes/iot-hub-include-blob-storage-format.md)]

Cloud Shell 세션 내에서 설정해야 하는 스크립트에 사용되는 변수는 다음과 같습니다.

**resourceGroup**: 이 필드는 두 번 발생합니다. 둘 다 리소스 그룹에 설정합니다.

**name**: 이 필드는 라우팅이 적용될 IoT Hub의 이름입니다.

**endpointName**: 이 필드는 엔드포인트를 식별하는 이름입니다. 

**endpointType**: 이 필드는 엔드포인트의 형식입니다. 이 값은 `azurestoragecontainer`, `eventhub`, `servicebusqueue` 또는 `servicebustopic`으로 설정해야 합니다. 여기에서는 `azurestoragecontainer`로 설정합니다.

**subscriptionID**: 이 필드는 Azure 계정에 대한 subscriptionID로 설정됩니다.

**storageConnectionString**: 이 값은 이전 스크립트에서 설정한 스토리지 계정에서 검색됩니다. 라우팅을 통해 스토리지 계정에 액세스하는 데 사용됩니다.

**containerName**: 이 필드는 데이터가 작성될 스토리지 계정의 컨테이너 이름입니다.

**Encoding**: 이 필드는 `AVRO` 또는 `JSON`으로 설정합니다. 이것은 저장된 데이터의 형식을 지정합니다. 기본값은 AVRO입니다.

**routeName**: 이 필드는 설정하는 경로의 이름입니다 

**condition**: 이 필드는 이 엔드포인트에 보낸 메시지를 필터링하는 데 사용되는 쿼리입니다. 스토리지로 라우팅되는 메시지의 쿼리 조건은 `level="storage"`입니다.

**enabled**: 이 필드의 기본값은 `true`이며, 메시지 경로가 생성된 후 사용하도록 설정되어야 한다는 것을 나타냅니다.

이 스크립트를 복사하여 Cloud Shell 창에 붙여넣습니다.

```powershell
##### ROUTING FOR STORAGE #####

$endpointName = "ContosoStorageEndpoint"
$endpointType = "azurestoragecontainer"
$routeName = "ContosoStorageRoute"
$condition = 'level="storage"'
```

다음 단계에서는 스토리지 계정에 대한 라우팅 엔드포인트를 만듭니다. 결과가 저장될 컨테이너도 지정합니다. 컨테이너는 스토리지 계정이 만들어졌을 때 생성되었습니다.

```powershell
# Create the routing endpoint for storage.
# Specify 'AVRO' or 'JSON' for the encoding of the data.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $storageConnectionString  `
  -ContainerName $containerName `
  -Encoding AVRO
```

다음으로, 스토리지 엔드포인트에 대한 메시지 경로를 만듭니다. 메시지 경로는 쿼리 사양을 충족하는 메시지를 보낼 위치를 지정합니다.

```powershell
# Create the route for the storage endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="route-to-a-service-bus-queue"></a>Service Bus 큐에 라우팅

이제 Service Bus 큐에 대한 라우팅을 설정합니다. Service Bus 큐에 대한 연결 문자열을 검색하려면, 올바른 권한이 정의된 권한 부여 규칙을 만들어야 합니다. 다음 스크립트는 `sbauthrule`이라는 Service Bus 큐에 대한 권한 부여 규칙을 만들고 권한을 `Listen Manage Send`로 설정합니다. 권한 부여 규칙이 설정되면, 이것을 사용하여 큐에 대한 연결 문자열을 검색할 수 있습니다.

```powershell
##### ROUTING FOR SERVICE BUS QUEUE #####

# Create the authorization rule for the Service Bus queue.
New-AzServiceBusAuthorizationRule `
  -ResourceGroupName $resourceGroup `
  -NamespaceName $serviceBusNamespace `
  -Queue $serviceBusQueueName `
  -Name "sbauthrule" `
  -Rights @("Manage","Listen","Send")
```

이제 권한 부여 규칙을 사용하여 Service Bus 큐 키를 검색합니다. 이 권한 부여 규칙은 나중에 스크립트에서 연결 문자열을 검색하는 데 사용됩니다.

```powershell
$sbqkey = Get-AzServiceBusKey `
    -ResourceGroupName $resourceGroup `
    -NamespaceName $serviceBusNamespace `
    -Queue $servicebusQueueName `
    -Name "sbauthrule"
```

이제 Service Bus 큐에 대한 메시지 경로와 라우팅 엔드포인트를 설정합니다. Cloud Shell 세션 내에서 설정해야 하는 스크립트에 사용되는 변수는 다음과 같습니다.

**endpointName**: 이 필드는 엔드포인트를 식별하는 이름입니다. 

**endpointType**: 이 필드는 엔드포인트의 형식입니다. 이 값은 `azurestoragecontainer`, `eventhub`, `servicebusqueue` 또는 `servicebustopic`으로 설정해야 합니다. 여기에서는 `servicebusqueue`로 설정합니다.

**routeName**: 이 필드는 설정하는 경로의 이름입니다 

**condition**: 이 필드는 이 엔드포인트에 보낸 메시지를 필터링하는 데 사용되는 쿼리입니다. Service Bus 큐로 라우팅되는 메시지의 쿼리 조건은 `level="critical"`입니다.

다음은 Service Bus 큐의 메시지 라우팅을 위한 Azure PowerShell입니다.

```powershell
$endpointName = "ContosoSBQueueEndpoint"
$endpointType = "servicebusqueue"
$routeName = "ContosoSBQueueRoute"
$condition = 'level="critical"'

# Add the routing endpoint, using the connection string property from the key.
Add-AzIotHubRoutingEndpoint `
  -ResourceGroupName $resourceGroup `
  -Name $iotHubName `
  -EndpointName $endpointName `
  -EndpointType $endpointType `
  -EndpointResourceGroup $resourceGroup `
  -EndpointSubscriptionId $subscriptionId `
  -ConnectionString $sbqkey.PrimaryConnectionString

# Set up the message route for the Service Bus queue endpoint.
Add-AzIotHubRoute `
   -ResourceGroupName $resourceGroup `
   -Name $iotHubName `
   -RouteName $routeName `
   -Source DeviceMessages `
   -EndpointName $endpointName `
   -Condition $condition `
   -Enabled 
```

### <a name="view-message-routing-in-the-portal"></a>포털에서 메시지 라우팅 보기

[!INCLUDE [iot-hub-include-view-routing-in-portal](../../includes/iot-hub-include-view-routing-in-portal.md)]

## <a name="next-steps"></a>다음 단계

리소스가 설정되고 메시지 경로가 구성되었으면 다음 자습서로 진행하여, IoT 허브에 메시지를 보낸 후 다른 대상으로 라우팅되는 과정을 확인하는 방법을 살펴보세요. 

> [!div class="nextstepaction"]
> [2부 - 메시지 라우팅 결과 보기](tutorial-routing-view-message-routing-results.md)
