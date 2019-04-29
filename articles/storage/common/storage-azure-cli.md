---
title: Azure Storage에서 Azure CLI 사용 | Microsoft Docs
description: Azure Storage에서 Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Storage 계정을 만들어 관리하고 Azure blob과 파일 작업을 수행하는 방법에 대해 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.devlang: azurecli
ms.topic: article
ms.date: 06/02/2017
ms.author: rogarana
ms.subservice: common
ms.openlocfilehash: f485f38d4c580937b027bb76d0c34c98f699ed93
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483575"
---
# <a name="using-the-azure-cli-with-azure-storage"></a>Azure Storage에서 Azure CLI 사용

플랫폼 간 오픈 소스 Azure CLI는 Azure 플랫폼을 사용하기 위한 명령 집합을 제공합니다. 풍부한 데이터 액세스를 포함하여 [Azure Portal](https://portal.azure.com)과 동일한 기능을 제공합니다.

이 가이드에서는 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)를 통해 Azure Storage 계정의 리소스를 사용하는 몇 가지 작업을 수행하는 방법을 설명합니다. 이 가이드를 사용하기 전에 최신 버전의 CLI를 다운로드하여 설치하거나 업그레이드하는 것이 좋습니다.

이 가이드의 예제에서는 Ubuntu에서 Bash 셸을 사용한다고 가정하지만 다른 플랫폼에서도 비슷하게 수행해야 합니다. 

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>필수 조건
이 가이드에서는 Azure Storage의 기본 개념을 이해하고 있다고 가정합니다. 또한 Azure와 Storage 서비스에 대해 아래에 지정된 계정 만들기 요구 사항을 충족할 수 있다고 가정합니다.

