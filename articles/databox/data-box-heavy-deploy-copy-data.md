---
title: Azure Data Box Heavy에서 SMB를 통해 데이터를 복사하는 자습서 | Microsoft Docs
description: 이 자습서에서는 로컬 웹 UI와 함께 SMB를 사용하여 호스트 컴퓨터에서 Azure Data Box Heavy로 데이터를 연결하고 복사하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 6c88a1eaee50676cfe6bb9589779d7156b805e30
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920979"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>자습서: SMB를 통해 Azure Data Box Heavy에 데이터 복사

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Azure Data Box Heavy에 데이터 복사

::: zone-end

::: zone target = "docs"

이 자습서에서는 로컬 웹 UI를 사용하여 호스트 컴퓨터에서 연결하고 데이터를 복사하는 방법을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Data Box Heavy에 연결
> * Data Box Heavy에 데이터 복사

::: zone-end

::: zone target = "chromeless"

SMB, NFS, REST, 데이터 복사 서비스 또는 관리 디스크를 통해 원본 서버에서 Data Box로 데이터를 복사할 수 있습니다.

각 경우에서 공유 및 폴더 이름과 데이터 크기가 [Azure Storage 및 Data Box Heavy 서비스 제한](data-box-heavy-limits.md)에서 설명하는 지침을 따르는지 확인합니다.

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box Heavy 설정](data-box-deploy-set-up.md)을 완료했습니다.
2. Data Box Heavy를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. Data Box Heavy에 복사할 데이터가 포함된 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
    - [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
    - 고속 네트워크에 연결되어 있어야 합니다. 가장 빠른 복사 속도를 위해 40GbE 연결 2개(노드당 1개)를 병렬로 이용할 수 있습니다. 사용 가능한 40GbE 연결이 없는 경우 10GbE 연결이 2개(노드당 1개) 이상 있는 것이 좋습니다.
   

## <a name="connect-to-data-box-heavy-shares"></a>Data Box Heavy 공유에 연결

선택한 스토리지 계정에 따라 Data Box Heavy에서 만드는 최대 항목 수는 다음과 같습니다.
- GPv1 및 GPv2에서 연결된 각 스토리지에 대한 공유 3개.
- Premium Storage에 대한 공유 1개
- Blob 스토리지 계정에 대한 공유 1개

해당 공유는 디바이스의 두 노드에 모두 생성됩니다.

블록 Blob 및 페이지 Blob 공유 아래:
- 첫 번째 수준 엔터티는 컨테이너입니다.
- 두 번째 수준 엔터티는 Blob입니다.

Azure Files에 대한 공유 아래:
- 첫 번째 수준 엔터티는 공유입니다.
- 두 번째 수준 엔터티는 파일입니다.

다음 표는 데이터가 업로드되는 Data Box Heavy 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
| 스토리지           | UNC 경로                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure 블록 Blob | <li>공유 UNC 경로: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 파일       |<li>공유 UNC 경로: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Windows 또는 Linux 클라이언트를 사용하여 연결하는 단계는 서로 다릅니다.

> [!NOTE]
> 병렬 연결된 디바이스의 두 노드에 모두 연결할 때에는 같은 단계를 따릅니다.

### <a name="connect-on-a-windows-system"></a>Windows 시스템에서의 연결

Windows Server 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 Data Box Heavy에 연결합니다.

1. 첫 번째 단계는 세션을 인증하고 시작하는 것입니다. **연결 및 복사**로 이동합니다. **자격 증명 가져오기**를 클릭하여 스토리지 계정과 연결된 공유의 액세스 자격 증명을 가져옵니다.

    ![공유 자격 증명 가져오기 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. [액세스 공유 및 데이터 복사] 대화 상자에서 공유에 해당하는 **사용자 이름** 및 **암호**를 복사합니다. **확인**을 클릭합니다.
    
    ![공유 자격 증명 가져오기 1](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. 호스트 컴퓨터에서 스토리지 계정과 연결된 공유(다음 예제의 *databoxe2etest*)에 액세스하려면 명령 창을 엽니다. 명령 프롬프트에 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    데이터 형식에 따라 공유 경로는 다음과 같습니다.
    - Azure 블록 Blob - `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Azure 페이지 Blob - `\\10.100.10.100\databoxe2etest_PageBlob`
    - Azure 파일 - `\\10.100.10.100\databoxe2etest_AzFile`
    
4. 메시지가 표시되면 공유 암호를 입력합니다. 다음 샘플은 이전 명령을 통해 공유에 연결하는 방법을 보여줍니다.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Windows + R을 누르고 **실행** 창에서 `\\<device IP address>`를 지정합니다. **확인**을 클릭하여 파일 탐색기를 엽니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    이제 공유가 폴더로 표시될 것입니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.
    
### <a name="connect-on-a-linux-system"></a>Linux 시스템에서의 연결

Linux 클라이언트를 사용하는 경우 다음 명령을 사용하여 SMB 공유를 탑재합니다.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

`vers` 매개 변수는 Linux 호스트에서 지원하는 SMB의 버전입니다. 위의 명령에서 적절한 버전을 삽입합니다.

Data Box Heavy에서 지원하는 SMB 버전은 [Linux 클라이언트에 지원되는 파일 시스템](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients)을 참조하세요.

## <a name="copy-data-to-data-box-heavy"></a>Data Box Heavy에 데이터 복사

Data Box Heavy 공유에 연결된 후에는 데이터를 복사합니다.

### <a name="copy-considerations"></a>복사 고려 사항

데이터 복사를 시작하기 전에 다음 고려 사항을 검토합니다.

- 적절한 데이터 형식에 해당하는 공유에 데이터를 복사해야 합니다. 예를 들어 블록 Blob에 대한 공유에 블록 Blob 데이터를 복사합니다. 페이지 Blob에 VHD를 복사합니다.

    데이터 형식이 적절한 공유 형식과 일치하지 않는 경우 이후 단계에서 Azure에 데이터를 업로드하는 작업이 실패합니다.
-  데이터를 복사하는 동안 데이터 크기가 [Azure 스토리지 및 Data Box Heavy 제한](data-box-heavy-limits.md)에 설명된 크기 제한을 준수해야 합니다.
- Data Box Heavy에 의해 업로드되는 데이터가 Data Box Heavy 외부의 다른 애플리케이션에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.
- 다음이 권장됩니다.
    - SMB와 NFS를 동시에 사용하지 않습니다.
    - 동일한 데이터를 Azure에서 동일한 최종 대상에 복사합니다.
     
  이 경우 최종 결과를 확인할 수 없습니다.
- 복사하려는 파일의 폴더를 항상 공유 아래에 만든 다음, 해당 폴더에 파일을 복사하세요. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.

SMB 공유에 연결한 후에는 데이터 복사를 시작합니다.

1. Robocopy처럼 SMB 호환 파일 복사 도구를 사용하여 데이터를 복사할 수 있습니다. Robocopy 명령을 사용하여 여러 복사 작업을 시작할 수 있습니다. 다음 명령을 사용합니다.
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    다음 표에는 특성이 설명되어 있습니다.
    
    |attribute  |Description  |
    |---------|---------|
    |/e      |빈 디렉터리를 포함하는 하위 디렉터리를 복사합니다.         |
    |/r:     |실패한 복사에 대한 재시도 횟수를 지정합니다.         |
    |/w:     |다시 시도 간의 대기 시간을 지정합니다(초).         |
    |/is     |동일한 파일을 포함합니다.         |
    |/nfl    |파일 이름을 기록하지 않도록 지정합니다.         |
    |/ndl    |디렉터리 이름을 기록하지 않도록 지정합니다.        |
    |/np     |복사 작업의 진행률(지금까지 복사된 파일 또는 디렉터리의 수)이 표시되지 않도록 지정합니다. 진행률을 표시하면 성능이 대폭 저하됩니다.         |
    |/MT     | 다중 스레딩을 사용합니다. 32 또는 64 스레드를 권장합니다. 이 옵션은 암호화된 파일에 사용되지 않습니다. 암호화된 파일과 암호화되지 않은 파일을 구분해야 합니다. 그러나 단일 스레드 복사를 사용하면 성능이 대폭 저하됩니다.           |
    |/fft    | 파일 시스템의 타임스탬프 세분성을 줄이기 위해 사용합니다.        |
    |/b      | 백업 모드로 파일을 복사합니다.        |
    |/z      | 다시 시작 모드로 파일을 복사합니다. 환경이 불안정한 경우에 사용하세요. 이 옵션은 추가 로깅으로 인해 처리량이 감소합니다.      |
    | /zb    | 다시 시작 모드를 사용합니다. 액세스가 거부되는 경우 이 옵션은 백업 모드를 사용합니다. 이 옵션은 검사점 설정 때문에 처리량이 감소합니다.         |
    |/efsraw | EFS 원시 모드에서 암호화된 파일을 모두 복사합니다. 암호화된 파일에만 사용하세요.         |
    |log+:\<LogFile>| 기존 로그 파일에 출력을 추가합니다.|
    
 
    다음 샘플에서는 Data Box Heavy에 파일을 복사할 robocopy 명령의 출력을 보여줍니다.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                    Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
        Files :        17        17         0         0         0         0
        Bytes :     3.9 k     3.9 k         0         0         0         0          
    C:\Users>
    ```       

2. 성능을 최적화하기 위해, 데이터를 복사할 때 다음 robocopy 매개 변수를 사용하세요. (아래 숫자는 최상의 경우 시나리오를 나타냅니다.)

    | 플랫폼    | 대부분 512KB 미만의 작은 파일    | 대부분 512KB ~ 1MB의 중간 파일  | 대부분 1MB를 초과하는 큰 파일                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | Robocopy 세션 6개 <br> 세션당 24스레드 | Robocopy 세션 6개 <br> 세션당 16스레드 | Robocopy 세션 6개 <br> 세션당 16스레드 |


    Robocopy 명령에 대한 자세한 내용은 [Robocopy 및 몇 가지 예제](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)를 참조하세요.

3. 대상 폴더를 열어 복사된 파일을 보고 확인합니다.

    ![복사된 파일 보기](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. 데이터가 복사될 때:

    - 파일 이름, 크기 및 형식의 유효성을 검사하여 Azure 파일 및 컨테이너 이름 지정 규칙뿐만 아니라 Azure 개체 및 스토리지 제한도 만족하는지 확인합니다.
    - 데이터 무결성을 보장하기 위해, 체크섬이 인라인으로 계산됩니다.

    복사 프로세스 중 오류가 있는 경우 문제 해결을 위해 오류 파일을 다운로드하세요. 오류 파일을 다운로드하려면 화살표 아이콘을 선택합니다.

    ![오류 파일 다운로드](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    자세한 내용은 [Data Box Heavy로 데이터를 복사하는 동안 오류 로그 보기](data-box-logs.md#view-error-log-during-data-copy)를 참조하세요. 데이터를 복사하는 동안 발생하는 오류에 대한 자세한 목록을 보려면 [Data Box Heavy 문제 해결](data-box-troubleshoot.md)을 참조하세요.

5. 메모장에서 오류 파일을 엽니다. 다음 오류 파일은 데이터가 올바르게 정렬되지 않았음을 나타냅니다.

    ![오류 파일 열기](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    페이지 Blob의 경우 데이터는 512바이트 단위로 정렬되어야 합니다. 이 데이터가 제거된 후 다음 스크린샷과 같이 오류가 해결됩니다.

    ![오류가 해결됨](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. 복사가 완료된 후 **대시보드 보기** 페이지로 이동합니다. 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.
    
    ![대시보드에서 여유 공간 및 사용 중인 공간 확인](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

디바이스의 두 번째 노드에서 데이터를 복사하려면 위의 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Heavy 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box Heavy에 연결
> * Data Box Heavy에 데이터 복사


Data Box Heavy를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box Heavy 배송](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>SMB를 통해 데이터 복사

1. Windows 호스트를 사용하는 경우 다음 명령을 사용하여 SMB 공유에 연결합니다.

    `\\<IP address of your device>\ShareName`

2. 공유 액세스 자격 증명을 가져오려면 Data Box의 로컬 웹 UI에 있는 **연결 및 복사** 페이지로 이동합니다.

3. Robocopy와 같이 SMB 호환 파일 복사 도구를 사용하여 공유할 데이터를 복사합니다.

단계별 지침은 [자습서: SMB를 통해 Azure Data Box에 데이터 복사](data-box-heavy-deploy-copy-data.md)로 이동합니다.

### <a name="copy-data-via-nfs"></a>NFS를 통해 데이터 복사

1. NFS 호스트를 사용하는 경우 다음 명령을 사용하여 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. 공유 액세스 자격 증명을 가져오려면 Data Box Heavy의 로컬 웹 UI에서 **연결 및 복사 페이지**로 이동합니다.
3. `cp` 또는 `rsync` 명령을 사용하여 데이터를 복사합니다. 
4. 이러한 단계를 반복하여 Data Box Heavy의 두 번째 노드에 데이터를 연결하고 복사합니다.

단계별 지침은 [자습서: NFS를 통해 Azure Data Box에 데이터 복사](data-box-heavy-deploy-copy-data-via-nfs.md)로 이동합니다.

### <a name="copy-data-via-rest"></a>REST를 통한 데이터 복사

1. REST API를 통해 Data Box Blob Storage를 사용하여 데이터를 복사하기 위해 *http* 또는 *https*를 통해 연결할 수 있습니다.
2. Data Box Blob Storage에 데이터를 복사하려면 AzCopy를 사용하면 됩니다.
3. 이러한 단계를 반복하여 Data Box Heavy의 두 번째 노드에 데이터를 연결하고 복사합니다.

단계별 지침은 [자습서: REST API를 통해 Azure Data Box Blob Storage에 데이터 복사](data-box-heavy-deploy-copy-data-via-rest.md)로 이동합니다.

### <a name="copy-data-via-data-copy-service"></a>데이터 복사 서비스를 통해 데이터 복사

1. 데이터 복사 서비스를 사용하여 데이터를 복사하려면 작업을 만들어야 합니다. Data Box Heavy의 로컬 웹 UI에서 **관리 > 데이터 복사 > 만들기**로 이동합니다.
2. 매개 변수를 입력하고 작업을 만듭니다.
3. 이러한 단계를 반복하여 Data Box Heavy의 두 번째 노드에 데이터를 연결하고 복사합니다.

단계별 지침은 [자습서: 데이터 복사 서비스를 사용하여 Azure Data Box Heavy로 데이터 복사](data-box-heavy-deploy-copy-data-via-copy-service.md)로 이동합니다.

### <a name="copy-data-to-managed-disks"></a>관리 디스크로 데이터 복사

1. Data Box Heavy 디바이스를 주문할 때 스토리지 대상으로 관리 디스크를 선택해야 합니다.
2. SMB 또는 NFS 공유를 통해 Data Box Heavy에 연결할 수 있습니다.
3. 그런 다음, SMB 또는 NFS 도구를 통해 데이터를 복사할 수 있습니다.
4. 이러한 단계를 반복하여 Data Box Heavy의 두 번째 노드에 데이터를 연결하고 복사합니다.

단계별 지침은 [자습서: Data Box Heavy를 사용하여 Azure의 관리 디스크로 데이터 가져오기](data-box-heavy-deploy-copy-data-from-vhds.md)로 이동합니다.

::: zone-end


