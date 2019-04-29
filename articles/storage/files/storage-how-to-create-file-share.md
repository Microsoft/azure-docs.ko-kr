---
title: Azure 파일 공유를 만드는 방법 | Microsoft Docs
description: Azure Portal, PowerShell 및 Azure CLI를 사용하여 Azure 파일에 Azure 파일 공유를 만드는 방법입니다.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6440e5c2cf534ea431387bcdfed8b218919bf627
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766288"
---
# <a name="create-a-file-share-in-azure-files"></a>Azure Files에 파일 공유 만들기
 [Azure Portal](https://portal.azure.com/), Azure Storage PowerShell cmdlet, Azure Storage 클라이언트 라이브러리 또는 Azure Storage REST API를 사용하여 Azure 파일 공유를 만들 수 있습니다. 이 자습서에서는 다음에 대해 알아봅니다.
* Azure Portal을 사용하여 Azure 파일 공유를 만드는 방법
* [Powershell을 사용하여 Azure 파일 공유를 만드는 방법](#create-file-share-through-powershell)
* [CLI를 사용하여 Azure 파일 공유를 만드는 방법](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>필수 조건
Azure 파일 공유를 만들려면 이미 존재하는 저장소 계정을 사용하거나 [새 Azure Storage 계정을 만들](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 수 있습니다. PowerShell을 사용하여 Azure 파일 공유를 만들려면 저장소 계정의 계정 키와 이름이 필요합니다. Powershell 또는 CLI를 사용하려면 스토리지 계정 키가 필요합니다.

## <a name="create-a-file-share-through-the-azure-portal"></a>Azure Portal을 통해 파일 공유 만들기
1. **Azure Portal에서 스토리지 계정 블레이드로 이동합니다**.    
    ![저장소 계정 블레이드](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **파일 공유 추가 단추를 클릭합니다**.    
    ![파일 공유 단추 클릭](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **이름과 할당량을 제공합니다. 할당량의 현재 최댓값은 5TiB입니다**.    
    ![새 파일 공유에 대한 이름과 원하는 할당량 제공](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **새 파일 공유 보기**:  ![새 파일 공유 보기](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **파일 업로드**:  ![파일 업로드](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **파일 공유를 찾아보고 디렉터리와 파일을 관리합니다**.  ![파일 공유 찾아보기](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>PowerShell 통해 파일 공유 만들기

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

PowerShell을 사용하려면 Azure PowerShell cmdlet을 다운로드하여 설치합니다. 설치 포인트와 설치 지침에 대해서는  [Azure PowerShell 설치 및 구성 방법](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) 을 참조하세요.

> [!Note]  
> 최신 Azure PowerShell 모듈을 다운로드하여 설치하거나 최신 모듈로 업그레이드하는 것이 좋습니다.

1. **저장소 계정 및 키에 대한 컨텍스트를 만듭니다**. 컨텍스트는 저장소 계정 이름과 계정 키를 캡슐화합니다.  [Azure Portal](https://portal.azure.com/)에서 계정 키를 복사하는 방법에 대한 지침은  [스토리지 계정 액세스 키](../common/storage-account-manage.md#access-keys)를 참조하세요.

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **새 파일 공유를 만듭니다**.    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은  [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](https://msdn.microsoft.com/library/azure/dn167011.aspx)를 참조하세요.

## <a name="create-file-share-through-command-line-interface-cli"></a>CLI(명령줄 인터페이스)를 통해 파일 공유 만들기
1. **명령줄 인터페이스(CLI)를 사용하도록 준비하려면 Azure CLI를 다운로드하여 설치합니다.**  
     [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 및 [Azure CLI 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조하세요.

2. **공유를 만들 저장소 계정에 연결 문자열을 만듭니다.**  
    다음 예제에서는 ```<storage-account>``` 및 ```<resource_group>```을 사용자의 스토리지 계정 이름 및 리소스 그룹으로 바꿉니다.

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **파일 공유 만들기**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>다음 단계
* [파일 공유 연결 및 탑재 - Windows](storage-how-to-use-files-windows.md)
* [파일 공유 연결 및 탑재 - Linux](../storage-how-to-use-files-linux.md)
* [파일 공유 연결 및 탑재 - macOS](storage-how-to-use-files-mac.md)

Azure Files에 대한 자세한 내용은 다음 링크를 참조합니다.

* [FAQ](../storage-files-faq.md)
* [Windows에서 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)      
* [Linux에서 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)   
