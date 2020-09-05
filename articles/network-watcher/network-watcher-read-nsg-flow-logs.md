---
title: NSG 흐름 로그 읽기 | Microsoft 문서
description: Azure PowerShell를 사용 하 여 Azure Network Watcher에서 몇 분 마다 생성 되 고 업데이트 되는 네트워크 보안 그룹 흐름 로그를 구문 분석 하는 방법에 대해 알아봅니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: damendo
ms.openlocfilehash: 18023f5a5b36dd971080d7a62fdd44698a205bb4
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272538"
---
# <a name="read-nsg-flow-logs"></a>NSG 흐름 로그 읽기

PowerShell을 사용하여 NSG 흐름 로그 항목을 읽는 방법을 설명합니다.

NSG 흐름 로그는 스토리지 계정의 [블록 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)에 저장됩니다. 블록 Blob는 더 작은 여러 블록으로 구성되어 있습니다. 각 로그는 1시간마다 생성되는 개별 블록 Blob입니다. 1시간마다 새 로그가 생성되며 몇 분마다 로그가 새 항목으로 업데이트되어 최신 데이터가 포함됩니다. 이 문서에서는 흐름 로그의 각 부분을 읽는 방법에 대해 알아봅니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>시나리오

다음 시나리오에서는 스토리지 계정에 저장된 예제 흐름 로그를 사용합니다. NSG 흐름 로그에서 최신 이벤트를 선택적으로 읽는 방법을 알아봅니다. 이 문서에서는 PowerShell을 사용하지만 여기서 설명하는 개념은 프로그래밍 언어에만 국한되지 않으며, Azure Storage API가 지원하는 모든 언어에 적용됩니다.

## <a name="setup"></a>설치 프로그램

시작하기 전에 계정에 있는 하나 이상의 네트워크 보안 그룹에서 네트워크 보안 그룹 흐름 로깅을 사용하도록 설정해야 합니다. 네트워크 보안 흐름 로그를 사용하도록 설정하는 방법에 대한 지침은 [네트워크 보안 그룹에 대한 흐름 로깅 소개](network-watcher-nsg-flow-logging-overview.md) 문서를 참조하세요.

## <a name="retrieve-the-block-list"></a>블록 목록 검색

다음 PowerShell은 NSG 흐름 로그 Blob를 쿼리하는 데 필요한 변수를 설정하고 [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) 블록 Blob 내의 블록을 나열합니다. 실제 환경에서 사용되는 값을 포함하여 스크립트를 업데이트하세요.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockListAsync()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

`$blockList` 변수는 Blob의 블록 목록을 반환합니다. 각 블록 Blob에는 블록이 두 개 이상 포함되어 있습니다.  첫 번째 블록은 길이가 `12`바이트이며 json 로그의 여는 괄호를 포함합니다. 나머지 블록은 닫는 괄호이며 길이가 `2`바이트입니다.  보시다시피 다음 예제 로그에는 개별 항목 7개가 있습니다. 로그의 모든 새 항목은 최종 블록 앞의 오른쪽 끝에 추가됩니다.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>블록 Blob 읽기

다음으로는 `$blocklist` 변수를 읽어 데이터를 검색해야 합니다. 이 예제에서는 블록 목록을 반복 검색하면서 각 블록의 바이트를 읽어 배열에 저장합니다. 이때 [DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray) 메서드를 사용하여 데이터를 검색합니다.

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
    # Set the size of the byte array to the largest block
    $maxvalue = ($blocklist | measure Length -Maximum).Maximum

    # Create an array to store values in
    $valuearray = @()

    # Define the starting index to track the current block being read
    $index = 0

    # Loop through each block in the block list
    for($i=0; $i -lt $blocklist.count; $i++)
    {
        # Create a byte array object to story the bytes from the block
        $downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

        # Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

현재 `$valuearray` 배열에는 각 블록의 문자열 값이 포함되어 있습니다. 항목을 확인하려면 `$valuearray[$valuearray.Length-2]`를 실행하여 배열의 마지막에서 두 번째 값을 가져옵니다. 마지막 값은 닫는 괄호이므로 가져올 필요가 없습니다.

이 값의 결과가 다음 예제에 나와 있습니다.

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

이 시나리오는 전체 로그를 구문 분석할 필요 없이 NSG 흐름 로그의 항목을 읽는 방법의 예입니다. 블록 Blob에 저장된 블록의 길이를 추적하거나 블록 ID를 사용하면 로그에 작성되는 새 항목을 읽을 수 있습니다. 이 방법을 사용하면 새 항목만 읽을 수 있습니다.

## <a name="next-steps"></a>다음 단계


NSG 흐름 로그를 보는 방법에 대해 자세히 알아보려면 [Elastic Stack 사용](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), [Grafana 사용](network-watcher-nsg-grafana.md) 및 [Graylog 사용](network-watcher-analyze-nsg-flow-logs-graylog.md)을 참조하세요. Blob을 직접 사용 하 고 다양 한 log analytics 소비자를 내보내는 데 사용 되는 오픈 소스 Azure 함수 방법은 [Azure Network Watcher NSG 흐름 로그 커넥터](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector)에서 찾을 수 있습니다.

[Azure 트래픽 분석](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) 를 사용 하 여 트래픽 흐름에 대 한 통찰력을 얻을 수 있습니다. 트래픽 분석은 [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) 를 사용 하 여 트래픽 흐름을 쿼리할 수 있도록 합니다.

저장소 blob에 대 한 자세한 내용은 [Azure Functions Blob storage 바인딩](../azure-functions/functions-bindings-storage-blob.md) 을 참조 하세요.
