---
title: NFS를 통해 Azure Data Box에 데이터를 복사하는 자습서 | Microsoft Docs
description: 이 자습서에서는 로컬 웹 UI와 함께 NFS를 사용하여 호스트 컴퓨터에서 Azure Data Box로 데이터를 연결하고 복사하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: alkohli
ms.openlocfilehash: 590a0a1ce474d48e95163081dcdcacb52233badf
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926079"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>자습서: NFS를 통해 Azure Data Box에 데이터 복사

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
    - [지원되는 운영 체제](data-box-system-requirements.md)를 실행합니다.
    - 고속 네트워크에 연결되어 있어야 합니다. 10GbE 연결이 하나 이상 있는 것이 좋습니다. 10GbE 연결을 사용할 수 없으면 1GbE 데이터 링크를 사용할 수 있지만, 이 경우 복사 속도가 떨어집니다. 

## <a name="connect-to-data-box"></a>Data Box에 연결

선택한 스토리지 계정에 따라 Data Box에서 만드는 항목은 다음과 같습니다.
- GPv1 및 GPv2에서 연결된 각 스토리지에 대한 공유 3개.
- Premium Storage에 대한 공유 1개 
- Blob 스토리지 계정에 대한 공유 1개 

블록 Blob 및 페이지 Blob 공유에서는 첫 번째 수준 엔터티가 컨테이너, 두 번째 수준 엔터티가 Blob입니다. Azure Files 공유에서 첫 번째 수준 엔터티는 공유, 두 번째 수준 엔터티는 파일입니다.

다음 표에서는 데이터가 업로드되는 Data Box 및 Azure Storage 경로 URL의 공유에 대한 UNC 경로를 보여줍니다. 최종 Azure Storage 경로 URL은 UNC 공유 경로에서 파생될 수 있습니다.
 
| Azure Storage 유형| Data Box 공유                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure 블록 Blob | <li>공유 UNC 경로: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure 페이지 Blob  | <li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure 파일       |<li>공유 UNC 경로: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Linux 호스트 컴퓨터를 사용하는 경우 다음 단계에 따라 NFS 클라이언트에 대한 액세스를 허용하도록 Data Box를 구성합니다.