### <a name="accounts"></a>계정
* **Azure 계정**: Azure 구독이 아직 없는 경우 [무료 Azure 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **스토리지 계정**: [Azure Storage 계정 정보](storage-create-storage-account.md)에서 [Storage 계정 만들기](storage-quickstart-create-account.md)를 참조하세요.

### <a name="install-the-azure-cli"></a>Azure CLI 설치

[Azure CLI 설치](/cli/azure/install-az-cli2)에 설명된 지침에 따라 Azure CLI를 다운로드하여 설치합니다.

> [!TIP]
> 설치하는 데 문제가 있으면 관련 문서의 [설치 문제 해결](/cli/azure/install-az-cli2) 섹션 및 GitHub의 [설치 문제 해결](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) 가이드를 참조하세요.
>

## <a name="working-with-the-cli"></a>CLI 사용

CLI를 설치했으면 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에서 `az` 명령을 사용하여 Azure CLI 명령에 액세스할 수 있습니다. `az` 명령을 입력하여 기본 명령의 전체 목록을 확인합니다(다음 예제 출력이 잘림).

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account          : Manage subscriptions.
    acr              : Manage Azure container registries.
    acs              : Manage Azure Container Services.
    ad               : Synchronize on-premises directories and manage Azure Active Directory
                       resources.
    ...
```

명령줄 인터페이스에서 `az storage --help` 명령을 실행하여 `storage` 명령 하위 그룹을 나열합니다. 하위 그룹에 대한 설명은 Azure CLI에서 저장소 리소스 작업을 위해 제공하는 기능에 대한 개요입니다.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Origin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Use queues to effectively scale applications according to traffic.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure 구독에 CLI 연결

Azure 구독에 있는 리소스를 사용하려면 먼저 `az login`으로 Azure 계정에 로그인해야 합니다. 로그인할 수 있는 몇 가지 방법은 다음과 같습니다.

* **대화형 로그인**: `az login`
* **사용자 이름 및 암호로 로그인**: `az login -u johndoe@contoso.com -p VerySecret`
  * Microsoft 계정 또는 다단계 인증을 사용하는 계정에서는 작동하지 않습니다.
* **서비스 주체로 로그인**: `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-sample-script"></a>Azure CLI 샘플 스크립트

다음으로 Azure Storage 리소스와 상호 작용하는 몇 가지 기본 Azure CLI 명령을 발급하는 작은 셸 스크립트를 사용합니다. 이 스크립트는 먼저 저장소 계정에 새 컨테이너를 만든 다음 해당 컨테이너에 기존 파일(Blob)을 업로드합니다. 그런 다음 컨테이너에 있는 모든 Blob을 나열하고, 마지막으로 사용자가 지정한 로컬 컴퓨터의 대상에 파일을 다운로드합니다.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create --name $container_name

echo "Uploading the file..."
az storage blob upload --container-name $container_name --file $file_to_upload --name $blob_name

echo "Listing the blobs..."
az storage blob list --container-name $container_name --output table

echo "Downloading the file..."
az storage blob download --container-name $container_name --name $blob_name --file $destination_file --output table

echo "Done"
```

**스크립트 구성 및 실행**

1. 원하는 텍스트 편집기를 연 다음 앞서의 스크립트를 복사하여 편집기에 붙여넣습니다.

2. 다음으로 구성 설정을 반영하도록 스크립트의 변수를 업데이트합니다. 다음 값을 지정한 대로 바꿉니다.

   * **\<storage_account_name\>** 스토리지 계정의 이름입니다.
   * **\<storage_account_key\>** 스토리지 계정의 기본 또는 보조 액세스 키입니다.
   * **\<container_name\>** 새로 만들 컨테이너의 이름입니다(예: "azure-cli-sample-container").
   * **\<Blob_name\>** 컨테이너에 있는 대상 Blob의 이름입니다.
   * **\<file_to_upload\>** 로컬 컴퓨터의 작은 파일 경로입니다(예: "~/images/HelloWorld.png").
   * **\<destination_file\>** 대상 파일 경로입니다(예: "~/downloadedImage.png").

3. 필요한 변수를 업데이트한 후 해당 스크립트를 저장하고 편집기를 종료합니다. 다음 단계에서는 스크립트 이름으로 **my_storage_sample.sh**를 지정했다고 가정합니다.

4. 필요한 경우 다음과 같이 스크립트를 실행 가능으로 표시합니다. `chmod +x my_storage_sample.sh`

5. 스크립트를 실행합니다. 예를 들어 Bash에서는 `./my_storage_sample.sh`입니다.

다음과 비슷한 출력이 표시되며, 스크립트에 지정한 **\<destination_file\>** 이 로컬 컴퓨터에 나타납니다.

```
Creating the container...
{
  "created": true
}
Uploading the file...
Percent complete: %100.0
Listing the blobs...
Name       Blob Type      Length  Content Type              Last Modified
---------  -----------  --------  ------------------------  -------------------------
README.md  BlockBlob        6700  application/octet-stream  2017-05-12T20:54:59+00:00
Downloading the file...
Name
---------
README.md
Done
```

> [!TIP]
> 앞서의 출력은 **테이블** 형식입니다. CLI 명령에서 `--output` 인수를 지정하여 사용할 출력 형식을 지정하거나 `az configure`를 사용하여 전역으로 설정할 수 있습니다.
>

## <a name="manage-storage-accounts"></a>저장소 계정 관리

### <a name="create-a-new-storage-account"></a>새 저장소 계정 만들기
Azure Storage를 사용하려면 스토리지 계정이 필요합니다. 구독에 연결하도록 컴퓨터를 구성한 후에 새 Azure Storage 계정을 만들 수 있습니다.

```azurecli
az storage account create \
    --location <location> \
    --name <account_name> \
    --resource-group <resource_group> \
    --sku <account_sku>
```

* `--location`[필수]: 위치입니다. 예를 들어 "미국 서부"를 선택합니다.
* `--name`[필수]: 저장소 계정 이름입니다. 이름의 길이는 3-24자여야 하며, 소문자 영숫자만 사용합니다.
* `--resource-group`[필수]: 리소스 그룹의 이름입니다.
* `--sku`[필수]: 스토리지 계정 SKU입니다. 허용되는 값은 다음과 같습니다.
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>기본 Azure Storage 계정 환경 변수 설정

Azure 구독에서 여러 저장소 계정을 사용할 수 있습니다. 모든 후속 저장소 명령에 사용하기 위해 이러한 계정 중 하나를 선택하려면 환경 변수를 다음과 같이 설정할 수 있습니다.

먼저, [az storage account keys list](/cli/azure/storage/account/keys) 명령을 사용하여 스토리지 계정 키를 표시합니다.

```azurecli-interactive
az storage account keys list \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --output table
```

이제 키가 있으므로 키 및 계정 이름을 환경 변수로 정의할 수 있습니다.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_KEY=<key>
```

기본 저장소 계정을 설정하는 또 다른 방법은 연결 문자열을 사용하는 것입니다. 먼저 `show-connection-string` 명령으로 연결 문자열을 가져옵니다.

```azurecli
az storage account show-connection-string \
    --name <account_name> \
    --resource-group <resource_group>
```

그런 다음 출력 연결 문자열을 복사하고 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수를 설정합니다(연결 문자열을 따옴표로 묶어야 할 수도 있음).

```azurecli
export AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
```

> [!NOTE]
> 이 문서의 다음 섹션에 나오는 모든 예제에서는 `AZURE_STORAGE_ACCOUNT` 및 `AZURE_STORAGE_KEY` 환경 변수를 설정했다고 가정합니다.

## <a name="create-and-manage-blobs"></a>Blob 만들기 및 관리
Azure Blob Storage는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있는 다량의 구조화되지 않은 데이터(예: 텍스트 또는 이진 데이터)를 저장할 수 있는 서비스입니다. 이 섹션에서는 Azure Blob Storage 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [.NET을 사용하여 Azure Blob Storage 시작](../blobs/storage-dotnet-how-to-use-blobs.md) 및 [Blob Service 개념](/rest/api/storageservices/blob-service-concepts)을 참조하세요.

### <a name="create-a-container"></a>컨테이너 만들기
Azure 저장소의 모든 Blob은 컨테이너에 있어야 합니다. `az storage container create` 명령을 사용하면 컨테이너를 만들 수 있습니다.

```azurecli
az storage container create --name <container_name>
```

선택적인 `--public-access` 인수를 지정하면 새 컨테이너에 대한 읽기 액세스의 다음 세 가지 수준 중 하나를 설정할 수 있습니다.

* `off`(기본값): 컨테이너 데이터가 계정 소유자 전용입니다.
* `blob`: Blob에 대한 공용 읽기 액세스입니다.
* `container`: 전체 컨테이너에 대한 공용 읽기 및 목록 액세스입니다.

자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.

### <a name="upload-a-blob-to-a-container"></a>컨테이너에 Blob 업로드
Azure Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. `blob upload` 명령을 사용하여 컨테이너에 Blob을 업로드합니다.

```azurecli
az storage blob upload \
    --file <local_file_path> \
    --container-name <container_name> \
    --name <blob_name>
```

저장소 계정의 컨테이너 내에 있는 폴더에 직접 업로드하려면 `--name <blob_name>`을 `--name <folder/blob_name>`으로 바꿉니다.

 기본적으로 `blob upload` 명령은 페이지 Blob에 *.vhd 파일을 업로드하며, 그렇지 않으면 블록 Blob에 업로드합니다. Blob을 업로드할 때 다른 유형을 지정하려면 `--type` 인수를 사용할 수 있으며, 허용되는 값은 `append`, `block` 및 `page`입니다.

 Blob에 대한 자세한 내용은 [블록 Blob, 추가 Blob 및 페이지 Blob 이해](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)를 참조하세요.


### <a name="download-a-blob-from-a-container"></a>컨테이너에서 Blob 다운로드
다음 예제에서는 컨테이너에서 Blob을 다운로드하는 방법을 보여 줍니다.

```azurecli
az storage blob download \
    --container-name mycontainer \
    --name myblob.png \
    --file ~/mydownloadedblob.png
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[az storage blob list](/cli/azure/storage/blob) 명령으로 컨테이너에 있는 Blob을 나열합니다.

```azurecli
az storage blob list \
    --container-name mycontainer \
    --output table
```

### <a name="copy-blobs"></a>Blob 복사
저장소 계정 및 지역 내 또는 전체에 걸쳐 비동기적으로 Blob을 복사할 수 있습니다.

다음 예제에서는 한 저장소 계정에서 다른 계정으로 Blob을 복사하는 방법을 보여줍니다. 먼저 해당 Blob에 대해 공용 읽기 액세스를 지정하여 원본 저장소 계정에 컨테이너를 만듭니다. 그런 다음 컨테이너에 파일을 업로드하고, 마지막으로 해당 컨테이너의 Blob을 대상 저장소 계정의 컨테이너에 복사합니다.

```azurecli
# Create container in source account
az storage container create \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --name sourcecontainer \
    --public-access blob

# Upload blob to container in source account
az storage blob upload \
    --account-name sourceaccountname \
    --account-key sourceaccountkey \
    --container-name sourcecontainer \
    --file ~/Pictures/sourcefile.png \
    --name sourcefile.png

# Copy blob from source account to destination account (destcontainer must exist)
az storage blob copy start \
    --account-name destaccountname \
    --account-key destaccountkey \
    --destination-blob destfile.png \
    --destination-container destcontainer \
    --source-uri https://sourceaccountname.blob.core.windows.net/sourcecontainer/sourcefile.png
```

위의 예제에서는 복사 작업이 성공하기 위해 대상 컨테이너가 대상 저장소 계정에 이미 있어야 합니다. 또한 `--source-uri` 인수에 지정된 원본 Blob는 SAS(공유 액세스 서명) 토큰을 포함하거나 이 예제에서처럼 공개적으로 액세스할 수 있어야 합니다.

### <a name="delete-a-blob"></a>Blob 삭제
Blob을 삭제하려면 `blob delete` 명령을 사용합니다.

```azurecli
az storage blob delete --container-name <container_name> --name <blob_name>
```

## <a name="create-and-manage-file-shares"></a>파일 공유 만들기 및 관리
Azure Files는 SMB(서버 메시지 블록) 프로토콜을 사용하는 애플리케이션을 위한 공유 스토리지를 제공합니다. Microsoft Azure 가상 머신 및 클라우드 서비스 그리고 온-프레미스 애플리케이션은 탑재된 공유를 통해 파일 데이터를 공유할 수 있습니다. Azure CLI를 통해 파일 공유 및 파일 데이터를 관리할 수 있습니다. Azure Files에 대한 자세한 내용은 [Azure Files 소개](../files/storage-files-introduction.md)를 참조하세요.

### <a name="create-a-file-share"></a>파일 공유 만들기
Azure에서 Azure 파일 공유는 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 파일 공유에서 만들어야 합니다. 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다. 다음 예제에서는 **myshare**라는 파일 공유를 만듭니다.

```azurecli
az storage share create --name myshare
```

### <a name="create-a-directory"></a>디렉터리 만들기
디렉터리는 Azure 파일 공유에 대한 계층 구조를 제공합니다. 다음 예에서는 파일 공유에 **myDir** 이라는 디렉터리를 만듭니다.

```azurecli
az storage directory create --name myDir --share-name myshare
```

디렉터리 경로에는 여러 수준이 포함 될 수 있습니다(예:**dir1/dir2**). 그러나 하위 디렉터리를 만들기 전에 모든 부모 디렉터리가 존재하는지 확인해야 합니다. 예를 들어, 경로 **dir1/dir2**의 경우, 먼저 **dir1** 디렉터리를 만든 다음, **dir2** 디렉터리를 만듭니다.

### <a name="upload-a-local-file-to-a-share"></a>공유에 로컬 파일 업로드
다음 예제에서는 **~/temp/samplefile.txt**에서 **myshare** 파일 공유의 루트로 파일을 업로드합니다. `--source` 인수는 업로드할 기존 로컬 파일을 지정합니다.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

디렉터리를 만드는 것과 마찬가지로 다음과 같이 공유 내의 디렉터리 경로를 지정하여 공유 내의 기존 디렉터리에 파일을 업로드할 수 있습니다.

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

공유에 있는 파일의 크기는 각각 최대 1TB일 수 있습니다.

### <a name="list-the-files-in-a-share"></a>공유에 있는 파일 나열
`az storage file list` 명령을 사용하여 공유에 있는 파일과 디렉터리를 나열할 수 있습니다.

```azurecli
# List the files in the root of a share
az storage file list --share-name myshare --output table

# List the files in a directory within a share
az storage file list --share-name myshare/myDir --output table

# List the files in a path within a share
az storage file list --share-name myshare --path myDir/mySubDir/MySubDir2 --output table
```

### <a name="copy-files"></a>파일 복사      
파일을 다른 파일로, 파일을 Blob으로 또는 Blob을 파일로 복사할 수 있습니다. 예를 들어 파일을 다른 공유의 디렉터리에 복사하려면 다음과 같이 수행합니다.        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt     
```

## <a name="create-share-snapshot"></a>공유 스냅숏 만들기
`az storage share snapshot` 명령을 사용하면 공유 스냅숏을 만들 수 있습니다.

```cli
az storage share snapshot -n <share name>
```

샘플 출력
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="list-share-snapshots"></a>공유 스냅숏 나열

`az storage share list --include-snapshots`를 사용하여 특정 공유의 공유 스냅숏을 나열할 수 있습니다.

```cli
az storage share list --include-snapshots
```

**샘플 출력**
```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

### <a name="browse-share-snapshots"></a>공유 스냅숏 찾아보기
`az storage file list`를 사용하여 특정 공유 스냅숏을 찾아서 해당 내용을 볼 수도 있습니다. 공유 이름 `--share-name <snare name>` 및 타임스탬프 `--snapshot '2017-10-04T19:45:18.0000000Z'`를 지정해야 합니다.

```azurecli-interactive
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

**샘플 출력**
```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
### <a name="restore-from-share-snapshots"></a>공유 스냅숏에서 복원

`az storage file download` 명령을 사용하여 공유 스냅숏에서 파일을 복사 또는 다운로드하여 파일을 복원할 수 있습니다.

```azurecli-interactive
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```
**샘플 출력**
```
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```
## <a name="delete-share-snapshot"></a>공유 스냅숏 삭제
`--snapshot` 매개 변수에 공유 스냅숏 타임스탬프를 제공하면 `az storage share delete` 명령을 사용하여 공유 스냅숏을 삭제할 수 있습니다.

```cli
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

샘플 출력
```json
{
  "deleted": true
}
```

## <a name="next-steps"></a>다음 단계
Azure CLI를 사용하는 방법에 대해 자세히 알아볼 수 있는 몇 가지 추가 리소스는 다음과 같습니다. 

* [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Azure CLI 명령 참조](/cli/azure)
* [GitHub의 Azure CLI](https://github.com/Azure/azure-cli)
