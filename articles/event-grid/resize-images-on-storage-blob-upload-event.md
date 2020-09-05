---
title: '자습서: Azure Event Grid를 사용하여 업로드된 이미지 크기 자동 조정'
description: '자습서: Azure Event Grid는 Azure Storage에 BLOB 업로드를 트리거할 수 있습니다. 이 기능을 활용하여 Azure Storage에 업로드된 이미지 파일을 Azure Functions 등의 타 서비스로 보내 크기를 조절하거나 다른 향상을 수행할 수 있습니다.'
ms.topic: tutorial
ms.date: 07/07/2020
ms.openlocfilehash: 19dfffdcee0fb95ae867b1b26fa51e702658445d
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86105798"
---
# <a name="tutorial-automate-resizing-uploaded-images-using-event-grid"></a>자습서: Event Grid를 사용하여 업로드된 이미지 크기 자동 조정

[Azure Event Grid](overview.md)는 클라우드용 이벤트 서비스입니다. Event Grid를 사용하면 Azure 서비스나 타 리소스에서 발생하는 이벤트에 대한 구독을 만들 수 있습니다.  

이 자습서는 스토리지 자습서의 2부입니다. [이전 스토리지 자습서][previous-tutorial]를 확장하여 Azure Event Grid 및 Azure Functions를 통해 서버리스 자동 미리 보기 생성을 추가합니다. Event Grid를 사용하면 [Azure Functions](../azure-functions/functions-overview.md)가 [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) 이벤트에 응답하고 업로드된 이미지의 미리 보기를 생성할 수 있습니다. Blob Storage 만들기 이벤트에 대해 이벤트 구독을 만듭니다. BLOB이 특정 Blob Storage 컨테이너에 추가되면 함수 엔드포인트가 호출됩니다. Event Grid에서 함수 바인딩에 전달된 데이터를 사용하여 BLOB에 액세스하고 미리 보기 이미지를 생성합니다.

Azure CLI 및 Azure Portal을 사용하여 크기 조정 기능을 기존 이미지 업로드 앱에 추가합니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

