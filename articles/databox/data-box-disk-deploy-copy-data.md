---
title: Azure Data Box Disk에 데이터를 복사하는 자습서 | Microsoft Docs
description: 이 자습서에는 호스트 컴퓨터에서 Azure Data Box Disk로 데이터를 복사한 다음, 체크섬을 생성하여 데이터 무결성을 확인하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: d33f53ef3d6ea0ef6a3040a82ec17b3089075949
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927116"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>자습서: Azure Data Box Disk에 데이터 복사 및 확인

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box-disk-and-validate"></a>Azure Data Box Disk에 데이터 복사 및 확인

디스크가 연결되고 잠금이 해제된 후에는 원본 데이터 서버에서 디스크로 데이터를 복사할 수 있습니다. 데이터 복사가 완료되면 데이터의 유효성을 검사하여 Azure에 성공적으로 업로드되는지 확인해야 합니다.

::: zone-end

::: zone target="docs"

이 자습서에는 호스트 컴퓨터에서 데이터를 복사한 다음, 데이터 무결성을 확인하는 체크섬을 생성하는 방법을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Data Box Disk에 데이터 복사
> * 데이터 확인

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 사항을 확인합니다.
- [자습서: Azure Data Box Disk 설치 및 구성](data-box-disk-deploy-set-up.md)을 완료했습니다.
- 디스크를 잠금 해제하고 클라이언트 컴퓨터에 연결합니다.
- 데이터를 디스크에 복사하는 데 사용되는 클라이언트 컴퓨터에서 [지원되는 운영 체제](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)를 실행해야 합니다.
- 데이터의 의도된 스토리지 유형이 [지원되는 스토리지 유형](data-box-disk-system-requirements.md#supported-storage-types-for-upload)과 일치하는지 확인합니다.
- [Azure 개체 크기 제한의 관리 디스크 제한](data-box-disk-limits.md#azure-object-size-limits)을 검토합니다.


## <a name="copy-data-to-disks"></a>디스크에 데이터 복사

데이터를 디스크에 복사하기 전에 다음 사항을 고려합니다.

- 적절한 데이터 형식에 해당하는 폴더에 데이터를 복사하는지 확인해야 합니다. 예를 들어 블록 Blob에 대한 폴더에 블록 Blob 데이터를 복사합니다. 데이터 형식이 적절한 폴더(스토리지 형식)와 일치하지 않는 경우 이후 단계에서 Azure에 대한 데이터 업로드가 실패합니다.
- 데이터를 복사하는 동안 데이터 크기가 [Azure Storage 및 Data Box Disk 제한](data-box-disk-limits.md)에 설명된 크기 제한을 준수하는지 확인합니다.
- Data Box Disk에 의해 업로드되는 데이터가 Data Box Disk 외부의 다른 애플리케이션에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.

   > [!IMPORTANT]
   >  순서를 만드는 동안 관리 디스크를 스토리지 대상 중 하나로 지정한 경우 다음 섹션이 적용됩니다.

- 사전 생성된 폴더 전체 및 Data Box Disk 전체에서 리소스 그룹에 지정된 이름의 관리 디스크 하나만 가질 수 있습니다. 즉, 사전 생성된 폴더에 업로드된 VHD는 이름이 고유해야 함을 의미합니다. 지정된 이름이 리소스 그룹의 기존 관리 디스크와 일치하지 않도록 해야 합니다. VHD 이름이 동일할 경우 단 하나의 VHD만 해당 이름의 관리 디스크로 변환됩니다. 다른 VHD는 준비 스토리지 계정에 페이지 Blob으로 업로드됩니다.
- 항상 사전 생성된 폴더 중 하나에 VHD를 복사합니다. 이러한 폴더 외부 또는 사용자가 만든 폴더 안에 VHD를 복사하는 경우 VHD는 Azure Storage 계정에 관리 디스크가 아닌 페이지 Blob으로 업로드됩니다.
- 고정된 VHD만 업로드하여 관리 디스크를 만들 수 있습니다. 동적 VHD, 차이점 보관용 VHD 또는 VHDX 파일은 지원되지 않습니다.


다음 단계를 수행하여 컴퓨터에서 Data Box Disk로 데이터를 연결하고 복사합니다.

1. 잠금 해제된 드라이브의 콘텐츠를 봅니다. 드라이브에서 사전 생성된 폴더 및 하위 폴더 목록은 Data Box Disk 주문 시 선택한 옵션에 따라 다릅니다.

    |선택한 스토리지 대상  |Storage 계정 유형|준비 스토리지 계정 유형 |폴더 및 하위 폴더  |
    |---------|---------|---------|------------------|
    |스토리지 계정     |GPv1 또는 GPv2                 | 해당 없음 | BlockBlob <br> PageBlob <br> AzureFile        |
    |스토리지 계정     |Blob Storage 계정         | 해당 없음 | BlockBlob        |
    |관리 디스크     |해당 없음 | GPv1 또는 GPv2         | ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>        |
    |스토리지 계정 <br> 관리 디스크     |GPv1 또는 GPv2 | GPv1 또는 GPv2         |BlockBlob <br> PageBlob <br> AzureFile <br> ManagedDisk<ul> <li> PremiumSSD </li><li>StandardSSD</li><li>StandardHDD</li></ul>         |
    |스토리지 계정 <br> 관리 디스크    |Blob Storage 계정 | GPv1 또는 GPv2         |BlockBlob <br> ManagedDisk<ul> <li>PremiumSSD</li><li>StandardSSD</li><li>StandardHDD</li></ul>         |

    GPv2 스토리지 계정이 지정된 주문의 예제 스크린샷은 아래와 같습니다.

    ![디스크 드라이브의 내용](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. *BlockBlob* 폴더에서 블록 Blob으로 가져와야 하는 데이터를 복사합니다. 마찬가지로 VHD/VHDX와 같은 데이터를 *PageBlob* 폴더, *AzureFile* 폴더로 복사합니다.

    BlockBlob 및 PageBlob 폴더 아래에 각 하위 폴더에 대한 Azure Storage 계정에 컨테이너가 만들어집니다. BlockBlob 및 PageBlob 폴더 아래의 모든 파일은 Azure Storage 계정 아래의 기본 컨테이너 `$root`로 복사됩니다. `$root` 컨테이너 있는 모든 파일은 항상 블록 Blob으로 업로드됩니다.

   *AzureFile* 폴더 내의 폴더로 파일을 복사합니다. *AzureFile* 폴더 내의 하위 폴더는 파일 공유를 만듭니다. *AzureFile* 폴더로 직접 복사된 모든 파일에 오류가 발생하고 블록 Blob으로 업로드됩니다.

    파일 및 폴더가 루트 디렉터리에 있는 경우 데이터 복사를 시작하기 전에 다른 폴더로 이동해야 합니다.

    > [!IMPORTANT]
    > 모든 컨테이너, Blob 및 파일 이름은 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions)을 준수해야 합니다. 이러한 규칙을 따르지 않는 경우 Azure로 데이터 업로드에 실패합니다.

3. 파일을 복사하는 경우 파일이 블록 Blob에 대해 4.7TiB 및 페이지 Blob에 대해 8TiB, Azure Files에 대해 1TiB를 초과하지 않도록 합니다. 
4. 파일 탐색기로 끌어서 놓기를 사용하여 데이터를 복사할 수 있습니다. Robocopy와 같은 SMB 호환 파일 복사 도구를 사용하여 데이터를 복사할 수도 있습니다. 여러 복사 작업은 다음 Robocopy 명령을 사용하여 시작될 수 있습니다.

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    명령에 대한 매개 변수 및 옵션은 다음과 같이 표로 정리됩니다.
    
    |매개 변수/옵션  |Description |
    |--------------------|------------|
    |원본            | 원본 디렉터리에 대한 경로를 지정합니다.        |
    |대상       | 대상 디렉터리에 대한 경로를 지정합니다.        |
    |/E                  | 빈 디렉터리를 포함하는 하위 디렉터리를 복사합니다. |
    |/MT[:N]             | N 스레드를 사용하여 다중 스레드 복사본을 만듭니다. 여기서 N은 1에서 128 사이의 정수입니다. <br>N의 기본값은 8입니다.        |
    |/R: \<N>             | 실패한 복사에 대한 재시도 횟수를 지정합니다. N의 기본값은 1,000,000입니다(백만 번의 다시 시도).        |
    |/W: \<N>             | 다시 시도 간의 대기 시간을 지정합니다(초). N의 기본값은 30입니다(대기 시간 30초).        |
    |/NFL                | 기록하지 않을 파일 이름을 지정합니다.        |
    |/NDL                | 기록하지 않을 디렉터리 이름을 지정합니다.        |
    |/FFT                | FAT 파일 시간을 가정합니다(2초 자릿수).        |
    |/Log:\<Log File>     | 로그 파일에 상태 출력을 작성합니다(기존 로그 파일을 덮어씀).         |

    각 디스크에서 실행 중인 여러 작업을 사용하여 병렬로 여러 디스크를 사용할 수 있습니다.

6. 작업이 진행되면 복사 상태를 확인합니다. 다음 샘플에서는 Data Box Disk에 파일을 복사할 robocopy 명령의 출력을 보여줍니다.

    ```
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
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
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    성능을 최적화하기 위해, 데이터를 복사할 때 다음 robocopy 매개 변수를 사용하세요.

    |    플랫폼    |    대부분 512KB 미만의 작은 파일                           |    대부분 512KB-1MB의 중간 파일                      |    대부분 1MB를 초과하는 큰 파일                             |   
    |----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
    |    Data Box Disk        |    Robocopy 세션 4개* <br> 세션당 16스레드    |    Robocopy 세션 2개* <br> 세션당 16스레드    |    Robocopy 세션 2개* <br> 세션당 16스레드    |
    
    **각 Robocopy 세션의 제한은 디렉터리 7,000개 및 파일 1억 5000만 개입니다.*
    
    >[!NOTE]
    > 위에서 권장하는 매개 변수는 내부 테스트에 사용된 환경을 기준으로 작성되었습니다.
    
    Robocopy 명령에 대한 자세한 내용은 [Robocopy 및 몇 가지 예제](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx)를 참조하세요.

6. 대상 폴더를 열어 복사된 파일을 보고 확인합니다. 복사 프로세스 중 오류가 있는 경우 문제 해결을 위해 로그 파일을 다운로드합니다. 로그 파일은 robocopy 명령에 지정된 위치에 있습니다.
 
### <a name="split-and-copy-data-to-disks"></a>데이터 분할 및 디스크에 복사

이 선택적인 절차는 여러 디스크를 사용 중이고 데이터를 분할하여 모든 디스크에 복사해야 하는 대용량 데이터 세트가 있는 경우에 사용할 수 있습니다. Data Box 분할 복사 도구는 Windows 컴퓨터에서 데이터를 분할하여 복사하는 데 도움이 됩니다.

>[!IMPORTANT]
> Data Box 분할 복사 도구 또한 데이터의 유효성을 검사합니다. Data Box 분할 복사 도구를 사용하여 데이터를 복사하는 경우 [유효성 검사 단계](#validate-data)를 건너뜁니다.
> 분할 복사 도구는 관리 디스크에는 지원되지 않습니다.

1. Windows 컴퓨터에서 Data Box 분할 복사 도구가 다운로드되어 로컬 폴더에 추출되어 있는지 확인합니다. 이 도구는 Windows용 Data Box Disk 도구 집합을 다운로드할 때 다운로드됩니다.
2. 파일 탐색기를 엽니다. 데이터 원본 드라이브 및 Data Box Disk에 할당된 드라이브 문자를 적어둡니다. 

     ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. 복사할 원본 데이터를 확인합니다. 예를 들어, 이 경우:
    - 다음 블록 Blob 데이터가 확인되었습니다.

         ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - 다음 페이지 Blob 데이터가 확인되었습니다.

         ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. 소프트웨어를 추출한 폴더로 이동합니다. 해당 폴더에서 `SampleConfig.json` 파일을 찾습니다. 이 파일은 수정하여 저장할 수 있는 읽기 전용 파일입니다.

   ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. `SampleConfig.json` 파일을 수정합니다.
 
   - 작업 이름을 제공합니다. Data Box Disk에 폴더가 만들어지고 이 디스크와 연결된 Azure Storage 계정의 컨테이너가 됩니다. 작업 이름은 Azure 컨테이너 명명 규칙을 따라야 합니다. 
   - `SampleConfigFile.json`에서 경로 형식을 기록하는 소스 경로를 제공합니다. 
   - 대상 디스크에 해당하는 드라이브 문자를 입력합니다. 소스 경로의 데이터를 가져다가 여러 디스크에 복사됩니다.
   - 로그 파일에 대한 경로를 제공합니다. 기본적으로 `.exe` 파일이 있는 현재 디렉터리로 전송됩니다.

     ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. 파일 형식의 유효성을 검사하려면 `JSONlint`로 이동합니다. 파일을 `ConfigFile.json`로 저장합니다. 

     ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. 명령 프롬프트 창을 엽니다. 

8. `DataBoxDiskSplitCopy.exe`를 실행합니다. Type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Enter 키를 눌러서 스크립트를 계속합니다.

    ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. 데이터 세트가 분할되고 복사되면 복사 세션의 분할 복사 도구에 대한 요약이 표시됩니다. 샘플 출력은 다음과 같습니다.

    ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. 데이터가 대상 디스크 전반에 분할되어 있는지 확인합니다. 
 
    ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    `n:` 드라이브의 내용을 자세히 살펴보면 블록 Blob 및 페이지 Blob 형식 데이터에 해당하는 두 개의 하위 폴더가 생성된 것이 보입니다.
    
     ![데이터 분할 복사](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. 복사 세션이 실패한 경우 복구하고 다시 시작하려면 다음 명령을 사용합니다.

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`

분할 복사 도구를 사용할 때 오류가 표시되면 [분할 복사 도구 오류 문제 해결](data-box-disk-troubleshoot-data-copy.md) 방법을 참조하세요.

데이터 복사가 완료되면 데이터 유효성을 검사할 수 있습니다. 분할 복사 도구를 사용한 경우 유효성 검사를 건너뛰고(분할 복사 도구도 유효성 검사를 수행하므로) 다음 자습서로 이동합니다.


## <a name="validate-data"></a>데이터의 유효성 검사

데이터를 복사할 때 분할 복사 도구를 사용하지 않은 경우 데이터의 유효성을 검사해야 합니다. 데이터의 유효성을 검사하려면 다음 단계를 수행합니다.

1. 드라이브의 *DataBoxDiskImport* 폴더에서 체크섬 유효성 검사를 위해 `DataBoxDiskValidation.cmd`를 실행합니다. Windows 환경에서만 사용할 수 있습니다. Linux 사용자는 디스크에 복사된 원본 데이터가 [필수 구성 요소](https://docs.microsoft.com/azure/databox/data-box-disk-limits)를 충족하는지 확인해야 합니다.
    
    ![Data Box Disk 유효성 검사 도구 출력](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. 적절한 옵션을 선택합니다. **항상 옵션 2를 선택하여 파일의 유효성을 검사하고 체크섬을 생성하는 것이 좋습니다**. 데이터 크기에 따라 이 단계는 시간이 걸릴 수 있습니다. 스크립트가 완료되면 명령 창을 종료합니다. 유효성 검사 및 체크섬 생성 중에 오류가 있으면 알림이 표시되고 오류 로그에 대한 링크도 제공됩니다.

    ![체크섬 출력](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - 두 실행 간에 도구를 다시 설정합니다.
    > - 작은 파일(KB 이하)이 포함된 대형 데이터 세트를 처리하는 경우 옵션 1을 사용합니다. 체크섬 생성에 시간이 오래 걸릴 수 있고 성능이 매우 느려질 수 있으므로 이 옵션은 파일의 유효성만 검사합니다.

3. 여러 디스크를 사용하는 경우 각 디스크에 대해 명령을 실행합니다.

유효성 검사 중 오류가 표시되면 [유효성 검사 오류 문제 해결](data-box-disk-troubleshoot.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Disk 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box Disk에 데이터 복사
> * 데이터 무결성 확인

Data Box Disk를 반환하고 Azure에 대한 데이터 업로드를 확인하는 방법을 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [Microsoft로 Azure Data Box 반송](./data-box-disk-deploy-picked-up.md)

::: zone-end

::: zone target="chromeless"

### <a name="copy-data-to-disks"></a>디스크에 데이터 복사

다음 단계를 수행하여 컴퓨터에서 Data Box Disk로 데이터를 연결하고 복사합니다.

1. 잠금 해제된 드라이브의 콘텐츠를 봅니다. 드라이브에서 사전 생성된 폴더 및 하위 폴더 목록은 Data Box Disk 주문 시 선택한 옵션에 따라 다릅니다.
2. 적절한 데이터 형식에 해당하는 폴더에 데이터를 복사해야 합니다. 예를 들어 비정형 데이터는 *BlockBlob* 폴더에 복사하고, VHD 또는 VHDX 데이터는 *PageBlob* 폴더에 복사하고, 파일은 *AzureFile*에 복사합니다. 데이터 형식이 적절한 폴더(스토리지 형식)와 일치하지 않는 경우 이후 단계에서 Azure에 대한 데이터 업로드가 실패합니다.

    - 모든 컨테이너, Blob 및 파일은 [Azure 명명 규칙](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions) 및 [Azure 개체 크기 제한](data-box-disk-limits.md#azure-object-size-limits)을 준수해야 합니다. 이러한 규칙 또는 제한을 따르지 않으면 Azure로 데이터를 업로드할 수 없습니다.     
    - 주문의 스토리지 대상 중 하나가 Managed Disks인 경우 [관리 디스크](data-box-disk-limits.md#managed-disk-naming-conventions)의 명명 규칙을 참조하세요.
    - BlockBlob 및 PageBlob 폴더 아래에 각 하위 폴더에 대한 Azure Storage 계정에 컨테이너가 만들어집니다. *BlockBlob* 및 *PageBlob* 폴더 아래의 모든 파일은 Azure Storage 계정 아래의 기본 컨테이너 $root로 복사됩니다. $root 컨테이너 있는 모든 파일은 항상 블록 Blob으로 업로드됩니다.
    - *AzureFile* 폴더 내에 하위 폴더를 만듭니다. 이 하위 폴더는 클라우드의 파일 공유에 매핑됩니다. 파일을 하위 폴더에 복사합니다. *AzureFile* 폴더로 직접 복사된 모든 파일에 오류가 발생하고 블록 Blob으로 업로드됩니다.
    - 파일 및 폴더가 루트 디렉터리에 있는 경우 데이터 복사를 시작하기 전에 다른 폴더로 이동해야 합니다.

3. 파일 탐색기 또는 Robocopy 같은 SMB 호환 파일 복사 도구에서 데이터를 끌어서 놓아 복사할 수 있습니다. 다음 명령을 사용하여 여러 복사 작업을 시작할 수 있습니다.

    ```
    Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt
    ```
4. 대상 폴더를 열어 복사된 파일을 보고 확인합니다. 복사 프로세스 중 오류가 있는 경우 문제 해결을 위해 로그 파일을 다운로드합니다. 로그 파일은 robocopy 명령에 지정된 위치에 있습니다.

이 [분할 밑 복사](data-box-disk-deploy-copy-data.md#split-and-copy-data-to-disks) 선택적 절차는 여러 디스크를 사용 중이고 큰 데이터 세트를 분할하여 모든 디스크에 복사해야 하는 경우에 사용할 수 있습니다.

### <a name="validate-data"></a>데이터의 유효성 검사

다음 단계를 수행하여 데이터를 확인합니다.

1. 드라이브의 *DataBoxDiskImport* 폴더에서 체크섬 유효성 검사를 위해 `DataBoxDiskValidation.cmd`를 실행합니다.
2. 옵션 2를 사용하여 파일의 유효성을 검사하고 체크섬을 생성합니다. 데이터 크기에 따라 이 단계는 시간이 걸릴 수 있습니다. 유효성 검사 및 체크섬 생성 중에 오류가 있으면 알림이 표시되고 오류 로그에 대한 링크도 제공됩니다.

    데이터 유효성 검사에 대한 자세한 내용은 [데이터 유효성 검사](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-copy-data#validate-data)를 참조하세요. 유효성 검사 중 오류가 표시되면 [유효성 검사 오류 문제 해결](data-box-disk-troubleshoot.md)을 참조하세요.

::: zone-end
