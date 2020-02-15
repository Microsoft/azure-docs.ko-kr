---
title: Azure Storage 마이그레이션 FAQ | Microsoft Docs
description: Azure Storage 마이그레이션에 대한 일반적인 질문과 대답입니다.
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 1445d74e3050ffd6da7c45037df552f4bee9acf5
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116677"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Azure Storage 마이그레이션에 대한 FAQ(질문과 대답)

이 문서에서는 Azure Storage 마이그레이션에 대한 일반적인 질문과 대답을 제공합니다.

## <a name="copy-upload-or-download"></a>복사, 업로드 또는 다운로드

**한 컨테이너에서 다른 컨테이너로 파일을 복사하는 스크립트를 만들려면 어떻게 할까요?**

컨테이너 간에 파일을 복사하려면 AzCopy를 사용할 수 있습니다. 다음 예제를 참조하십시오.

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy는 [Blob API 복사](https://docs.microsoft.com/rest/api/storageservices/copy-blob)를 사용하여 컨테이너의 각 파일을 복사합니다.  

인터넷에 액세스할 수 있는 모든 가상 머신 또는 로컬 컴퓨터를 사용하여 AzCopy를 실행할 수 있습니다. 또한 Azure Batch 일정을 사용하여 자동으로 수행할 수도 있지만 더 복잡합니다.  

Automation 스크립트는 스토리지 콘텐츠 조작 대신 Azure Resource Manager 배포를 위해 설계되었습니다. 자세한 내용은 [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

**동일한 지역 내의 동일한 스토리지 계정에 있는 별도의 두 파일 공유 간에 데이터를 복사하기 위해 요금이 있나요?**

아니요. 이 프로세스에는 요금이 부과되지 않습니다.

**Azure Portal에서 1~2TB의 데이터를 다운로드하려면 어떻게 해야 하나요?**

AzCopy를 사용하여 데이터를 다운로드합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**포털에서 다운로드 옵션을 사용하는 대신 로컬 컴퓨터에 VHD를 다운로드하려면 어떻게 해야 하나요?**

[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 VHD를 다운로드할 수 있습니다.

**Azure Storage 계정에서 Linux 기반 컴퓨터로 데이터를 다운로드하거나 Linux 컴퓨터에서 데이터를 업로드하려면 어떻게 할까요?**

Azure CLI를 사용할 수 있습니다.

- 단일 Blob을 다운로드합니다.

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- 단일 Blob을 업로드 합니다.

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**스토리지 계정 간에 Blob을 마이그레이션하는 방법**

 [Blob 마이그레이션 스크립트](../scripts/storage-common-transfer-between-storage-accounts.md)를 사용하여 이 작업을 수행할 수 있습니다.
 
## <a name="migration-or-backup"></a>마이그레이션 또는 백업

**한 스토리지 컨테이너에서 다른 스토리지 컨테이너로 데이터를 이동하려면 어떻게 할까요?**

다음 단계를 수행하세요.

1.  대상 Blob에 컨테이너(폴더)를 만듭니다.

2.  [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/)를 사용하여 원래 Blob 컨테이너의 내용을 다른 Blob 컨테이너로 복사합니다.

**Azure Storage의 Azure 파일 공유 간에 데이터를 이동하도록 PowerShell 스크립트를 만들려면 어떻게 할까요?**

AzCopy를 사용하여 Azure Storage의 Azure 파일 공유 간에 데이터를 이동합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**큰 .csv 파일을 Azure Storage에 업로드하려면 어떻게 할까요?**

AzCopy를 사용하여 큰 .csv 파일을 Azure Storage에 업로드합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**매일 D 드라이브에서 Azure storage 계정으로 로그를 이동 해야 합니다. 어떻게 할까요? 자동화**

AzCopy를 사용하고 작업 스케줄러에서 작업을 만들 수 있습니다. AzCopy 배치 스크립트를 사용하여 Azure Storage 계정에 파일을 업로드합니다. 자세한 내용은 [클라우드 서비스에 대한 시작 작업 구성 및 실행 방법](../../cloud-services/cloud-services-startup-tasks.md)을 참조하세요.

**구독 간에 내 스토리지 계정을 이동하려면 어떻게 할까요?**

AzCopy를 사용하여 구독 간에 스토리지 계정을 이동합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**다른 지역의 스토리지로 약 10TB의 데이터를 이동하려면 어떻게 해야 하나요?**

AzCopy를 사용하여 데이터를 이동합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**온-프레미스에서 Azure Storage로 데이터를 복사하려면 어떻게 해야 하나요?**

AzCopy를 사용하여 데이터를 복사합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**온-프레미스에서 Azure Files로 데이터를 이동하려면 어떻게 해야 하나요?**

AzCopy를 사용하여 데이터를 이동합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**관리 디스크를 다른 스토리지 계정으로 이동하려면 어떻게 할까요?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

다음 단계를 수행하세요.

1.  관리 디스크가 연결된 가상 머신을 중지합니다.

2.  다음 Azure PowerShell 스크립트를 실행하여 한 영역에서 다른 영역으로 관리 디스크 VHD를 복사합니다.

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  VHD를 복사한 다른 영역의 VHD 파일을 사용하여 관리 디스크를 만듭니다. 이렇게 하려면 다음 Azure PowerShell 스크립트를 실행합니다.  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ```

관리 디스크에서 가상 머신을 배포하는 방법에 대한 자세한 내용은 [CreateVmFromManagedOsDisk.ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1)을 참조하세요.

**스토리지 계정에서 데이터를 이동하거나 다운로드하려면 어떻게 할까요?**

AzCopy를 사용하여 데이터를 다운로드합니다. 자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md) 및 [Linux에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy-linux.md)을 참조하세요.

**Premium Storage 계정에서 표준 스토리지 계정으로 이동하려면 어떻게 할까요?**

다음 단계를 수행하세요.

1.  표준 스토리지 계정을 만듭니다. (또는 구독에서 기존 표준 스토리지 계정을 사용합니다.)

2.  AzCopy를 다운로드합니다. 다음 AzCopy 명령 중 하나를 실행합니다.

    스토리지 계정의 전체 디스크를 복사하려면 다음을 수행합니다.

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S

    하나의 디스크만 복사하려면 **패턴**에서 해당 디스크의 이름을 제공합니다

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

작업을 완료하는 데 몇 시간이 걸릴 수 있습니다.

전송이 성공적으로 완료되었는지 확인하려면 Azure Portal에서 대상 스토리지 계정 컨테이너를 검사합니다. 디스크를 표준 스토리지 계정으로 복사한 후 가상 머신에 기존 디스크로 연결할 수 있습니다. 자세한 내용은 [Azure Portal에서 Windows 가상 머신에 관리되는 데이터 디스크를 연결하는 방법](../../virtual-machines/windows/attach-managed-disk-portal.md)을 참조하세요.  

**클래식 스토리지 계정에서 Azure Resource Manager 스토리지 계정으로 이동하려면 어떻게 할까요?**

**Move-AzureStorageAccount** cmdlet을 사용할 수 있습니다. 이 cmdlet에는 여러 단계(유효성 검사, 준비, 커밋)가 있습니다. 만들기 전에 확인할 수 있습니다.

가상 머신이 있는 경우 스토리지 계정 데이터를 마이그레이션하기 전에 몇 가지 추가 단계를 수행해야 합니다. 자세한 내용은 [Azure PowerShell을 사용하여 클래식에서 Azure Resource Manager로 IaaS 리소스 마이그레이션](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)을 참조하세요.

**내 스토리지 계정 전체를 다른 스토리지 계정에 백업하려면 어떻게 할까요?**

스토리지 계정 전체를 직접 백업하는 옵션은 없습니다. 그러나 AzCopy 또는 Storage Explorer를 사용하여 수동으로 스토리지 계정의 컨테이너를 다른 계정으로 이동할 수 있습니다. 다음 단계에서는 AzCopy를 사용하여 컨테이너를 이동하는 방법을 보여 줍니다.  

1.  [AzCopy](storage-use-azcopy.md) 명령줄 도구를 설치합니다. 이 도구를 사용하면 스토리지 계정 간에 VHD 파일을 이동할 수 있습니다.

2.  설치 관리자를 사용하여 Windows에 AzCopy를 설치한 후에 명령 프롬프트 창을 연 다음 컴퓨터의 AzCopy 설치 폴더로 이동합니다. 기본적으로 AzCopy는 **%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy** 또는 **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**에 설치됩니다.

3.  다음 명령을 실행하여 컨테이너를 이동합니다. 텍스트를 실제 값으로 바꾸어야 합니다.   

            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: 원본 스토리지 계정에 대한 URI를 제공합니다(컨테이너까지).  
    - `/Dest`: 대상 스토리지 계정에 대한 URI를 제공합니다(컨테이너까지).  
    - `/SourceKey`: 원본 스토리지 계정에 대한 기본 키를 제공합니다. Azure Portal에서 스토리지 계정을 선택하여 이 키를 복사할 수 있습니다.  
    - `/DestKey`: 대상 스토리지 계정에 대한 기본 키를 제공합니다. 포털에서 스토리지 계정을 선택하여 이 키를 복사할 수 있습니다.

이 명령을 실행하면 컨테이너 파일이 대상 스토리지 계정으로 이동됩니다.

> [!NOTE]
> Azure Blob 간에 복사할 때 **패턴** 스위치가 포함된 AzCopy CLI는 작동하지 않습니다.
>
> AzCopy 명령을 직접 복사 및 편집하고, 교차 확인하여 **패턴**이 원본과 일치하는지 확인합니다. 또한 **/S** 와일드카드도 적용되었는지 확인합니다. 자세한 내용은 [AzCopy 매개 변수](storage-use-azcopy.md)를 참조하세요.

**Azure File Storage를 백업하려면 어떻게 할까요?**

백업 솔루션이 없습니다. 그러나 Azure Files는 비동기 복사도 지원합니다. 따라서 다음과 같이 파일을 복사할 수 있습니다.

- 공유에서 스토리지 계정 내의 다른 공유나 다른 스토리지 계정으로 복사

- 공유에서 스토리지 계정 내의 Blob 컨테이너나 다른 스토리지 계정으로 복사

자세한 내용은 [Windows에서 AzCopy를 사용하여 데이터 전송](storage-use-azcopy.md)을 참조하세요.
## <a name="configuration"></a>구성

**스토리지 계정에 대한 보조 지역을 유럽 지역으로 변경하려면 어떻게 할까요?**

스토리지 계정을 만들 때 계정에 대한 기본 지역을 선택합니다. 보조 지역은 주 지역을 기반으로 하여 선택하고 변경할 수 없습니다. 자세한 내용은 [GRS(지역 중복 스토리지): Azure Storage의 지역 간 복제](storage-redundancy.md)를 참조하세요.

**Azure SSE(스토리지 서비스 암호화)에 대한 자세한 정보는 어디서 얻을 수 있나요?**  

다음 문서를 참조하세요.

-  [Azure Storage 보안 가이드](../blobs/security-recommendations.md)

-  [휴지 상태의 데이터에 대한 Azure Storage 서비스 암호화](storage-service-encryption.md)

**스토리지 계정의 데이터를 암호화하려면 어떻게 할까요?**

스토리지 계정에서 암호화를 사용하도록 설정하면 기존 데이터가 암호화되지 않습니다. 기존 데이터를 암호화하려면 스토리지 계정에 해당 데이터를 다시 업로드해야 합니다.

AzCopy를 사용하여 다른 스토리지 계정으로 데이터를 복사한 다음 해당 데이터를 다시 가져옵니다. 또한 [미사용 데이터 암호화](storage-service-encryption.md)를 사용할 수도 있습니다.

**스토리지 계정의 복제를 지역 중복 스토리지에서 로컬 중복 스토리지로 변경하기 위한 필수 구성 요소가 있나요?**

아니요.

**파일 공유를 위해 Azure Premium Storage로 변환하려면 어떻게 할까요?**

Premium Storage는 Azure 파일 공유에서 허용되지 않습니다.

**Standard storage 계정에서 premium storage 계정으로 업그레이드할 어떻게 할까요? 있나요? Premium storage 계정에서 표준 저장소 계정으로 다운 그레이드를 어떻게 할까요? 하 시겠습니까?**

대상 스토리지 계정을 만들고, 원본 계정에서 대상 계정으로 데이터를 복사한 다음, 원본 계정을 삭제해야 합니다. AzCopy와 같은 도구를 사용하여 데이터를 복사할 수 있습니다.

가상 머신이 있는 경우 스토리지 계정 데이터를 마이그레이션하기 전에 몇 가지 추가 단계를 수행해야 합니다. 자세한 내용은 [Azure Premium Storage로 마이그레이션(비관리 디스크)](storage-migration-to-premium-storage.md)을 참조하세요.

**다른 사람에게 내 스토리지 리소스에 대한 액세스 권한을 부여하려면 어떻게 해야 하나요?**

다른 사람에게 스토리지 리소스에 대한 액세스 권한을 부여하려면 다음을 수행합니다.

-   SAS(공유 액세스 서명) 토큰을 사용하여 리소스에 대한 액세스를 제공합니다.

-   사용자에게 스토리지 계정의 기본 또는 보조 키를 제공합니다. 자세한 내용은 [스토리지 계정 액세스 키 관리](storage-account-keys-manage.md)를 참조하세요.

-   익명 액세스를 허용하도록 액세스 정책을 변경합니다. 자세한 내용은 [컨테이너 및 Blob에 익명의 사용자 권한 부여](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs)를 참조하세요.

**AzCopy는 어디에 설치되나요?**

-   Microsoft Azure Storage 명령줄에서 AzCopy에 액세스하는 경우 **AzCopy**를 입력합니다. 명령줄은 AzCopy와 함께 설치됩니다.

-   32비트 버전을 설치한 경우 **%ProgramFiles(x86)%\\Microsoft SDKs\\Azure\\AzCopy**에 있습니다.

-   64비트 버전을 설치한 경우 **%ProgramFiles%\\Microsoft SDKs\\Azure\\AzCopy**에 있습니다.

**내 저장소 계정으로 HTTPS 사용자 지정 도메인을 사용 어떻게 할까요?? 예를 들어 "https:\//mystorageaccountname.blob.core.windows.net/images/image.gif"를 "https:\//www.contoso.com/images/image.gif"로 표시 하려면 어떻게 해야 하나요?**

SSL은 현재 사용자 지정 도메인이 있는 스토리지 계정에서 지원되지 않습니다.
하지만 HTTPS가 아닌 사용자 지정 도메인은 사용할 수 있습니다. 자세한 내용은 [Blob Storage 엔드포인트에 대한 사용자 지정 도메인 이름 구성](../blobs/storage-custom-domain-name.md)을 참조하세요.

## <a name="access-to-storage"></a>저장소에 대 한 액세스

**가상 머신에서 컨테이너 폴더를 매핑하려면 어떻게 할까요?**

Azure 파일 공유를 사용합니다.

**Azure Files 중복 스토리지에 액세스하려면 어떻게 할까요?**

중복 스토리지에 액세스하려면 읽기 액세스 지역 중복 스토리지가 필요합니다. 그러나 Azure Files는 읽기 전용 액세스를 허용하지 않는 로컬 중복 스토리지 및 표준 지역 중복 스토리지만을 지원합니다.

**복제된 스토리지 계정(예: 영역 중복 스토리지, 지역 중복 스토리지 또는 읽기 권한 액세스 지역 중복 스토리지)의 경우 보조 지역에 저장된 데이터에 액세스하려면 어떻게 할까요?**

-   영역 중복 스토리지 또는 지역 중복 스토리지를 사용하는 경우 해당 지역으로 장애 조치를 시작하지 않으면 보조 지역의 데이터에 액세스할 수 없습니다. 장애 조치에 대한 자세한 내용은 [Azure Storage에서 재해 복구 및 스토리지 계정 장애 조치(failover)(미리 보기)](storage-disaster-recovery-guidance.md)를 참조하세요.

-   읽기 액세스 지역 중복 스토리지를 사용하는 경우 언제든지 보조 지역의 데이터에 액세스할 수 있습니다. 다음 방법 중 하나를 사용합니다.  

    - **AzCopy**: 보조 엔드포인트에 액세스하려면 URL의 스토리지 계정 이름에 **-secondary**를 추가합니다. 다음은 그 예입니다.  

      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS 토큰**: SAS 토큰을 사용하여 엔드포인트의 데이터에 액세스합니다. 자세한 내용은 [공유 액세스 서명 사용](storage-sas-overview.md)을 참조하세요.

**FTP를 사용하여 스토리지 계정에 있는 데이터에 액세스하려면 어떻게 할까요?**

FTP를 사용하여 스토리지 계정에 직접 액세스할 수 있는 방법은 없습니다. 그러나 Azure 가상 머신을 설정한 다음 가상 머신에 FTP 서버를 설치할 수 있습니다. FTP 서버는 Azure Files 공유 또는 가상 머신에서 사용할 수 있는 데이터 디스크에 파일을 저장할 수 있습니다.

Storage Explorer 또는 유사한 애플리케이션을 사용하지 않고 데이터만 다운로드하려는 경우 SAS 토큰을 사용할 수 있습니다. 자세한 내용은 [공유 액세스 서명 사용](storage-sas-overview.md)을 참조하세요.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