![브라우저에 게시된 웹앱](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

![브라우저에 게시된 웹앱](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Storage 계정 만들기
> * Azure Functions를 사용하여 서버 없는 코드 배포
> * Event Grid에서 Blob Storage 이벤트 구독 만들기

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서를 완료하려면 다음이 필요합니다.

이전 Blob 스토리지 자습서: [Azure Storage를 사용하여 클라우드에 이미지 데이터 업로드][previous-tutorial]를 완료했습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에는 Azure CLI 버전 2.0.14 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Cloud Shell을 사용하지 않는 경우 먼저 `az login`을 사용하여 로그인해야 합니다.

이전에 사용자의 구독에서 Event Grid 리소스 공급자를 등록한 적이 없는 경우, 등록되었는지 확인합니다.

```bash
az provider register --namespace Microsoft.EventGrid
```

```powershell
az provider register --namespace Microsoft.EventGrid
```

## <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

Azure Functions에는 일반 스토리지 계정이 필요합니다. 이전 자습서에서 만든 Blob Storage 계정 외에도 [az storage account create](/cli/azure/storage/account) 명령을 사용하여 리소스 그룹에 별도의 일반 스토리지 계정을 만듭니다. Storage 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 포함할 수 있습니다.

1. 이전 자습서에서 만든 리소스 그룹 이름을 보관하는 변수를 설정합니다.

    ```bash
    resourceGroupName="myResourceGroup"
    ```

    ```powershell
    $resourceGroupName="myResourceGroup"
    ```

1. 만들 리소스의 위치에 보유할 변수를 설정합니다. 

    ```bash
    location="eastus"
    ```

    ```powershell
    $location="eastus"
    ```

1. Azure Functions에 필요한 새 스토리지 계정 이름에 대한 변수를 설정합니다.

    ```bash
    functionstorage="<name of the storage account to be used by the function>"
    ```

    ```powershell
    $functionstorage="<name of the storage account to be used by the function>"
    ```

1. Azure 함수에 대한 스토리지 계정을 만듭니다.

    ```bash
    az storage account create --name $functionstorage --location $location \
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

    ```powershell
    az storage account create --name $functionstorage --location $location `
    --resource-group $resourceGroupName --sku Standard_LRS --kind StorageV2
    ```

## <a name="create-a-function-app"></a>함수 앱 만들기  

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. [az functionapp create](/cli/azure/functionapp) 명령을 사용하여 함수 앱을 만듭니다.

다음 명령에서 사용자 고유의 함수 앱 이름을 제공합니다. 함수 앱은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다.

1. 만들려는 함수 앱의 이름을 지정합니다.

    ```bash
    functionapp="<name of the function app>"
    ```

    ```powershell
    $functionapp="<name of the function app>"
    ```

1. Azure 함수를 만듭니다.

    ```bash
    az functionapp create --name $functionapp --storage-account $functionstorage \
      --resource-group $resourceGroupName --consumption-plan-location $location \
      --functions-version 2
    ```

    ```powershell
    az functionapp create --name $functionapp --storage-account $functionstorage `
      --resource-group $resourceGroupName --consumption-plan-location $location `
      --functions-version 2
    ```

이제 [이전 자습서][previous-tutorial]에서 만든 Blob 스토리지에 연결하도록 함수 앱을 구성합니다.

## <a name="configure-the-function-app"></a>함수 앱 구성

이 함수는 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령을 사용하여 함수 앱의 애플리케이션 설정에 추가된 Blob Storage 계정에 대한 자격 증명이 필요합니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

```bash
storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails \
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

```powershell
$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings AzureWebJobsStorage=$storageConnectionString THUMBNAIL_CONTAINER_NAME=thumbnails `
  THUMBNAIL_WIDTH=100 FUNCTIONS_EXTENSION_VERSION=~2
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

```bash
blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName \
  -n $blobStorageAccount --query [0].value --output tsv)

storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName \
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName \
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails \
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount \
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey \
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

```powershell
$blobStorageAccountKey=$(az storage account keys list -g $resourceGroupName `
  -n $blobStorageAccount --query [0].value --output tsv)

