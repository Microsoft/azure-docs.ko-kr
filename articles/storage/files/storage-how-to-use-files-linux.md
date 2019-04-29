---
title: Linux에서 Azure Files 사용 | Microsoft Docs
description: Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 75987c7838846aacb099b725e2a222967b32fe64
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763823"
---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용

[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [SMB 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

> [!NOTE]  
> 온-프레미스 또는 다른 Azure 지역과 같이 호스트되는 Azure 지역 외부에 Azure 파일 공유를 탑재하려면 OS에서 SMB 3.0 암호화 기능을 지원해야 합니다.

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Linux 및 cifs-utils 패키지와 함께 Azure 파일 공유를 탑재하기 위한 필수 조건
<a id="smb-client-reqs"></a>

* **기존 Azure storage 계정 및 파일 공유**: 이 문서를 완료 하기 위해 저장소 계정 및 파일 공유 하도록 해야 합니다. 아직 이미 만든 경우, 해당 주제 빠른 시작 중 하나를 참조: [파일 공유-CLI 만들기](storage-how-to-use-files-cli.md)합니다.

* **저장소 계정 이름과 키** 이 문서를 완료 하기 위해 저장소 계정 이름과 키 해야 합니다. 이미 있어야 하는 CLI 빠른 시작을 사용 하 여 하나를 만든 경우 저장소 계정 키를 검색 하는 방법을 학습 하기 위해서는 이전에 연결 된 CLI 빠른 시작이 고, 그렇지 참조 합니다.

* **탑재 요구 사항에 따라 Linux 배포판을 선택합니다.**  
      SMB 2.1 및 SMB 3.0을 통해 Azure Files를 탑재할 수 있습니다. 클라이언트 온-프레미스 또는 다른 Azure 지역의 연결에서는 SMB 3.0을 사용해야 합니다. Azure Files는 SMB 2.1(또는 암호화되지 않은 SMB 3.0)을 거부합니다. 동일한 Azure 지역 내의 VM에서 Azure 파일 공유에 액세스하는 경우 Azure 파일 공유를 호스트하는 스토리지 계정에 대해 ‘보안 전송 필요’가 사용하지 않도록 설정된 경우에만 SMB 2.1을 사용하여 파일 공유에 액세스할 수 있습니다. 항상 보안 전송을 요구하고 암호화된 SMB 3.0만 사용하는 것이 좋습니다.

    SMB 3.0 암호화 지원은 Linux 커널 버전 4.11에서 도입되었으며 널리 사용되는 Linux 배포판의 이전 커널 버전에 백포트되었습니다. 이 문서를 게시하는 시점에 Azure 갤러리에서 다음 배포판이 이 테이블 헤더에서 지정된 탑재 옵션을 지원합니다. 

* **해당 탑재 기능으로 최소 권장되는 버전(SMB 버전 2.1 및 SMB 버전 3.0)**    

    |   | SMB 2.1 <br>(동일한 Azure 지역 내에서 VM에 탑재) | SMB 3.0 <br>(온-프레미스 및 지역 간 탑재) |
    | --- | :---: | :---: |
    | Ubuntu Server | 14.04+ | 16.04+ |
    | RHEL | 7+ | 7.5+ |
    | CentOS | 7+ |  7.5+ |
    | Debian | 8+ |   |
    | openSUSE | 13.2+ | 42.3+ |
    | SUSE Linux Enterprise Server | 12 | 12 SP3+ |

    Linux 배포판이 여기 나열되지 않은 경우 다음 명령을 사용하여 Linux 커널 버전을 확인할 수 있습니다.

   ```bash
   uname -r
   ```

* <a id="install-cifs-utils"></a>**cifs-utils 패키지가 설치됩니다.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt-get` 패키지 관리자를 사용합니다.

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    **RHEL** 및 **CentOS**에서는 `yum` 패키지 관리자를 사용합니다.

    ```bash
    sudo yum install cifs-utils
    ```

    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.

    ```bash
    sudo zypper install cifs-utils
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **탑재된 공유의 디렉터리/파일 권한 결정**: 아래 예제에서는 모든 사용자에게 읽기, 쓰기 및 실행 권한을 부여하기 위해 `0777` 권한을 사용합니다. 다른 [chmod 권한](https://en.wikipedia.org/wiki/Chmod)으로 바꿀 수 있습니다.

* **445 포트가 열려 있는지 확인**: SMB는 445 TCP 포트를 통해 통신합니다. 클라이언트 컴퓨터에서 방화벽이 445 TCP 포트를 차단하고 있지 않은지 확인합니다.

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재

1. **[Linux 배포판에 cifs-utils 패키지를 설치합니다](#install-cifs-utils)**.

1. **탑재 지점에 대한 폴더를 만듭니다**. 탑재 지점의 폴더는 파일 시스템의 어디에나 만들 수 있지만, 일반적으로 `/mnt` 폴더 아래에 만듭니다. 예를 들면 다음과 같습니다.

    ```bash
    mkdir /mnt/MyAzureFileShare
    ```

1. **탑재 명령을 사용하여 Azure 파일 공유를 탑재합니다**. `<storage-account-name>`, `<share-name>`, `<smb-version>`, `<storage-account-key>` 및 `<mount-point>`를 사용자 환경에 적합한 정보로 바꾸어야 합니다. Linux 배포판이 암호화된 SMB 3.0을 지원하는 경우(자세한 내용은 [SMB 클라이언트 요구 사항 이해](#smb-client-reqs) 참조) `<smb-version>`에 대해 `3.0`을 사용합니다. Linux 배포판이 암호화된 SMB 3.0을 지원하지 않는 경우 `<smb-version>`에 대해 `2.1`을 사용합니다. Azure 파일 공유를 Azure 지역 외부에서 탑재 될 수 있습니다 (포함 하 여 온-프레미스 또는 다른 Azure 지역) SMB 3.0을 사용 합니다. 

    ```bash
    sudo mount -t cifs //<storage-account-name>.file.core.windows.net/<share-name> <mount-point> -o vers=<smb-version>,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount <mount-point>`를 사용하여 공유를 탑재 해제할 수 있습니다.

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기

1. **[Linux 배포판에 cifs-utils 패키지를 설치합니다](#install-cifs-utils)**.

1. **탑재 지점에 대한 폴더를 만듭니다**. 탑재 지점의 폴더는 파일 시스템의 어디에나 만들 수 있지만, 일반적으로 `/mnt` 폴더 아래에 만듭니다. 이 폴더를 어디에 만들든 폴더의 절대 경로를 기록해 둡니다. 예를 들어 다음 명령은 `/mnt` 아래에 새 폴더를 만듭니다(경로는 절대 경로).

    ```bash
    sudo mkdir /mnt/MyAzureFileShare
    ```

1. **파일 공유를 위한 사용자 이름(저장소 계정 이름) 및 암호(저장소 계정 키)를 저장할 자격 증명 파일을 만듭니다.** `<storage-account-name>` 및 `<storage-account-key>`를 사용자 환경에 대한 적절한 정보로 바꾸어야 합니다. 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    if [ ! -f "/etc/smbcredentials/<storage-account-name>.cred" ]; then
        sudo bash -c 'echo "username=<storage-account-name>" >> /etc/smbcredentials/<storage-account-name>.cred'
        sudo bash -c 'echo "password=<storage-account-key>" >> /etc/smbcredentials/<storage-account-name>.cred'
    fi
    ```

1. **하나의 루트만 암호 파일을 읽거나 수정할 수 있도록 자격 증명 파일의 권한을 변경합니다.** 스토리지 계정 키는 기본적으로 스토리지 계정에 대한 상위 관리자 암호이므로, 루트만 액세스할 수 있는 파일에서 사용 권한을 설정하는 것은 더 낮은 권한 사용자가 스토리지 계정 키를 검색할 수 없도록 하는 데 중요합니다.   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage-account-name>.cred
    ```

1. **다음 명령을 사용하여 `/etc/fstab`에 다음 줄을 추가합니다**. `<storage-account-name>`, `<share-name>`, `<smb-version>` 및 `<mount-point>`를 사용자 환경에 적합한 정보로 바꾸어야 합니다. Linux 배포판이 암호화된 SMB 3.0을 지원하는 경우(자세한 내용은 [SMB 클라이언트 요구 사항 이해](#smb-client-reqs) 참조) `<smb-version>`에 대해 `3.0`을 사용합니다. Linux 배포판이 암호화된 SMB 3.0을 지원하지 않는 경우 `<smb-version>`에 대해 `2.1`을 사용합니다. Azure 파일 공유를 Azure 지역 외부에서 탑재 될 수 있습니다 (포함 하 여 온-프레미스 또는 다른 Azure 지역) SMB 3.0을 사용 합니다. 

    ```bash
    sudo bash -c 'echo "//<storage-account-name>.file.core.windows.net/<share-name> <mount-point> cifs nofail,vers=<smb-version>,credentials=/etc/smbcredentials/<storage-account-name>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'
    ```

> [!Note]  
> 다시 부팅하는 대신 `/etc/fstab`을 편집한 후에 `sudo mount -a`를 사용하여 Azure 파일 공유를 탑재할 수 있습니다.

## <a name="feedback"></a>사용자 의견

Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure Files는 Linux에서 File Storage를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure Files Linux 사용자](mailto:azurefileslinuxusers@microsoft.com)에게 메일을 보내세요.

## <a name="next-steps"></a>다음 단계

Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