1. 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다. 로컬 웹 UI에서 **연결 및 복사** 페이지로 이동합니다. **NFS 설정** 아래에서 **NFS 클라이언트 액세스**를 클릭합니다. 

    ![NFS 클라이언트 액세스 구성 1](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. NFS 클라이언트의 IP 주소를 입력하고 **추가**를 클릭합니다. 이 단계를 반복하여 여러 NFS 클라이언트에 대한 액세스를 구성할 수 있습니다. **확인**을 클릭합니다.

    ![NFS 클라이언트 액세스 구성 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Linux 호스트 컴퓨터에 [지원되는 버전](data-box-system-requirements.md)의 NFS 클라이언트를 설치합니다. 해당 Linux 배포판에 맞는 버전을 사용하세요. 

3. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Data Box 디바이스에 NFS 공유를 탑재합니다.

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    다음 예제에서는 NFS를 통해 Data Box 공유에 연결하는 방법을 보여줍니다. Data Box 디바이스 IP는 `10.161.23.130`이고, `Mystoracct_Blob` 공유는 ubuntuVM에 탑재되며, 탑재 지점은 `/home/databoxubuntuhost/databox`입니다.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Mac 클라이언트에 대해 다음과 같은 추가 옵션을 추가해야 합니다. 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.

## <a name="copy-data-to-data-box"></a>Data Box에 데이터 복사

Data Box 공유에 연결된 후에는 데이터를 복사합니다. 데이터 복사를 시작하기 전에 다음 고려 사항을 검토합니다.

* 적절한 데이터 형식에 해당하는 공유에 데이터를 복사해야 합니다. 예를 들어 블록 Blob에 대한 공유에 블록 Blob 데이터를 복사합니다. 페이지 Blob에 VHD를 복사합니다. 데이터 형식이 적절한 공유 형식과 일치하지 않는 경우 이후 단계에서 Azure에 데이터를 업로드하는 작업이 실패합니다.
*  데이터를 복사하는 동안 데이터 크기가 [Azure Storage 및 Data Box 제한](data-box-limits.md)에 설명된 크기 제한을 준수해야 합니다. 
* Data Box에 의해 업로드되는 데이터가 Data Box 외부의 다른 애플리케이션에 의해 동시에 업로드되는 경우 업로드 작업이 실패하고 데이터 손상이 발생할 수 있습니다.
* SMB 및 NFS를 동시에 사용하거나 Azure의 동일한 최종 대상에 동일한 데이터를 복사하지 않는 것이 좋습니다. 이 경우 최종 결과를 확인할 수 없습니다.
* **복사하려는 파일에 대한 폴더는 항상 공유 아래에 만든 다음 이 폴더에 파일을 복사합니다**. 블록 Blob 및 페이지 Blob 공유 아래에 만들어진 폴더는 데이터가 Blob으로 업로드되는 컨테이너를 나타냅니다. 스토리지 계정의 *root* 폴더에 파일을 직접 복사할 수 없습니다.
* NFS 공유에서 Data Box의 NFS까지 대/소문자 구분 디렉터리 및 파일 이름을 수집하는 경우:
  * 이름의 대/소문자가 유지됩니다.
  * 파일은 대/소문자를 구분하지 않습니다.

    예를 들어 `SampleFile.txt` 및 `Samplefile.Txt`를 복사할 경우 Data Box에 복사되는 이름의 대/소문자는 유지되지만 두 번째 파일은 동일한 파일로 인식되어 첫 번째 파일을 덮어씁니다.

> [!IMPORTANT]
> Data Box에서 Azure Storage로 데이터를 전송했음을 확인할 수 있을 때까지 원본 데이터의 복사본을 유지하세요.

Linux 호스트 컴퓨터를 사용하는 경우 Robocopy와 비슷한 복사 유틸리티를 사용합니다. Linux에서 사용할 수 있는 대안 중 일부는 [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) 또는 [Ultracopier](https://ultracopier.first-world.info/)입니다.  

`cp` 명령은 디렉터리를 복사하는 최고의 옵션 중 하나입니다. 사용 방법에 대한 자세한 내용은 [cp 기본 페이지](http://man7.org/linux/man-pages/man1/cp.1.html)를 참조하세요.

다중 스레드 복사에 rsync 옵션을 사용하는 경우 다음 지침을 따르세요.

* Linux 클라이언트에서 사용하는 파일 시스템에 따라 **CIFS 유틸리티** 또는 **NFS 유틸리티** 패키지를 설치합니다.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* **Rsync** 및 **Parallel**을 설치합니다(Linux 배포판 버전에 따라 다름).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* 탑재 지점을 만듭니다.

    `sudo mkdir /mnt/databox`

* 볼륨을 탑재합니다.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* 폴더 디렉터리 구조를 미러링합니다.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* 파일을 복사합니다.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     여기서 j는 병렬 처리의 수를 지정하고, X는 병렬 복사본의 수

     병렬 복사본 16개로 시작하여 가용 리소스에 따라 스레드 수를 늘리는 것이 좋습니다.

> [!IMPORTANT]
> Linux 파일 형식인 기호 링크, 문자 파일, 블록 파일, 소켓 및 파이프는 지원되지 않습니다. 이러한 파일 형식을 사용하면 **배송 준비** 단계 동안 오류가 발생합니다.

복사 프로세스 중에 오류가 발생하면 알림이 표시됩니다.

![연결 및 복사 오류 다운로드 및 보기](media/data-box-deploy-copy-data/view-errors-1.png)

**문제 목록 다운로드**를 선택합니다.

![연결 및 복사 오류 다운로드 및 보기](media/data-box-deploy-copy-data/view-errors-2.png)

목록을 열어 오류의 세부 정보를 보고 해결 URL을 선택하여 권장 해결 방법을 확인합니다.

![연결 및 복사 오류 다운로드 및 보기](media/data-box-deploy-copy-data/view-errors-3.png)

자세한 내용은 [Data Box로 데이터를 복사하는 동안 오류 로그 보기](data-box-logs.md#view-error-log-during-data-copy)를 참조하세요. 데이터를 복사하는 동안 발생하는 오류에 대한 자세한 목록을 보려면 [Data Box 문제 해결](data-box-troubleshoot.md)을 참조하세요.

데이터 무결성을 보장하기 위해, 데이터가 복사될 때 체크섬이 인라인으로 계산됩니다. 복사가 완료되면 디바이스에서 사용 중인 공간과 여유 공간을 확인합니다.

   ![대시보드에서 여유 공간 및 사용 중인 공간 확인](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

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