$storageConnectionString=$(az storage account show-connection-string --resource-group $resourceGroupName `
  --name $blobStorageAccount --query connectionString --output tsv)

az functionapp config appsettings set --name $functionapp --resource-group $resourceGroupName `
  --settings FUNCTIONS_EXTENSION_VERSION=~2 BLOB_CONTAINER_NAME=thumbnails `
  AZURE_STORAGE_ACCOUNT_NAME=$blobStorageAccount `
  AZURE_STORAGE_ACCOUNT_ACCESS_KEY=$blobStorageAccountKey `
  AZURE_STORAGE_CONNECTION_STRING=$storageConnectionString
```

---

`FUNCTIONS_EXTENSION_VERSION=~2` 설정은 함수 앱을 Azure Functions 런타임의 버전 2.x에서 실행하게 만듭니다.

이제 이 함수 앱에 함수 코드 프로젝트를 배포할 수 있습니다.

## <a name="deploy-the-function-code"></a>함수 코드 배포 

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

샘플 C# 크기 조정 기능은 [GitHub](https://github.com/Azure-Samples/function-image-upload-resize)에서 사용할 수 있습니다. 이 코드 프로젝트를 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) 명령을 사용하여 함수 앱에 배포합니다.

```bash
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName \
  --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

```powershell
az functionapp deployment source config --name $functionapp --resource-group $resourceGroupName `
  --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/function-image-upload-resize
```

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

샘플 Node.js 크기 조정 기능은 [GitHub](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10)에서 사용할 수 있습니다. 이 Functions 코드 프로젝트를 [az functionapp deployment source config](/cli/azure/functionapp/deployment/source) 명령을 사용하여 함수 앱에 배포합니다.

```bash
az functionapp deployment source config --name $functionapp \
  --resource-group $resourceGroupName --branch master --manual-integration \
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

```powershell
az functionapp deployment source config --name $functionapp `
  --resource-group $resourceGroupName --branch master --manual-integration `
  --repo-url https://github.com/Azure-Samples/storage-blob-resize-function-node-v10
```

---

이미지 크기 조정 함수는 Event Grid 서비스에서 전송된 HTTP 요청에 의해 트리거됩니다. 이벤트 구독을 만들어 Event Grid가 함수의 URL에서 이러한 알림을 가져오도록 지시합니다. 이 자습서에서 Blob 생성 이벤트를 구독합니다.

Event Grid 알림에서 함수에 전달되는 데이터에는 Blob의 URL이 포함됩니다. 차례로 URL을 입력 바인딩에 전달하여 Blob Storage에서 업로드된 이미지를 가져옵니다. 이 함수는 미리 보기 이미지를 생성하고 Blob Storage에서 결과 스트림을 별도의 컨테이너에 기록합니다.

이 프로젝트에서는 트리거 형식에 `EventGridTrigger`를 사용합니다. 일반 HTTP 트리거보다는 Event Grid 트리거를 사용하는 것이 좋습니다. Event Grid는 Event Grid Function 트리거의 유효성을 자동으로 검사합니다. 일반 HTTP 트리거를 사용하면 [유효성 검사 응답](security-authentication.md)을 구현해야 합니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

이 함수에 대해 자세히 알아보려면 [function.json 및 run.csx 파일](https://github.com/Azure-Samples/function-image-upload-resize/tree/master/ImageFunctions)을 참조하세요.

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

이 함수에 대해 자세히 알아보려면 [function.json 및 index.js 파일](https://github.com/Azure-Samples/storage-blob-resize-function-node-v10/tree/master/Thumbnail)을 참조하세요.

---

함수 프로젝트 코드는 공개 샘플 리포지토리에서 직접 배포됩니다. Azure Functions의 배포 옵션에 대한 자세한 내용은 [Azure Functions에 대한 지속적인 배포](../azure-functions/functions-continuous-deployment.md)를 참조하세요.

## <a name="create-an-event-subscription"></a>이벤트 구독 만들기

이벤트 구독은 특정 엔드포인트로 보내려는 공급자 생성 이벤트를 나타냅니다. 이 경우 해당 함수에서 엔드포인트가 노출됩니다. Azure Portal에서 함수에 알림을 전송하는 이벤트 구독을 만들려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)의 페이지 상단에서 `Function App`을 검색하여 선택하고 방금 만든 함수 앱을 선택합니다. **Functions**를 선택하고 **썸네일** 함수를 선택합니다.

    :::image type="content" source="media/resize-images-on-storage-blob-upload-event/choose-thumbnail-function.png" alt-text="포털에서 썸네일 함수 선택":::

1.  **통합**을 선택한 다음, **Event Grid 트리거**를 선택하고 **Event Grid 구독 만들기**를 선택합니다.

    :::image type="content" source="./media/resize-images-on-storage-blob-upload-event/add-event-subscription.png" alt-text="Azure Portal에서 Event Grid 구독 추가로 이동" :::

1. 표에 지정된 대로 이벤트 구독 설정을 사용합니다.
    
    ![Azure Portal의 함수에서 이벤트 구독 만들기](./media/resize-images-on-storage-blob-upload-event/event-subscription-create.png)

    | 설정      | 제안 값  | Description                                        |
    | ------------ | ---------------- | -------------------------------------------------- |
    | **이름** | imageresizersub | 새 이벤트 구독을 식별하는 이름입니다. |
    | **항목 유형** | Storage 계정 | 스토리지 계정 이벤트 공급자를 선택합니다. |
    | **구독** | Azure 구독 | 기본적으로 현재 Azure 구독을 선택합니다. |
    | **리소스 그룹** | myResourceGroup | **기존 항목 사용**을 선택하고 이 자습서에서 사용한 리소스 그룹을 선택합니다. |
    | **리소스** | Blob Storage 계정 | 만든 Blob Storage 계정을 선택합니다. |
    | **시스템 토픽 이름** | imagestoragesystopic | 시스템 항목의 이름을 지정합니다. 시스템 항목에 대한 자세한 내용은 [시스템 토픽 개요](system-topics.md)를 참조하세요. |    
    | **이벤트 유형** | 만든 BLOB | 만든 **BLOB** 이외의 모든 유형을 선택 취소합니다. `Microsoft.Storage.BlobCreated` 이벤트 유형만 함수에 전달됩니다. |
    | **엔드포인트 유형** | autogenerated | **Azure 함수**로 미리 정의됩니다. |
    | **엔드포인트** | autogenerated | 함수의 이름입니다. 이 경우 **썸네일**입니다. |

1. **필터** 탭으로 전환하고 다음 작업을 수행합니다.
    1. **제목 필터링 사용** 옵션을 선택합니다.
    1. **제목 시작 문자**에 **/blobServices/default/containers/images/blobs/** 값을 입력합니다.

        ![이벤트 구독의 필터 지정](./media/resize-images-on-storage-blob-upload-event/event-subscription-filter.png)

1. **만들기**를 선택하여 이벤트 구독을 추가합니다. 그러면 `images` 컨테이너에 Blob이 추가될 때 `Thumbnail` 함수를 트리거하는 이벤트 구독이 만들어집니다. 함수는 이미지의 크기를 조정하고 `thumbnails` 컨테이너에 추가합니다.

이제 백엔드 서비스가 구성되었으므로 샘플 웹앱에서 이미지 크기 조정 기능을 테스트할 수 있습니다.

## <a name="test-the-sample-app"></a>샘플 앱 테스트

웹앱에서 이미지 크기 조정을 테스트하려면 게시된 앱의 URL로 이동합니다. 웹앱의 기본 URL은 `https://<web_app>.azurewebsites.net`입니다.

# <a name="net-v12-sdk"></a>[\.NET v12 SDK](#tab/dotnet)

**사진 업로드** 영역을 클릭하여 파일을 선택 및 업로드합니다. 이 영역에 사진을 끌어갈 수도 있습니다.

업로드된 이미지가 사라진 후에는 업로드된 이미지의 사본이 **생성된 미리 보기** 카루셀에 표시됩니다. 이 이미지는 함수를 통해 크기 조정되어 *썸네일* 컨테이너에 추가되었으며 웹 클라이언트가 다운로드한 것입니다.

![브라우저에 게시된 웹앱](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png)

# <a name="nodejs-v10-sdk"></a>[Node.js V10 SDK](#tab/nodejsv10)

**파일 선택**을 클릭하여 파일을 선택한 다음, **이미지 업로드**를 클릭합니다. 성공적으로 업로드되면 브라우저가 성공 페이지로 이동합니다. 홈 페이지로 돌아가려면 해당 링크를 클릭합니다. 업로드된 이미지의 복사본이 **생성된 썸네일** 영역에 표시됩니다. (처음에 이미지가 표시되지 않으면 페이지를 다시 로드해 보세요.) 이 이미지는 함수를 통해 크기 조정되어 *썸네일* 컨테이너에 추가되었으며 웹 클라이언트가 다운로드한 것입니다.

![브라우저에 게시된 웹앱](./media/resize-images-on-storage-blob-upload-event/upload-app-nodejs-thumb.png)

---

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 일반 Azure Storage 계정 만들기
> * Azure Functions를 사용하여 서버 없는 코드 배포
> * Event Grid에서 Blob Storage 이벤트 구독 만들기

스토리지 자습서 시리즈의 3부를 통해 스토리지 계정에 대한 보안 액세스를 학습합니다.

> [!div class="nextstepaction"]
> [클라우드에서 애플리케이션 데이터에 대한 보안 액세스](../storage/blobs/storage-secure-access-application.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

+ Event Grid에 대한 소개는 [Azure Event Grid](overview.md)를 참조하세요.
+ Azure Functions를 다룬 다른 자습서를 살펴보려면 [Azure Logic Apps와 통합하는 함수 만들기](../azure-functions/functions-twitter-email.md)를 참조하세요.

[previous-tutorial]: ../storage/blobs/storage-upload-process-images.md
