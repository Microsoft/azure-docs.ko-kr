---
title: Azure Blob storage 이벤트를 웹 끝점으로 보내기-PowerShell | Microsoft Docs
description: Azure Event Grid를 사용 하 여 Blob 저장소 이벤트를 구독 하 고, 이벤트를 트리거하고, 결과를 볼 수 있습니다. Azure PowerShell를 사용 하 여 저장소 이벤트를 웹 끝점으로 라우팅합니다.
author: normesta
ms.author: normesta
ms.reviewer: dastanfo
ms.date: 08/23/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 06be168ff9dfd55a56578b3afcdab8d984416756
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078013"
---
# <a name="quickstart-route-storage-events-to-web-endpoint-with-powershell"></a>빠른 시작: PowerShell을 사용 하 여 웹 끝점에 저장소 이벤트 라우팅

Azure Event Grid는 클라우드에 대한 이벤트 서비스입니다. 이 문서에서는 Azure PowerShell을 사용하여 Blob Storage 이벤트를 구독하고 이벤트를 트리거하여 결과를 확인합니다. 

일반적으로 이벤트 데이터를 처리하고 작업을 수행하는 엔드포인트에 이벤트를 보냅니다. 그러나 이 문서를 간소화하기 위해 메시지를 수집하고 표시하는 웹앱에 이벤트를 보냅니다.

작업을 완료하면 이벤트 데이터가 웹앱에 보내진 것을 확인할 수 있습니다.

![결과 보기](./media/storage-blob-event-quickstart-powershell/view-results.png)

## <a name="setup"></a>설치 프로그램

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

이 문서에서는 Azure PowerShell의 최신 버전을 실행해야 합니다. 설치 또는 업그레이드 해야 하는 경우 [Azure PowerShell 설치 및 구성](/powershell/azure/install-Az-ps)을 참조 하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

`Connect-AzAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시에 따라 인증합니다.

```powershell
Connect-AzAccount
```

이 예제에서는 **westus2**를 사용하고, 선택한 항목을 전체적으로 사용할 수 있게 변수에 저장합니다.

```powershell
$location = "westus2"
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Event Grid 토픽은 Azure 리소스이며 Azure 리소스 그룹에 배치해야 합니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컬렉션입니다.

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 명령을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 **westus2** 위치에 **gridResourceGroup**이라는 리소스 그룹을 만듭니다.  

```powershell
$resourceGroup = "gridResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

Blob Storage 이벤트는 범용 v2 스토리지 계정과 Blob Storage 계정에서 사용할 수 있습니다. **범용 v2** 스토리지 계정은 Blob, 파일, 큐 및 테이블을 포함하여 모든 스토리지 서비스의 모든 기능을 지원하는 스토리지 계정입니다. **Blob 저장소 계정은** 구조화 되지 않은 데이터를 Azure Storage blob (objects)로 저장 하는 특수 한 저장소 계정입니다. Blob Storage 계정은 범용 스토리지 계정과 유사합니다. 블록 Blob과 연결 Blob에 대한 100% API 일관성을 포함하여 현재 제공되는 뛰어난 내구성, 가용성, 확장성은 모두 같습니다. 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

[New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount)를 사용하여 LRS 복제를 통한 Blob Storage 계정을 만든 후 사용할 스토리지 계정을 정의하는 스토리지 계정 컨텍스트를 검색합니다. 스토리지 계정에서 작업할 때 반복적으로 자격 증명을 제공하는 대신 컨텍스트를 참조합니다. 이 예제에서는 LRS(로컬 중복 스토리지)를 사용하여 **gridstorage**라는 스토리지 계정을 만듭니다. 

> [!NOTE]
> 스토리지 계정 이름은 전역 네임스페이스에 있으므로 이 스크립트에 제공된 이름에 일부 임의 문자를 추가해야 합니다.

```powershell
$storageName = "gridstorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind BlobStorage `
  -AccessTier Hot

$ctx = $storageAccount.Context
```

## <a name="create-a-message-endpoint"></a>메시지 엔드포인트 만들기

토픽을 구독하기 전에 이벤트 메시지에 대한 엔드포인트를 만들어 보겠습니다. 일반적으로 엔드포인트는 이벤트 데이터를 기반으로 작업을 수행합니다. 이 빠른 시작을 간소화하기 위해 이벤트 메시지를 표시하는 [미리 작성된 웹앱](https://github.com/Azure-Samples/azure-event-grid-viewer)을 배포합니다. 배포된 솔루션은 App Service 계획, App Service 웹앱 및 GitHub의 소스 코드를 포함합니다.

`<your-site-name>`을 웹앱의 고유한 이름으로 바꿉니다. 웹앱 이름은 DNS 항목의 일부이므로 고유해야 합니다.

```powershell
$sitename="<your-site-name>"

