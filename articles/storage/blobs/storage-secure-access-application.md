---
title: 애플리케이션 데이터에 대한 보안 액세스
titleSuffix: Azure Storage
description: SAS 토큰, 암호화 및 HTTPS를 사용하여 클라우드의 애플리케이션 데이터를 보호합니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: tamram
ms.reviewer: ozgun
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: e7dca9224fd213178aa9cee3a284f1eba39fb4fc
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001176"
---
# <a name="secure-access-to-application-data"></a>애플리케이션 데이터에 대한 보안 액세스

이 자습서는 시리즈의 3부입니다. 스토리지 계정에 대한 액세스를 보호하는 방법을 배웁니다. 

시리즈 3부에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * SAS 토큰을 사용하여 썸네일 이미지에 액세스
> * 서버 쪽 암호화 켜기
> * HTTPS 전용 전송 사용

[Azure Blob Storage](../common/storage-introduction.md#blob-storage)는 애플리케이션용 파일을 저장하기 위한 강력한 서비스를 제공합니다. 이 자습서에서는 [이전 항목][previous-tutorial]을 확장하여 웹 애플리케이션에서 스토리지 계정에 대한 액세스를 보호하는 방법을 보여 줍니다. 완료되면 이미지가 암호화되고 웹앱은 보안 SAS 토큰을 사용하여 썸네일 이미지에 액세스합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 이전 스토리지 자습서: [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정][previous-tutorial]를 완료해야 합니다.

## <a name="set-container-public-access"></a>컨테이너 공용 액세스 설정

자습서 시리즈의 이 부분에서는 썸네일에 액세스하기 위해 SAS 토큰을 사용합니다. 이 단계에서는 *thumbnails* 컨테이너에 대한 공용 액세스를 `off`로 설정합니다.

```bash
blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup \
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission \
    --account-name $blobStorageAccount \
    --account-key $blobStorageAccountKey \
    --name thumbnails \
    --public-access off
```

```powershell
$blobStorageAccount="<blob_storage_account>"

blobStorageAccountKey=$(az storage account keys list -g myResourceGroup `
    --account-name $blobStorageAccount --query [0].value --output tsv) 

az storage container set-permission `
    --account-name $blobStorageAccount `
    --account-key $blobStorageAccountKey `
    --name thumbnails `
    --public-access off
```

## <a name="configure-sas-tokens-for-thumbnails"></a>썸네일에 대한 SAS 토큰 구성

이 자습서 시리즈의 1부에서 웹 애플리케이션은 공용 컨테이너의 이미지를 표시했습니다. 시리즈의 이 부분에서는 SAS(공유 액세스 서명) 토큰을 사용하여 썸네일 이미지를 검색합니다. SAS 토큰을 사용하여 IP, 프로토콜, 시간 간격, 허용되는 권한에 따라 컨테이너 또는 blob에 대해 제한된 액세스를 제공할 수 있습니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.

이 예제에서 소스 코드 리포지토리는 업데이트된 코드 샘플이 있는 `sasTokens` 분기를 사용합니다. [az webapp deployment source delete](/cli/azure/webapp/deployment/source)를 사용하여 기존 GitHub 배포를 삭제합니다. 다음으로, [az webapp deployment source config](/cli/azure/webapp/deployment/source) 명령을 사용하여 웹앱에 대한 Git 배포를 구성합니다.

다음 명령에서 `<web-app>`은 웹앱의 이름입니다.

```bash
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> \
    --resource-group myResourceGroup --branch sasTokens --manual-integration \
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

```powershell
az webapp deployment source delete --name <web-app> --resource-group myResourceGroup

az webapp deployment source config --name <web_app> `
    --resource-group myResourceGroup --branch sasTokens --manual-integration `
    --repo-url https://github.com/Azure-Samples/storage-blob-upload-from-webapp
```

리포지토리의 `sasTokens` 분기는 `StorageHelper.cs` 파일을 업데이트합니다. `GetThumbNailUrls` 작업이 아래 코드 예제로 바뀝니다. 업데이트된 작업은 [BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder)를 사용하여 SAS 토큰에 대한 시작 시간, 만료 시간 및 사용 권한을 지정함으로써 썸네일 URL을 검색합니다. 웹앱을 배포했으므로 이제 SAS 토큰을 사용하여 URL을 갖는 썸네일이 검색됩니다. 업데이트된 작업은 다음 예제에 표시됩니다.

