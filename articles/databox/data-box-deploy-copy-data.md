---
title: Azure Data Box에서 SMB를 통해 데이터를 복사하는 자습서 | Microsoft Docs
description: 이 자습서에서는 로컬 웹 UI와 함께 SMB를 사용하여 호스트 컴퓨터에서 Azure Data Box로 데이터를 연결하고 복사하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: a0622c7556896b7ae7201ffa3a7ecac8de1106a4
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053544"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>자습서: SMB를 통해 Azure Data Box에 데이터 복사

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Azure Data Box에 데이터 복사

::: zone-end

::: zone target="docs"

이 자습서에서는 로컬 웹 UI를 사용하여 호스트 컴퓨터에서 연결하고 데이터를 복사하는 방법을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 필수 구성 요소
> * Data Box에 연결
> * Data Box에 데이터 복사

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box 설정](data-box-deploy-set-up.md)을 완료했습니다.
2. Data Box를 받았고 포털의 주문 상태가 **배달됨**입니다.
3. Data Box에 복사할 데이터가 포함된 호스트 컴퓨터가 있습니다. 호스트 컴퓨터는 다음 사항이 필수입니다.
   * [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
   * 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용해도 되지만, 이 경우 복사 속도가 떨어집니다.

## <a name="connect-to-data-box"></a>Data Box에 연결

선택한 스토리지 계정에 따라 Data Box에서 만드는 항목은 다음과 같습니다.

* GPv1 및 GPv2에서 연결된 각 스토리지에 대한 공유 3개.
* Premium Storage에 대한 공유 1개
* Blob 스토리지 계정에 대한 공유 1개

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files 공유에서 첫 번째 수준 엔터티는 공유, 두 번째 수준 엔터티는 파일입니다.

다음 표에서는 데이터가 업로드되는 Data Box 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
|Azure Storage 형식  | Data Box 공유            |
|-------------------|--------------------------------------------------------------------------------|
| Azure 블록 Blob | <li>공유 UNC 경로: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 파일       |<li>공유 UNC 경로: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Windows Server 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 Data Box에 연결합니다.

1. 첫 번째 단계는 세션을 인증하고 시작하는 것입니다. **연결 및 복사**로 이동합니다. **SMB**를 선택하여 스토리지 계정과 연결된 공유의 액세스 자격 증명을 가져옵니다. 

    ![공유 자격 증명 가져오기 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. [액세스 공유 및 데이터 복사] 대화 상자에서 공유에 해당하는 **사용자 이름** 및 **암호**를 복사합니다. **확인**을 선택합니다.
    
    ![공유 자격 증명 가져오기 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. 호스트 컴퓨터에서 스토리지 계정과 연결된 공유(다음 예제의 *utsac1*)에 액세스하려면 명령 창을 엽니다. 명령 프롬프트에 다음을 입력합니다.

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    데이터 형식에 따라 공유 경로는 다음과 같습니다.
    - Azure 블록 Blob - `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Azure 페이지 Blob - `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Azure 파일 - `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. 메시지가 표시되면 공유 암호를 입력합니다. 다음 샘플은 이전 명령을 통해 공유에 연결하는 방법을 보여줍니다.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. Windows + R을 누르고 **실행** 창에서 `\\<device IP address>`를 지정합니다. **확인**을 선택하여 파일 탐색기를 엽니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    이제 공유가 폴더로 표시될 것입니다.
    
    ![파일 탐색기를 통해 공유에 연결 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.
    
Linux 클라이언트를 사용하는 경우 다음 명령을 사용하여 SMB 공유를 탑재합니다. 아래에서 "vers" 매개 변수는 Linux 호스트에서 지원하는 SMB 버전입니다. 아래 명령에서 적절한 버전을 삽입합니다. Data Box에서 지원하는 SMB 버전은 [Linux 클라이언트에 지원되는 파일 시스템](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients)을 참조하세요. 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Data Box에 데이터 복사

Data Box 공유에 연결된 후에는 데이터를 복사합니다. 데이터 복사를 시작하기 전에 다음 고려 사항을 검토합니다.

* 적절한 데이터 형식에 해당하는 공유에 데이터를 복사해야 합니다. 예를 들어 블록 Blob에 대한 공유에 블록 Blob 데이터를 복사합니다. 페이지 Blob에 VHD를 복사합니다. 데이터 형식이 적절한 공유 형식과 일치하지 않는 경우 이후 단계에서 Azure에 데이터를 업로드하는 작업이 실패합니다.
* 데이터를 복사하는 동안 데이터 크기가 [Azure 스토리지 및 Data Box 제한](data-box-limits.md)에 설명된 크기 제한을 준수해야 합니다.
* Data Box에 의해 업로드되는 데이터가 Data Box 외부의 다른 애플리케이션에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.
* 다음이 권장됩니다.
  * SMB와 NFS를 동시에 사용하지 않습니다.
  * 동일한 데이터를 Azure에서 동일한 최종 대상에 복사합니다.

  이 경우 최종 결과를 확인할 수 없습니다.
* 복사하려는 파일의 폴더를 항상 공유 아래에 만든 다음, 해당 폴더에 파일을 복사하세요. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.

> [!IMPORTANT]
> Data Box에서 Azure Storage로 데이터를 전송했음을 확인할 수 있을 때까지 원본 데이터의 복사본을 유지하세요.

SMB 공유에 연결한 후 데이터 복사를 시작합니다. Robocopy처럼 SMB 호환 파일 복사 도구를 사용하여 데이터를 복사할 수 있습니다. Robocopy 명령을 사용하여 여러 복사 작업을 시작할 수 있습니다. 다음 명령을 사용합니다.

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

다음 표에는 특성이 설명되어 있습니다.
    
|attribute  |Description  |
|---------|---------|
|/e     |빈 디렉터리를 포함하는 하위 디렉터리를 복사합니다.         |
|/r:     |실패한 복사에 대한 재시도 횟수를 지정합니다.         |
|/w:     |다시 시도 간의 대기 시간을 지정합니다(초).         |
|/is     |동일한 파일을 포함합니다.         |
|/nfl     |파일 이름을 기록하지 않도록 지정합니다.         |
|/ndl    |디렉터리 이름을 기록하지 않도록 지정합니다.        |
|/np     |복사 작업의 진행률(지금까지 복사된 파일 또는 디렉터리의 수)이 표시되지 않도록 지정합니다. 진행률을 표시하면 성능이 대폭 저하됩니다.         |
|/MT     | 다중 스레딩을 사용합니다. 32 또는 64 스레드를 권장합니다. 이 옵션은 암호화된 파일에 사용되지 않습니다. 암호화된 파일과 암호화되지 않은 파일을 구분해야 합니다. 그러나 단일 스레드 복사를 사용하면 성능이 대폭 저하됩니다.           |
|/fft     | 파일 시스템의 타임스탬프 세분성을 줄이기 위해 사용합니다.        |
|/b     | 백업 모드로 파일을 복사합니다.        |
|/z    | 다시 시작 모드로 파일을 복사합니다. 환경이 불안정한 경우에 사용하세요. 이 옵션은 추가 로깅으로 인해 처리량이 감소합니다.      |
| /zb     | 다시 시작 모드를 사용합니다. 액세스가 거부되는 경우 이 옵션은 백업 모드를 사용합니다. 이 옵션은 검사점 설정 때문에 처리량이 감소합니다.         |
|/efsraw     | EFS 원시 모드에서 암호화된 파일을 모두 복사합니다. 암호화된 파일에만 사용하세요.         |
|log+:\<LogFile>| 기존 로그 파일에 출력을 추가합니다.|    
 
다음 샘플에서는 Data Box에 파일을 복사하는 robocopy 명령의 출력을 보여줍니다.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

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

성능을 최적화하기 위해, 데이터를 복사할 때 다음 robocopy 매개 변수를 사용하세요.

|    플랫폼    |    대부분 512KB 미만의 작은 파일                           |    대부분 512KB-1MB의 중간 파일                      |    대부분 1MB를 초과하는 큰 파일                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    Robocopy 세션 2개 <br> 세션당 16스레드    |    Robocopy 세션 3개 <br> 세션당 16스레드    |    Robocopy 세션 2개 <br> 세션당 24스레드    |

Robocopy 명령에 대한 자세한 내용은 [Robocopy 및 몇 가지 예제](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)를 참조하세요.

복사 프로세스 중에 오류가 발생하면 알림이 표시됩니다.

![연결 및 복사 오류 다운로드 및 보기](media/data-box-deploy-copy-data/view-errors-1.png)

**문제 목록 다운로드**를 선택합니다.

![연결 및 복사 오류 다운로드 및 보기](media/data-box-deploy-copy-data/view-errors-2.png)

목록을 열어 오류의 세부 정보를 보고 해결 URL을 선택하여 권장 해결 방법을 확인합니다.

![연결 및 복사 오류 다운로드 및 보기](media/data-box-deploy-copy-data/view-errors-3.png)

자세한 내용은 [Data Box로 데이터를 복사하는 동안 오류 로그 보기](data-box-logs.md#view-error-log-during-data-copy)를 참조하세요. 데이터를 복사하는 동안 발생하는 오류에 대한 자세한 목록을 보려면 [Data Box 문제 해결](data-box-troubleshoot.md)을 참조하세요.

데이터 무결성을 보장하기 위해, 데이터가 복사될 때 체크섬이 인라인으로 계산됩니다. 복사가 완료되면 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.

![대시보드에서 여유 공간 및 사용 중인 공간 확인](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

SMB, NFS, REST, 데이터 복사 서비스 또는 관리 디스크를 통해 원본 서버에서 Data Box로 데이터를 복사할 수 있습니다.

각 경우에서 공유 및 폴더 이름과 데이터가 [Azure Storage 및 Data Box 서비스 제한](data-box-limits.md)에서 설명하는 지침을 따르는지 확인합니다.

## <a name="copy-data-via-smb"></a>SMB를 통해 데이터 복사

SMB를 통해 데이터를 복사하려면 다음을 수행합니다.

1. Windows 호스트를 사용하는 경우 다음 명령을 사용하여 SMB 공유에 연결합니다.

    `\\<IP address of your device>\ShareName`

2. 공유 액세스 자격 증명을 가져오려면 Data Box의 로컬 웹 UI에 있는 **연결 및 복사** 페이지로 이동합니다.
3. Robocopy와 같이 SMB 호환 파일 복사 도구를 사용하여 공유할 데이터를 복사합니다. 

단계별 지침은 [자습서: SMB를 통해 Azure Data Box에 데이터 복사](data-box-deploy-copy-data.md)로 이동합니다.

## <a name="copy-data-via-nfs"></a>NFS를 통해 데이터 복사

NFS를 통해 데이터를 복사하려면 다음을 수행합니다.

1. NFS 호스트를 사용하는 경우 다음 명령을 사용하여 Data Box에 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. 공유 액세스 자격 증명을 가져오려면 Data Box의 로컬 웹 UI에 있는 **연결 및 복사** 페이지로 이동합니다.
3. `cp` 또는 `rsync` 명령을 사용하여 데이터를 복사합니다.

단계별 지침은 [자습서: NFS를 통해 Azure Data Box에 데이터 복사](data-box-deploy-copy-data-via-nfs.md)로 이동합니다.

## <a name="copy-data-via-rest"></a>REST를 통한 데이터 복사

REST를 통해 데이터를 복사하려면 다음을 수행합니다.

1. REST API를 통해 Data Box Blob Storage를 사용하여 데이터를 복사하기 위해 *http* 또는 *https*를 통해 연결할 수 있습니다.
2. Data Box Blob Storage에 데이터를 복사하려면 AzCopy를 사용하면 됩니다.

단계별 지침은 [자습서: REST API를 통해 Azure Data Box Blob Storage에 데이터 복사](data-box-deploy-copy-data-via-nfs.md)로 이동합니다.

## <a name="copy-data-via-data-copy-service"></a>데이터 복사 서비스를 통해 데이터 복사

데이터 복사 서비스를 통해 데이터를 복사하려면 다음을 수행합니다.

1. 데이터 복사 서비스를 사용하여 데이터를 복사하려면 작업을 만들어야 합니다. Data Box의 로컬 웹 UI에서 **관리 > 데이터 복사 > 만들기**로 이동합니다.
2. 매개 변수를 입력하고 작업을 만듭니다.

단계별 지침은 [자습서: 데이터 복사 서비스를 사용하여 Azure Data Box로 데이터 복사](data-box-deploy-copy-data-via-copy-service.md)로 이동합니다.

## <a name="copy-data-to-managed-disks"></a>관리 디스크로 데이터 복사

데이터 관리 디스크를 복사하려면 다음을 수행합니다.

1. Data Box 디바이스를 주문할 때 스토리지 대상으로 관리 디스크를 선택해야 합니다.
2. SMB 또는 NFS 공유를 통해 Data Box에 연결할 수 있습니다.
3. 그런 다음, SMB 또는 NFS 도구를 통해 데이터를 복사할 수 있습니다.

단계별 지침은 [자습서: Data Box를 사용하여 Azure의 관리 디스크로 데이터 가져오기](data-box-deploy-copy-data-from-vhds.md)로 이동합니다.

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * 필수 구성 요소
> * Data Box에 연결
> * Data Box에 데이터 복사

Data Box를 Microsoft로 다시 배송하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 배송](./data-box-deploy-picked-up.md)

::: zone-end
