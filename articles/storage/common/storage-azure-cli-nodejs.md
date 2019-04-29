---
title: Azure Storage에서 Azure 클래식 CLI 사용 | Microsoft Docs
description: Azure Storage에서 Azure 클래식 CLI(명령줄 인터페이스)를 사용하여 스토리지 계정을 만들어 관리하고 Azure Blob 및 파일 작업을 수행하는 방법에 대해 알아봅니다.
services: storage
author: seguler
ms.service: storage
ms.topic: article
ms.date: 01/30/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: 211051254e08d69c06afd4242599c909048e7e17
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61483745"
---
# <a name="using-the-azure-classic-cli-with-azure-storage"></a>Azure Storage에서 Azure 클래식 CLI 사용

## <a name="overview"></a>개요

Azure 클래식 CLI는 Azure 플랫폼 작업을 위한 플랫폼 간 오픈 소스 명령 집합을 제공합니다. 다양한 데이터 액세스 기능뿐만 아니라 [Azure 포털](https://portal.azure.com) 에 있는 동일한 기능을 대부분 제공합니다.

이 가이드에서는 [Azure 클래식 CLI](../../cli-install-nodejs.md)를 사용하여 Azure Storage를 통해 다양한 개발 및 관리 작업을 수행하는 방법을 설명합니다. 이 가이드를 사용하기 전에 최신 Azure 클래식 CLI를 다운로드 및 설치하거나, 최신 버전으로 업그레이드하는 것이 좋습니다.

이 가이드에서는 Azure Storage의 기본 개념을 이해하고 있다고 가정합니다. 이 가이드는 Azure Storage에서 클래식 CLI를 사용하는 방법을 보여 주는 몇 가지 스크립트를 제공합니다. 각 스크립트를 실행하기 전에 구성에 따라 스크립트 변수를 업데이트 해야 합니다.

> [!NOTE]
> 이 가이드에서는 클래식 저장소 계정용 Azure 클래식 CLI 명령 및 스크립트 예제를 제공합니다. Resource Manager 저장소 계정용 Azure 클래식 CLI 명령은 [Azure Resource 관리에서 Mac, Linux 및 Windows용 Azure CLI 사용](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)을 참조하세요.
>
>

[!INCLUDE [storage-cli-versions](../../../includes/storage-cli-versions.md)]

## <a name="get-started-with-azure-storage-and-the-azure-classic-cli-in-5-minutes"></a>5분 안에 Azure Storage 및 Azure 클래식 CLI 시작하기
이 가이드에서는 예제를 보려면 Ubuntu를 사용하며 다른 OS 플랫폼에서도 유사하게 수행되어야 합니다.

**Azure를 처음 사용하는 경우:** Microsoft Azure 구독 및 해당 구독과 연결된 Microsoft 계정을 가져옵니다. Azure 구입 옵션에 대한 자세한 내용은 [무료 평가판](https://azure.microsoft.com/pricing/free-trial/), [구입 옵션](https://azure.microsoft.com/pricing/purchase-options/) 및 [회원 제안](https://azure.microsoft.com/pricing/member-offers/)(MSDN, Microsoft 파트너 네트워크, BizSpark 및 기타 Microsoft 프로그램의 회원인 경우)을 참조하세요.

Azure 구독에 대한 자세한 내용은 [Azure AD(Azure Active Directory)에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 을 참조하세요.

**Microsoft Azure 구독 및 계정을 만든 후:**

1. [Azure 클래식 CLI 설치](../../cli-install-nodejs.md)에 설명된 지침에 따라 Azure 클래식 CLI를 다운로드하여 설치합니다.
2. 클래식 CLI가 설치되었으면 명령줄 인터페이스(Bash, 터미널, 명령 프롬프트)에서 azure 명령을 사용하여 클래식 CLI 명령에 액세스할 수 있습니다. _azure_ 명령을 입력하면 다음 출력이 표시되어야 합니다.

    ![Azure 명령 출력](./media/storage-azure-cli/azure_command.png)   
3. 명령줄 인터페이스에 `azure storage`를 입력하여 모든 azure storage 명령을 나열한 다음 클래식 CLI에서 제공되는 기능을 파악합니다. 명령 이름에 **-h** 매개 변수를 입력하여(예: `azure storage share create -h`) 명령 구문에 대한 세부 정보를 볼 수 있습니다.
4. 이제 Azure Storage에 액세스할 수 있는 기본적인 클래식 CLI 명령을 보여 주는 간단한 스크립트를 살펴보겠습니다. 먼저 스크립트가 저장소 계정 및 키에 대한 두 변수를 설정할 것인지 묻습니다. 그런 다음 이 스크립트는 새 저장소 계정에 새 컨테이너를 만들고 해당 컨테이너에 기존 이미지 파일(Blob)을 업로드합니다. 스크립트가 해당 컨테이너의 모든 Blob을 나열한 후 로컬 컴퓨터에 있는 대상 디렉터리에 이미지 파일을 다운로드합니다.

    ```azurecli
    #!/bin/bash
    # A simple Azure storage example

    export AZURE_STORAGE_ACCOUNT=<storage_account_name>
    export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

    export container_name=<container_name>
    export blob_name=<blob_name>
    export image_to_upload=<image_to_upload>
    export destination_folder=<destination_folder>

    echo "Creating the container..."
    azure storage container create $container_name

    echo "Uploading the image..."
    azure storage blob upload $image_to_upload $container_name $blob_name

    echo "Listing the blobs..."
    azure storage blob list $container_name

    echo "Downloading the image..."
    azure storage blob download $container_name $blob_name $destination_folder

    echo "Done"
    ```

5. 로컬 컴퓨터에서 원하는 텍스트 편집기(예: vim)를 엽니다. 텍스트 편집기에 위의 스크립트를 입력합니다.
6. 이제, 구성 설정에 따라 스크립트 변수를 업데이트해야 합니다.

   * **&lt;storage_account_name&gt;** 스크립트에 지정된 이름을 사용하거나 사용자 스토리지 계정에 대한 새 이름을 입력합니다. **중요:** 스토리지 계정 이름은 Azure에서 고유해야 합니다. 소문자여야 합니다.
   * **&lt;storage_account_key&gt;** 스토리지 계정의 액세스 키.
   * **<container_name>** 스크립트에 지정된 이름을 사용하거나 사용자 컨테이너에 대한 새 이름을 입력합니다.
   * **<image_to_upload>** 로컬 컴퓨터의 그림 경로(예: "~/images/HelloWorld.png")를 입력합니다.
   * **<destination_folder>** Azure Storage에서 다운로드한 파일을 보관할 로컬 디렉터리의 경로(예: "~/downloadImages")를 입력합니다.
7. vim에서 필요한 변수를 업데이트 한 후 키 조합 `ESC`, `:`, `wq!`를 눌러 스크립트를 저장합니다.
8. 이 스크립트를 실행하려면 bash 콘솔에서 스크립트 파일 이름만 입력하면 됩니다. 스크립트가 실행된 후 다운로드한 이미지 파일을 포함하는 로컬 대상 폴더가 있어야 합니다. 다음 스크린샷은 예제 출력을 보여 줍니다.

스크립트가 실행된 후 다운로드한 이미지 파일을 포함하는 로컬 대상 폴더가 있어야 합니다.

## <a name="manage-storage-accounts-with-the-azure-classic-cli"></a>Azure 클래식 CLI를 사용하여 저장소 계정 관리
### <a name="connect-to-your-azure-subscription"></a>Azure 구독에 연결
대부분의 저장소 명령은 Azure 구독이 없어도 작동하지만 클래식 CLI에서 구독에 연결하는 것이 좋습니다.

### <a name="create-a-new-storage-account"></a>새 저장소 계정 만들기
Azure 저장소를 사용하려면 저장소 계정이 있어야 합니다. 구독에 연결하도록 컴퓨터를 구성한 후 새 Azure 저장소 계정을 만들 수 있습니다.

```azurecli
azure storage account create <account_name>
```

사용자의 저장소 계정 이름은 3자에서 24자 사이여야 하고 숫자 및 소문자만 사용해야 합니다.

### <a name="set-a-default-azure-storage-account-in-environment-variables"></a>환경 변수에서 기본 Azure 저장소 계정 설정
구독에서 여러 저장소 계정을 사용할 수 있습니다. 그중 하나를 선택하여 동일한 세션의 모든 저장소 명령에 대한 환경 변수에서 설정할 수 있습니다. 이렇게 하면 저장소 계정 및 키를 명시적으로 지정하지 않고도 클래식 CLI 저장소 명령을 실행할 수 있습니다.

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

기본 저장소 계정을 설정하는 다른 방법은 연결 문자열을 사용하는 것입니다. 첫째, 명령으로 연결 문자열을 가져옵니다.

```azurecli
azure storage account connectionstring show <account_name>
```

그런 다음 출력 연결 문자열을 복사 하여 환경 변수로 설정합니다.

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

## <a name="create-and-manage-blobs"></a>Blob 만들기 및 관리
Azure Blob Storage는 HTTP 또는 HTTPS를 통해 전 세계 어디에서든 액세스할 수 있는 다량의 구조화되지 않은 데이터(예: 텍스트 또는 이진 데이터)를 저장할 수 있는 서비스입니다. 이 섹션에서는 Azure Blob Storage 개념에 이미 익숙하다고 가정합니다. 자세한 내용은 [.NET을 사용하여 Azure Blob Storage 시작](../blobs/storage-dotnet-how-to-use-blobs.md) 및 [Blob Service 개념](https://msdn.microsoft.com/library/azure/dd179376.aspx)을 참조하세요.

### <a name="create-a-container"></a>컨테이너 만들기
Azure 저장소의 모든 Blob은 컨테이너에 있어야 합니다. `azure storage container create` 명령을 사용하여 개인 컨테이너를 만들 수 있습니다.

```azurecli
azure storage container create mycontainer
```

> [!NOTE]
> 익명 읽기 권한의 세 가지 수준은 **해제**, **Blob** 및 **컨테이너**입니다. Blob에 대한 익명 액세스를 방지하려면 권한 매개 변수를 **해제**로 설정합니다. 기본적으로 새 컨테이너는 전용이며 계정 소유자만 액세스할 수 있습니다. 익명 공용 읽기 권한을 Blob 리소스에 대해 허용하지만 컨테이너 메타데이터나 컨테이너의 Blob 목록에 대해서는 허용하지 않으려면, 사용 권한 매개 변수를 **Blob**으로 설정하세요. Blob 리소스, 컨테이너 메타데이터 및 컨테이너의 Blob 목록에 대한 전체 공용 읽기 권한을 허용하려면, 권한 매개 변수를 **컨테이너**로 설정하세요. 자세한 내용은 [컨테이너 및 Blob에 대한 익명 읽기 권한 관리](../blobs/storage-manage-access-to-resources.md)를 참조하세요.
>
>

### <a name="upload-a-blob-into-a-container"></a>컨테이너에 Blob 업로드
Azure Blob Storage는 블록 Blob 및 페이지 Blob을 지원합니다. 자세한 내용은 [블록 Blob, 추가 Blob 및 페이지 Blob 이해](https://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요.

컨테이너의 blob를 업로드하도록 `azure storage blob upload`을 사용할 수 있습니다. 기본적으로 이 명령은 로컬 파일을 블록 Blob에 업로드합니다. Blob의 종류를 지정하기 위해 `--blobtype` 매개 변수를 사용할 수 있습니다.

```azurecli
azure storage blob upload '~/images/HelloWorld.png' mycontainer myBlockBlob
```

### <a name="download-blobs-from-a-container"></a>컨테이너에서 Blob 다운로드
다음 예제에서는 컨테이너에서 Blob을 다운로드하는 방법을 보여 줍니다.

```azurecli
azure storage blob download mycontainer myBlockBlob '~/downloadImages/downloaded.png'
```

### <a name="copy-blobs"></a>Blob 복사
저장소 계정 및 지역 내 또는 전체에 걸쳐 비동기적으로 Blob을 복사할 수 있습니다.

다음 예제에서는 한 저장소 계정에서 다른 계정으로 Blob을 복사하는 방법을 보여줍니다. 이 샘플에서는 blob을 공개적으로 하는 컨테이너 만들어 익명으로 액세스할 수 있습니다.

```azurecli
azure storage container create mycontainer2 -a <accountName2> -k <accountKey2> -p Blob

azure storage blob upload '~/Images/HelloWorld.png' mycontainer2 myBlockBlob2 -a <accountName2> -k <accountKey2>

azure storage blob copy start 'https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2' mycontainer
```

이 예제에서는 비동기 복사를 수행합니다. `azure storage blob copy show` 작업을 실행하여 각 복사 작업의 상태를 모니터링할 수 있습니다.

복사 작업을 위해 제공되는 원본 URL에 공개적으로 액세스할 수 있도록 하거나 SAS(공유 액세스 서명) 토큰을 포함해야 합니다.

### <a name="delete-a-blob"></a>Blob 삭제
Blob을 삭제하려면 아래 명령을 사용하세요.

```azurecli
azure storage blob delete mycontainer myBlockBlob2
```

## <a name="create-and-manage-file-shares"></a>파일 공유 만들기 및 관리
Azure Files는 표준 SMB 프로토콜을 사용하여 애플리케이션을 위한 공유 스토리지를 제공합니다. Microsoft Azure 가상 머신 및 클라우드 서비스 그리고 온-프레미스 애플리케이션은 탑재된 공유를 통해 파일 데이터를 공유할 수 있습니다. 클래식 CLI를 통해 파일 공유 및 파일 데이터를 관리할 수 있습니다. Azure Files에 대한 자세한 내용은 [Azure Files 소개](../files/storage-files-introduction.md)를 참조하세요.

### <a name="create-a-file-share"></a>파일 공유 만들기
Azure에서 Azure 파일 공유는 SMB 파일 공유입니다. 모든 디렉터리 및 파일을 파일 공유에서 만들어야 합니다. 계정에 포함할 수 있는 공유 수에는 제한이 없으며, 공유에 저장할 수 있는 파일 수에는 저장소 계정의 최대 용량 한도까지 제한이 없습니다. 다음 예제에서는 **myshare**라는 파일 공유를 만듭니다.

```azurecli
azure storage share create myshare
```

### <a name="create-a-directory"></a>디렉터리 만들기
디렉터리는 Azure 파일 공유에 대한 선택적 계층적 구조를 제공합니다. 다음 예에서는 파일 공유에 **myDir** 이라는 디렉터리를 만듭니다.

```azurecli
azure storage directory create myshare myDir
```

해당 디렉터리 경로에 여러 수준이 포함 될 수 있습니다( *예:* **a/b**). 그러나 모든 부모 디렉터리가 존재하는지 확인해야 합니다. 예를 들어, 경로 **a/b**의 경우, 먼저 디렉터리 **a**를 만든 다음, **b** 디렉터리를 만듭니다.

### <a name="upload-a-local-file-to-directory"></a>디렉터리에 로컬 파일 업로드
다음 예제에서는 **~/temp/samplefile.txt**에서 **myDir** 디렉터리로 파일을 업로드합니다. 로컬 컴퓨터의 유효한 파일을 가리키도록 파일 경로를 편집합니다.

```azurecli
azure storage file upload '~/temp/samplefile.txt' myshare myDir
```

공유 중인 파일 하나는 최대 1TB일 수 있습니다.

### <a name="list-the-files-in-the-share-root-or-directory"></a>공유 루트 또는 디렉터리의 파일 목록
다음 명령을 사용하여 공유 루트 또는 디렉터리에 있는 파일 및 하위 디렉터리를 나열할 수 있습니다.

```azurecli
azure storage file list myshare myDir
```

나열 작업에 대해 디렉터리 이름은 선택적입니다. 생략하면 명령이 공유의 루트 디렉터리의 내용을 나열합니다.

### <a name="copy-files"></a>파일 복사
클래식 CLI 버전 0.9.8부터는 파일을 다른 파일로, 파일을 Blob으로 또는 Blob을 파일로 복사할 수 있습니다. 아래에는 CLI 명령을 사용하여 이러한 복사 작업을 수행하는 방법이 나와 있습니다. 새 디렉터리에 파일을 복사하려면 다음을 수행합니다.

```azurecli
azure storage file copy start --source-share srcshare --source-path srcdir/hello.txt --dest-share destshare
    --dest-path destdir/hellocopy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

파일 디렉터리에 Blob을 복사하려면 다음을 수행합니다.

```azurecli
azure storage file copy start --source-container srcctn --source-blob hello2.txt --dest-share hello
    --dest-path hellodir/hello2copy.txt --connection-string $srcConnectionString --dest-connection-string $destConnectionString
```

## <a name="next-steps"></a>다음 단계

Storage 리소스 사용을 위한 Azure 클래식 CLI 명령 참조는 다음 항목에서 확인할 수 있습니다.

* [Resource Manager 모드의 Azure 클래식 CLI 명령](../../virtual-machines/azure-cli-arm-commands.md#azure-storage-commands-to-manage-your-storage-objects)
* [Azure 서비스 관리 모드의 Azure CLI 명령](../../cli-install-nodejs.md)

Resource Manager 배포 모델에서 [Azure CLI](../storage-azure-cli.md)의 최신 버전을 사용해 볼 수도 있습니다.