New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/azure-event-grid-viewer/master/azuredeploy.json" `
  -siteName $sitename `
  -hostingPlanName viewerhost
```

배포가 완료될 때까지 몇 분 정도 걸릴 수 있습니다. 배포가 성공된 후 실행하는지 확인하려면 웹앱을 봅니다. 웹 브라우저에서 `https://<your-site-name>.azurewebsites.net`으로 이동합니다.

현재 표시된 메시지가 없는 사이트가 표시되어야 합니다.

[!INCLUDE [event-grid-register-provider-powershell.md](../../../includes/event-grid-register-provider-powershell.md)]

## <a name="subscribe-to-your-storage-account"></a>스토리지 계정 구독

항목을 구독 하 여 추적할 이벤트 Event Grid를 알려 줍니다. 다음 예제에서는 사용자가 만든 저장소 계정을 구독 하 고 웹 앱의 URL을 이벤트 알림에 대 한 끝점으로 전달 합니다. 웹앱에 대한 엔드포인트는 접미사 `/api/updates/`를 포함해야 합니다.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName $resourceGroup -AccountName $storageName).Id
$endpoint="https://$sitename.azurewebsites.net/api/updates"

New-AzEventGridSubscription `
  -EventSubscriptionName gridBlobQuickStart `
  -Endpoint $endpoint `
  -ResourceId $storageId
```

웹앱을 다시 확인하고, 구독 유효성 검사 이벤트를 보냈음을 확인합니다. 눈 모양 아이콘을 선택하여 이벤트 데이터를 확장합니다. Event Grid는 유효성 검사 이벤트를 보내므로 엔드포인트는 이벤트 데이터를 수신하려는 것을 확인할 수 있습니다. 웹앱은 구독의 유효성을 검사하는 코드를 포함합니다.

![구독 이벤트 보기](./media/storage-blob-event-quickstart-powershell/view-subscription-event.png)

## <a name="trigger-an-event-from-blob-storage"></a>Blob Storage에서 이벤트 트리거

이제 이벤트를 트리거하여 Event Grid가 메시지를 사용자 엔드포인트에 어떻게 배포하는지 살펴 보겠습니다. 먼저 컨테이너 및 개체를 만들어 보겠습니다. 그런 다음 컨테이너에 개체를 업로드합니다.

```powershell
$containerName = "gridcontainer"
New-AzStorageContainer -Name $containerName -Context $ctx

echo $null >> gridTestFile.txt

Set-AzStorageBlobContent -File gridTestFile.txt -Container $containerName -Context $ctx -Blob gridTestFile.txt
```

이벤트를 트리거했고 Event Grid가 구독할 때 구성한 엔드포인트로 메시지를 보냈습니다. 웹앱을 확인하여 방금 전송한 이벤트를 봅니다.

```json
[{
  "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Storage/storageAccounts/myblobstorageaccount",
  "subject": "/blobServices/default/containers/gridcontainer/blobs/gridTestFile.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T20:33:51.0595757Z",
  "id": "4d96b1d4-0001-00b3-58ce-16568c064fab",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "Azure-Storage-PowerShell-d65ca2e2-a168-4155-b7a4-2c925c18902f",
    "requestId": "4d96b1d4-0001-00b3-58ce-16568c000000",
    "eTag": "0x8D4E4E61AE038AD",
    "contentType": "application/octet-stream",
    "contentLength": 0,
    "blobType": "BlockBlob",
    "url": "https://myblobstorageaccount.blob.core.windows.net/gridcontainer/gridTestFile.txt",
    "sequencer": "00000000000000EB0000000000046199",
    "storageDiagnostics": {
      "batchId": "dffea416-b46e-4613-ac19-0371c0c5e352"
    }
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

## <a name="clean-up-resources"></a>리소스 정리
이 스토리지 계정 및 이벤트 구독을 계속 사용하려면 이 문서에서 만든 리소스를 정리하지 마세요. 계속 사용하지 않으려면 다음 명령을 사용하여 이 문서에서 만든 리소스를 삭제합니다.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>다음 단계

이제 토픽 및 이벤트 구독을 만드는 방법을 알아보았으므로 Blob Storage 이벤트 및 Event Grid에서 수행할 수 있는 작업에 대해 자세히 알아봅니다.

- [Blob Storage 이벤트에 응답](storage-blob-event-overview.md)
- [Event Grid 정보](../../event-grid/overview.md)