```csharp
public static async Task<List<string>> GetThumbNailUrls(AzureStorageConfig _storageConfig)
{
    List<string> thumbnailUrls = new List<string>();

    // Create a URI to the storage account
    Uri accountUri = new Uri("https://" + _storageConfig.AccountName + ".blob.core.windows.net/");

    // Create BlobServiceClient from the account URI
    BlobServiceClient blobServiceClient = new BlobServiceClient(accountUri);

    // Get reference to the container
    BlobContainerClient container = blobServiceClient.GetBlobContainerClient(_storageConfig.ThumbnailContainer);

    if (container.Exists())
    {
        // Set the expiration time and permissions for the container.
        // In this case, the start time is specified as a few 
        // minutes in the past, to mitigate clock skew.
        // The shared access signature will be valid immediately.
        BlobSasBuilder sas = new BlobSasBuilder
        {
            Resource = "c",
            BlobContainerName = _storageConfig.ThumbnailContainer,
            StartsOn = DateTimeOffset.UtcNow.AddMinutes(-5),
            ExpiresOn = DateTimeOffset.UtcNow.AddHours(1)
        };

        sas.SetPermissions(BlobContainerSasPermissions.All);

        // Create StorageSharedKeyCredentials object by reading
        // the values from the configuration (appsettings.json)
        StorageSharedKeyCredential storageCredential =
            new StorageSharedKeyCredential(_storageConfig.AccountName, _storageConfig.AccountKey);

        // Create a SAS URI to the storage account
        UriBuilder sasUri = new UriBuilder(accountUri);
        sasUri.Query = sas.ToSasQueryParameters(storageCredential).ToString();

        foreach (BlobItem blob in container.GetBlobs())
        {
            // Create the URI using the SAS query token.
            string sasBlobUri = container.Uri + "/" +
                                blob.Name + sasUri.Query;

            //Return the URI string for the container, including the SAS token.
            thumbnailUrls.Add(sasBlobUri);
        }
    }
    return await Task.FromResult(thumbnailUrls);
}
```

다음 클래스, 속성 및 메서드는 이전 작업에서 사용됩니다.

| 클래스 | 속성 | 메서드 |
|-------|------------|---------|
|[StorageSharedKeyCredential](/dotnet/api/azure.storage.storagesharedkeycredential) |  |  |
|[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) |  |[GetBlobContainerClient](/dotnet/api/azure.storage.blobs.blobserviceclient.getblobcontainerclient) |
|[BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) | [Uri](/dotnet/api/azure.storage.blobs.blobcontainerclient.uri) |[Exists](/dotnet/api/azure.storage.blobs.blobcontainerclient.exists) <br> [GetBlobs](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobs) |
|[BlobSasBuilder](/dotnet/api/azure.storage.sas.blobsasbuilder) |  | [SetPermissions](/dotnet/api/azure.storage.sas.blobsasbuilder.setpermissions) <br> [ToSasQueryParameters](/dotnet/api/azure.storage.sas.blobsasbuilder.tosasqueryparameters) |
|[BlobItem](/dotnet/api/azure.storage.blobs.models.blobitem) | [이름](/dotnet/api/azure.storage.blobs.models.blobitem.name) |  |
|[UriBuilder](/dotnet/api/system.uribuilder) | [쿼리](/dotnet/api/system.uribuilder.query) |  |
|[목록](/dotnet/api/system.collections.generic.list-1) | | [추가](/dotnet/api/system.collections.generic.list-1.add) |

## <a name="azure-storage-encryption"></a>Azure Storage 암호화

[Azure Storage 암호화](../common/storage-service-encryption.md)를 사용하면 미사용 데이터를 암호화하고 암호화 및 암호 해독을 처리하여 데이터를 보호할 수 있습니다. 모든 데이터는 가장 강력한 블록 암호화 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 암호화됩니다.

Microsoft에서 암호화 키를 관리하도록 선택하거나 Azure Key Vault를 통해 고객 관리 키를 사용하여 사용자 고유의 키를 가져올 수 있습니다. 자세한 내용은 [Azure Key Vault를 통해 고객 관리 키를 사용하여 Azure Storage 암호화 관리](../common/encryption-customer-managed-keys.md)를 참조하세요.

Azure Storage 암호화는 모든 성능 계층(표준 및 프리미엄), 모든 배포 모델(Azure Resource Manager 및 클래식) 및 모든 Azure Storage 서비스(Blob, 큐, 테이블 및 파일)의 데이터를 자동으로 암호화합니다.

## <a name="enable-https-only"></a>HTTPS만 사용

스토리지 계정과의 데이터 요청을 안전하게 유지하기 위해 요청을 HTTPS로만 제한할 수 있습니다. [az storage account update](/cli/azure/storage/account) 명령을 사용하여 스토리지 계정 필수 프로토콜을 업데이트합니다.

```azurecli-interactive
az storage account update --resource-group myresourcegroup --name <storage-account-name> --https-only true
```

`HTTP` 프로토콜을 사용하여 `curl`을 사용하는 연결을 테스트합니다.

```azurecli-interactive
curl http://<storage-account-name>.blob.core.windows.net/<container>/<blob-name> -I
```

보안 전송이 요구되므로 이제 다음과 같은 메시지가 표시됩니다.

```
HTTP/1.1 400 The account being accessed does not support http.
```

## <a name="next-steps"></a>다음 단계

시리즈의 3부에서는 다음 방법을 비롯하여 스토리지 계정에 대한 액세스를 보호하는 방법을 배웠습니다.

> [!div class="checklist"]
> * SAS 토큰을 사용하여 썸네일 이미지에 액세스
> * 서버 쪽 암호화 켜기
> * HTTPS 전용 전송 사용

시리즈의 4부를 계속 진행하면서 클라우드 스토리지 애플리케이션을 모니터링하고 문제를 해결하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [애플리케이션 클라우드 애플리케이션 스토리지 모니터링 및 문제 해결](storage-monitor-troubleshoot-storage-application.md)

[previous-tutorial]: ../../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json
