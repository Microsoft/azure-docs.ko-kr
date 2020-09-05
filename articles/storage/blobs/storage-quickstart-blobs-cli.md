---
title: 빠른 시작 - Azure CLI를 사용하여 Blob 만들기
titleSuffix: Azure Storage
description: 이 빠른 시작에서는 Azure CLI를 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: 55cbf0a304bbf13d47fefad0981c0143c101bbb0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88520773"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Blob 생성, 다운로드 및 나열

Azure CLI는 Azure 리소스를 관리하는 Azure의 명령줄 환경입니다. 브라우저에서 Azure Cloud Shell과 함께 사용할 수 있습니다. macOS, Linux 또는 Windows에 설치하여 명령줄에서 실행할 수도 있습니다. 빠른 시작에서는 Azure CLI를 사용하여 Azure Blob Storage에서 데이터를 업로드 및 다운로드하는 방법을 알아봅니다.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>로컬로 Azure CLI 설치

Azure CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 빠른 시작에서는 Azure CLI 버전 2.0.46 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

Azure CLI를 로컬로 실행하는 경우 로그인하여 인증해야 합니다. Azure Cloud Shell을 사용하는 경우 이 단계가 필요하지 않습니다. Azure CLI에 로그인하려면 `az login`을 실행하고 브라우저 창에서 인증합니다.

```azurecli
az login
```

Azure CLI의 인증에 대한 자세한 내용은 [Azure CLI를 사용하여 로그인](/cli/azure/authenticate-azure-cli)을 참조하세요.

## <a name="authorize-access-to-blob-storage"></a>Blob 스토리지에 대한 액세스 권한 부여

Azure AD 자격 증명을 사용하거나 스토리지 계정 액세스 키를 사용하여 Azure CLI에서 Blob 스토리지에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 자격 증명을 사용하는 것이 좋습니다. 이 문서에서는 Azure AD를 사용하여 Blob 스토리지 작업에 권한을 부여하는 방법을 보여 줍니다.

Blob 스토리지에 대한 데이터 작업에 사용하는 Azure CLI 명령은 지정된 작업에 권한을 부여하는 방법을 지정할 수 있는 `--auth-mode` 매개 변수를 지원합니다. Azure AD 자격 증명을 사용하여 권한을 부여하려면 `--auth-mode` 매개 변수를 `login`으로 설정합니다. 자세한 내용은 [Azure CLI를 사용하여 Blob 또는 큐 데이터에 대한 액세스 권한 부여](../common/authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.

Blob 스토리지 데이터 작업만 `--auth-mode` 매개 변수를 지원합니다. 리소스 그룹 또는 스토리지 계정 만들기와 같은 관리 작업은 Azure AD 자격 증명을 권한 부여에 자동으로 사용합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group) 명령을 사용하여 Azure 리소스 그룹을 만듭니다. 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

[az storage account create](/cli/azure/storage/account) 명령을 사용하여 범용 스토리지 계정을 만듭니다. 범용 스토리지 계정은 4개의 모든 서비스(Blob, 파일, 테이블 및 큐)에 사용할 수 있습니다.

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>컨테이너 만들기

Blob은 항상 컨테이너에 업로드됩니다. 컴퓨터의 폴더에서 파일을 구성하는 것과 비슷한 방식으로 컨테이너에서 Blob 그룹을 구성할 수 있습니다. Blob 저장을 위한 컨테이너는 [az storage container create](/cli/azure/storage/container) 명령을 사용하여 만듭니다.

다음 예제에서는 Azure AD 계정을 사용하여 컨테이너를 만드는 작업에 권한을 부여합니다. 컨테이너를 만들기 전에 [Storage Blob 데이터 참가자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) 역할을 자신에게 할당합니다. 계정 소유자인 경우에도 스토리지 계정에 대한 데이터 작업을 수행하려면 명시적 권한이 필요합니다. Azure 역할을 할당하는 방법에 대한 자세한 내용은 [Azure CLI 사용하여 액세스에 대한 Azure 역할 할당](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json)을 참조하세요.  

꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Azure 역할 할당을 전파하는 데 몇 분 정도 걸릴 수 있습니다.

스토리지 계정 키를 사용하여 컨테이너를 만들도록 작업에 권한을 부여할 수도 있습니다. Azure CLI 사용하여 데이터 작업에 권한을 부여하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 blob 또는 큐 데이터에 대한 액세스 권한 부여](../common/authorize-data-operations-cli.md?toc=/azure/storage/blobs/toc.json)를 참조하세요.

## <a name="upload-a-blob"></a>Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 이 빠른 시작의 예제에서는 블록 Blob을 사용하는 방법을 보여 줍니다.

먼저 Blob에 업로드할 파일을 만듭니다. Azure Cloud Shell을 사용하는 경우 다음 명령을 사용하여 파일을 만듭니다.

```bash
vi helloworld
```

파일이 열리면 **삽입**을 누릅니다. *Hello World*를 입력한 다음, **Esc** 키를 누릅니다. 그런 다음, *:x*를 입력하고 **Enter** 키를 누릅니다.

이 예제에서는 [az storage blob upload](/cli/azure/storage/blob) 명령을 사용하여 마지막 단계에서 만든 컨테이너에 Blob을 업로드합니다. 파일을 루트 디렉터리에 만들었으므로 파일 경로를 지정할 필요가 없습니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

이 작업은 Blob이 없는 경우 만들고, Blob이 있는 경우 덮어씁니다. 원하는 만큼 파일을 업로드한 후 계속합니다.

동시에 여러 파일을 업로드하려면 [az storage blob upload-batch](/cli/azure/storage/blob) 명령을 사용할 수 있습니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[az storage blob list](/cli/azure/storage/blob) 명령으로 컨테이너에 있는 Blob을 나열합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Blob 다운로드

[az storage blob download](/cli/azure/storage/blob) 명령을 사용하여 이전에 업로드한 Blob을 다운로드합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>AzCopy를 사용한 데이터 전송

AzCopy 명령줄 유틸리티는 Azure Storage에 대한 스크립트 가능한 고성능 데이터 전송을 제공합니다. AzCopy를 사용하여 Blob 스토리지와 Azure Files 간에 데이터를 전송할 수 있습니다. 최신 버전의 AzCopy인 AzCopy v10에 대한 자세한 내용은 [AzCopy 시작](../common/storage-use-azcopy-v10.md)을 참조하세요. Blob 스토리지에서 AzCopy v10을 사용하는 방법에 대한 자세한 내용은 [AzCopy 및 Blob 스토리지를 사용하여 데이터 전송](../common/storage-use-azcopy-blobs.md)을 참조하세요.

다음 예제에서는 AzCopy를 사용하여 로컬 파일을 Blob에 업로드합니다. 샘플 값을 사용자 고유의 값으로 바꿔야 합니다.

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>리소스 정리

스토리지 계정을 포함하여 이 빠른 시작의 일부로 만든 리소스를 삭제하려면 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹을 삭제합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 로컬 파일 시스템과 Azure Blob 스토리지의 컨테이너 간에 파일을 전송하는 방법을 알아보았습니다. Azure CLI를 통해 Blob 스토리지를 사용하는 방법에 대해 자세히 알아보려면 Blob 스토리지용 Azure CLI 샘플을 살펴보세요.

> [!div class="nextstepaction"]
> [Blob 스토리지용 Azure CLI 샘플](/azure/storage/blobs/storage-samples-blobs-cli?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
