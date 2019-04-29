---
title: Azure Files를 배포하는 방법 | Microsoft Docs
description: Azure Files를 배포하는 방법을 처음부터 끝까지 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 05/22/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6afe54d269d273c6a93e6431e9f1c1af7b18cc0e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766422"
---
# <a name="how-to-deploy-azure-files"></a>Azure Files를 배포하는 방법
[Azure Files](storage-files-introduction.md)는 산업 표준 SMB 프로토콜을 통해 액세스할 수 있는, 클라우드에서 완전히 관리되는 파일 공유를 제공합니다. 이 문서에서는 조직 내에서 실제적으로 Azure Files를 배포하는 방법을 보여 줍니다.

이 문서의 단계를 수행하기 전에 [Azure Files 배포 계획](storage-files-planning.md)을 읽는 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건
이 문서에서는 사용자가 이미 다음 단계를 완료했다고 가정합니다.

- 원하는 지역에서 원하는 복원력 및 암호화 옵션을 사용하여 Azure Storage 계정을 만들었습니다. 저장소 계정을 만드는 방법에 대한 단계별 지침은 [저장소 계정 만들기](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.
- Storage 계정에서 원하는 할당량을 사용하여 Azure 파일 공유를 만들었습니다. 파일 공유를 만드는 방법에 대한 단계별 지침은 [파일 공유 만들기](storage-how-to-create-file-share.md)를 참조하세요.

## <a name="transfer-data-into-azure-files"></a>데이터를 Azure Files로 전송
온-프레미스에 저장된 파일 공유 같은 기존 파일 공유를 새 Azure 파일 공유로 마이그레이션하려고 할 수 있습니다. 이 섹션에서는 [계획 지침](storage-files-planning.md#data-transfer-method)에서 자세히 설명된 널리 사용되는 여러 메서드를 통해 데이터를 Azure 파일 공유로 이동하는 방법을 보여줍니다.

### <a name="azure-file-sync"></a>Azure 파일 동기화
Azure 파일 동기화를 사용하여 온-프레미스 파일 서버의 유연성, 성능 및 호환성을 희생하지 않고 Azure Files에서 조직의 파일 공유를 중앙 집중화할 수 있습니다. 이 작업은 Windows Server를 Azure 파일 공유의 빠른 캐시로 변환하여 수행합니다. Windows Server에서 사용할 수 있는 아무 프로토콜이나 사용하여 데이터를 로컬로(SMB, NFS 및 FTPS 포함) 액세스할 수 있으며 세계 전역에 걸쳐 필요한 만큼 캐시를 보유할 수 있습니다.

장기 사용의 경우 동기화 메커니즘이 필요하지 않지만 Azure 파일 동기화를 사용하여 데이터를 Azure 파일 공유로 마이그레이션할 수 있습니다. Azure 파일 동기화를 사용하여 데이터를 Azure 파일 공유로 전송하는 방법에 대한 자세한 내용은 [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md) 및 [Azure 파일 동기화를 배포하는 방법](storage-sync-files-deployment-guide.md)을 참조하세요.

### <a name="azure-importexport"></a>Azure Import/Export
Azure Import/Export 서비스를 사용하면 하드 디스크 드라이브를 Azure 데이터 센터에 발송하여 많은 양의 데이터를 안전하게 Azure 파일 공유로 전송할 수 있습니다. 서비스에 대한 자세한 내용은 [Microsoft Azure Import/Export 서비스를 사용하여 Azure Storage로 데이터 전송](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

> [!Note]  
> Azure Import/Export 서비스는 현재 Azure 파일 공유에서 파일을 내보내는 작업을 지원하지 않습니다.

다음 단계에서는 온-프레미스 위치에서 Azure 파일 공유로 데이터를 가져옵니다.

1. Azure에 우편으로 보낼 하드 디스크를 필요한 수만큼 확보합니다. 하드 디스크는 크기에 제한이 없지만 SATA II 또는 SATA III 표준을 지원하는 2.5" 또는 3.5" SSD/HDD여야 합니다. 

2. 데이터 전송을 수행하는 서버/PC에서 각 디스크를 연결하고 탑재합니다. 성능을 최적화하려면 데이터가 포함된 서버에서 로컬로 온-프레미스 내보내기 작업을 실행하는 것이 좋습니다. 데이터를 제공하는 파일 서버가 NAS 디바이스인 경우와 같이 일부 경우에는 이 기능을 사용하지 못할 수 있습니다. 이 경우 PC에서 각 디스크를 탑재하면 됩니다.

3. 각 드라이브가 온라인 상태이고, 초기화되고, 드라이브 문자가 할당되어 있는지 확인하세요. 드라이브를 온라인으로 전환하고, 초기화하고, 드라이브 문자를 할당하려면 디스크 관리 MMC 스냅인(diskmgmt.msc)을 엽니다.

    - 디스크를 온라인으로 전환하려면(아직 온라인 상태가 아닌 경우) 디스크 관리 MMC의 아래쪽 창에서 디스크를 마우스 오른쪽 단추로 클릭하고 “온라인”을 선택합니다.
    - 디스크를 초기화하려면 디스크가 온라인으로 전환된 후 아래쪽 창에서 디스크를 마우스 오른쪽 단추로 클릭하고 “초기화”를 선택합니다. 메시지가 표시되면 “GPT”를 선택해야 합니다.

        ![디스크 관리 MMC의 디스크 초기화 메뉴 스크린샷](media/storage-files-deployment-guide/transferdata-importexport-1.PNG)

    - 디스크에 드라이브 문자를 할당하려면 온라인 및 초기화된 디스크의 “할당되지 않은” 영역을 마우스 오른쪽 단추로 클릭하고 “새 단순 볼륨”을 클릭합니다. 이렇게 하면 드라이브 문자를 할당할 수 있습니다. 나중에 지정되므로 볼륨 형식을 지정할 필요가 없습니다.

        ![디스크 관리 MMC의 새 단순 볼륨 마법사 스크린샷](media/storage-files-deployment-guide/transferdata-importexport-2.png)

4. 데이터 세트 CSV 파일을 만듭니다. 데이터 세트 CSV 파일은 온-프레미스 데이터 경로와 데이터를 복사해야 하는 원하는 Azure 파일 공유 간 매핑입니다. 예를 들어 다음 데이터 세트 CSV 파일은 온-프레미스 파일 공유("F:\shares\scratch")를 Azure 파일 공유("MyAzureFileShare")에 매핑합니다.
    
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\shares\scratch\","MyAzureFileShare/",file,rename,"None",None
    ```

    한 저장소 계정으로 여러 공유를 지정할 수 있습니다. 자세한 내용은 [데이터 세트 CSV 파일 준비](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-the-dataset-csv-file)를 참조하세요.

5. 드라이브 집합 CSV 파일을 만듭니다. 드라이브 집합 CSV 파일에는 온-프레미스 내보내기 에이전트에 사용할 수 있는 디스크가 나열됩니다. 예를 들어 다음 드라이브 집합 CSV 파일에는 온-프레미스 내보내기 작업에 사용되는 `X:`, `Y:` 및 `Z:` 드라이브가 나열됩니다.

    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    X,Format,SilentMode,Encrypt,
    Y,Format,SilentMode,Encrypt,
    Z,Format,SilentMode,Encrypt,
    ```
    
    자세한 내용은 [드라이브 집합 CSV 파일 준비](../common/storage-import-export-tool-preparing-hard-drives-import.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#prepare-initialdriveset-or-additionaldriveset-csv-file)를 참조하세요.

6. [WAImportExport 도구](https://www.microsoft.com/download/details.aspx?id=55280)를 사용하여 하나 이상의 하드 드라이브에 데이터를 복사합니다.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
    ```

    > [!Warning]  
    > 디스크 준비를 완료한 후 하드 디스크 드라이브 또는 저널 파일에 있는 데이터를 수정하지 마세요.

7. [가져오기 작업 만들기](../common/storage-import-export-data-to-files.md#step-2-create-an-import-job).
    
### <a name="robocopy"></a>Robocopy
Robocopy는 Windows 및 Windows Server와 함께 제공되는 잘 알려진 복사 도구입니다. 파일 공유를 로컬로 탑재하고 탑재된 위치를 Robocopy 명령의 대상으로 사용하는 방식으로 Robocopy를 사용하여 데이터를 Azure Files로 전송할 수 있습니다. Robocopy 사용은 매우 간단합니다.

1. [Azure 파일 공유 탑재](storage-how-to-use-files-windows.md). 성능을 최적화하려면 데이터가 포함된 서버에서 로컬로 Azure 파일 공유를 탑재하는 것이 좋습니다. 데이터를 제공하는 파일 서버가 NAS 디바이스인 경우와 같이 일부 경우에는 이 기능을 사용하지 못할 수 있습니다. 이 경우 PC에서 Azure 파일 공유를 탑재하면 됩니다. 이 예제에서 `net use`는 명령줄에서 파일 공유를 탑재하는 데 사용됩니다.

    ```
    net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
    ```

2. 명령줄에서 `robocopy`를 사용하여 데이터를 Azure 파일 공유로 이동합니다.

    ```
    robocopy <path-to-local-share> <path-to-azure-file-share> /E /Z /MT:32
    ```
    
    Robocopy에는 복사 동작을 원하는 대로 수정할 수 있는 다양한 옵션이 있습니다. 자세한 내용은 [Robocopy](https://technet.microsoft.com/library/cc733145.aspx) 매뉴얼 페이지를 참조하세요.

### <a name="azcopy"></a>AzCopy
AzCopy는 간단한 명령과 최적의 성능으로 데이터를 Azure Files 및 Azure Blob Storage에서 복사하도록 디자인된 명령줄 유틸리티입니다. AzCopy 사용은 간단합니다.

1. [Linux](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#download-and-install-azcopy) 또는 [Windows에서 최신 AzCopy 버전](https://aka.ms/downloadazcopy)을 다운로드합니다.
2. 명령줄에서 `azcopy`를 사용하여 데이터를 Azure 파일 공유로 이동합니다. Windows의 구문은 다음과 같습니다. 

    ```
    azcopy /Source:<path-to-local-share> /Dest:https://<storage-account>.file.core.windows.net/<file-share>/ /DestKey:<storage-account-key> /S
    ```

    Linux의 명령 구문은 약간 다릅니다.

    ```
    azcopy --source <path-to-local-share> --destination https://<storage-account>.file.core.windows.net/<file-share>/ --dest-key <storage-account-key> --recursive
    ```

    AzCopy에는 복사 동작을 원하는 대로 수정할 수 있는 다양한 옵션이 있습니다. 자세한 내용은 [Windows의 AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) 및 [Linux의 AzCopy](../common/storage-use-azcopy-linux.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)를 참조하세요.

## <a name="automatically-mount-on-needed-pcsservers"></a>필요한 PC/서버에 자동 탑재
온-프레미스 파일 공유를 바꾸려면 공유가 사용될 컴퓨터에 공유를 미리 탑재하는 것이 좋습니다. 이 작업은 컴퓨터 목록에서 자동으로 수행될 수 있습니다.

> [!Note]  
> Azure 파일 공유를 탑재하려면 저장소 계정 키를 암호로 사용해야 하므로 신뢰할 수 있는 환경의 탑재만 권장합니다. 

### <a name="windows"></a>Windows
PowerShell을 사용하여 여러 PC에서 탑재 명령을 실행할 수 있습니다. 다음 예제에서 `$computers`는 수동으로 채워지지만 자동으로 탑재할 컴퓨터 목록을 생성할 수 있습니다. 예를 들어 이 변수를 Active Directory의 결과로 채울 수 있습니다.

```powershell
$computer = "MyComputer1", "MyComputer2", "MyComputer3", "MyComputer4"
$computer | ForEach-Object { Invoke-Command -ComputerName $_ -ScriptBlock { net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name> /PERSISTENT:YES } }
```

### <a name="linux"></a>Linux
다음 예제에서 SSH와 결합된 간단한 bash 스크립트로 동일한 결과를 생성할 수 있습니다. `$computer` 변수도 사용자가 입력해야 합니다.

```
computer = ("MyComputer1" "MyComputer2" "MyComputer3" "MyComputer4")
for item in "${computer[@]}"
do
    ssh $item "sudo bash -c 'echo \"//<storage-account-name>.file.core.windows.net/<share-name> /mymountpoint cifs vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino\" >> /etc/fstab'", "sudo mount -a"
done
```

## <a name="next-steps"></a>다음 단계
- [Azure 파일 동기화 배포 계획](storage-sync-files-planning.md)
- [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)
- [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
