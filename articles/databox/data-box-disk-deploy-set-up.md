---
title: Azure Data Box 디스크의 압축을 풀고, 연결하고, 잠금을 해제하기 위한 자습서 | Microsoft Docs
description: 이 자습서에서는 Windows 및 Linux 클라이언트에서 Azure Data Box Disk의 압축을 풀고, 디스크에 연결하고, 암호를 가져오고, 디스크의 잠금을 해제하는 방법에 대해 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 570653d620c5e314db9e6a0bc56d731f8acf9296
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87925654"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>자습서: Azure Data Box Disk 압축 풀기, 연결 및 잠금 해제

이 자습서는 Azure Data Box Disk의 압축을 풀고, 연결 및 잠금 해제하는 방법을 설명합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Data Box Disk 압축 풀기
> * 디스크에 연결 및 암호 가져오기
> * Windows 클라이언트에서 디스크 잠금 해제
> * Linux 클라이언트에서 디스크 잠금 해제

::: zone-end

::: zone target="chromeless"

## <a name="unpack-connect-and-unlock-azure-data-box-disk"></a>Azure Data Box Disk 압축 풀기, 연결 및 잠금 해제

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 다음 사항을 확인합니다.

1. [자습서: Azure Data Box Disk 주문](data-box-disk-deploy-ordered.md)을 참조하세요.
2. 디스크를 받고 포털에서 작업 상태가 **배달됨**으로 업데이트되었습니다.
3. Data Box Disk 잠금 해제 도구를 설치할 수 있는 클라이언트 컴퓨터가 있습니다. 클라이언트 컴퓨터는 다음 작업을 수행해야 합니다.
    - [지원되는 운영 체제](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)를 실행합니다.
    - Windows 클라이언트인 경우 다른 [필수 소프트웨어](data-box-disk-system-requirements.md#other-required-software-for-windows-clients)를 설치합니다.  

## <a name="unpack-your-disks"></a>디스크 압축 풀기

 다음 단계를 수행하여 디스크의 압축을 풉니다.

1. Data Box Disks는 작은 배송 상자에 넣어 발송됩니다. 상자를 열고 해당 콘텐츠를 제거합니다. 상자에 1~5개의 SSD(반도체 디스크) 및 디스크당 USB 연결 케이블이 있는지 확인합니다. 변조의 증거 또는 기타 명백한 손상에 대해 상자를 검사합니다.

    ![Data Box Disk 배송 패키지](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. 배송 상자가 훼손되었거나 심각하게 손상된 경우 상자를 열지 마십시오. 디스크가 올바른 작업 주문에 있는지 여부를 평가하는 데 도움을 얻고 대체물을 배송 받아야 하는지 Microsoft 지원에 문의합니다.
3. 상자에 반송 배송을 위한 포장용 레이블(현재 레이블 아래)을 포함하는 투명한 케이스가 있는지 확인합니다. 이 레이블이 손실되거나 손상된 경우 Azure Portal에서 항상 새 레이블을 다운로드하고 인쇄할 수 있습니다.

    ![Data Box Disk 포장용 레이블](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. 디스크의 반송 배송을 위해 상자와 패키징 폼을 보관합니다.

## <a name="connect-to-disks-and-get-the-passkey"></a>디스크에 연결 및 암호 가져오기

1. 포함된 케이블을 사용하여 필수 구성 요소에서 설명한 것처럼 지원되는 OS를 실행하는 클라이언트 컴퓨터에 디스크를 연결합니다.

    ![Data Box Disk 연결](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)

2. Azure Portal에서 Data Box Disk Order로 이동합니다. **일반 > 모든 리소스**로 이동하여 검색한 다음, Data Box Disk Order를 선택합니다. 복사 아이콘을 사용하여 암호를 복사합니다. 이 암호는 디스크의 잠금을 해제하는 데 사용됩니다.

    ![Data Box Disk 잠금 해제 암호](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png)

Windows 또는 Linux 클라이언트에 연결되는지에 따라 디스크의 잠금을 해제하는 단계가 다릅니다.

## <a name="unlock-disks-on-windows-client"></a>Windows 클라이언트에서 디스크 잠금 해제

다음 단계를 수행하여 디스크를 연결하고 잠금 해제합니다.

1. Azure Portal에서 Data Box Disk Order로 이동합니다. **일반 > 모든 리소스**로 이동하여 검색한 다음, Data Box Disk Order를 선택합니다.
2. Windows 클라이언트에 해당하는 Data Box Disk 도구 집합을 다운로드합니다. 이 도구 집합에는 Data Box 디스크 잠금 해제 도구, Data Box 디스크 유효성 검사 도구 및 Data Box 디스크 분할 복사 도구라는 3가지 도구가 포함됩니다.

    이 절차에서는 Data Box 디스크 잠금 해제 도구만 사용합니다. 다른 두 가지 도구는 나중에 사용됩니다.

    > [!div class="nextstepaction"]
    > [Windows용 Data Box Disk 도구 집합 다운로드](https://aka.ms/databoxdisktoolswin)

3. 데이터를 복사하는 데 사용할 동일한 컴퓨터에서 도구 집합을 추출합니다.
4. 명령 프롬프트 창을 열거나 동일한 컴퓨터에서 관리자 권한으로 Windows PowerShell을 실행합니다.
5. (선택 사항) 디스크를 잠금 해제하는 데 사용하는 컴퓨터가 운영 체제 요구 사항을 충족하는지 확인하려면 시스템 검사 명령을 실행합니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.

    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. `DataBoxDiskUnlock.exe`를 실행하고 [디스크에 연결 및 암호 가져오기](#connect-to-disks-and-get-the-passkey)에서 얻은 암호를 입력합니다. 디스크에 할당한 드라이브 문자가 표시됩니다. 샘플 출력은 다음과 같습니다.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1

    Following volumes are unlocked and verified.
    Volume drive letters: D:

    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. 나중에 디스크를 다시 삽입할 수 있도록 잠금 해제 단계를 반복합니다. Data Box Disk 잠금 해제 도구와 관련하여 도움이 필요하면 `help` 명령을 사용합니다.

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>

    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help

    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.

    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  

8. 디스크가 잠금 해제되면 디스크의 콘텐츠를 볼 수 있습니다.

    ![Data Box Disk 콘텐츠](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

디스크를 잠금 해제하는 동안 문제가 발생하면 [잠금 해제 문제 해결](data-box-disk-troubleshoot-unlock.md)을 참조하세요.

## <a name="unlock-disks-on-linux-client"></a>Linux 클라이언트에서 디스크 잠금 해제

1. Azure Portal에서 **일반 &gt; 디바이스 세부 정보**로 이동합니다.
2. Linux 클라이언트에 해당하는 Data Box Disk 도구 집합을 다운로드합니다.  

    > [!div class="nextstepaction"]
    > [Linux용 Data Box Disk 도구 집합 다운로드](https://aka.ms/databoxdisktoolslinux)

3. Linux 클라이언트에서 터미널을 엽니다. 소프트웨어를 다운로드한 폴더로 이동합니다. 이러한 파일을 실행할 수 있도록 파일 사용 권한을 변경합니다. 다음 명령을 입력합니다.

    `chmod +x DataBoxDiskUnlock_x86_64` 

    `chmod +x DataBoxDiskUnlock_Prep.sh` 

    샘플 출력은 다음과 같습니다. chmod 명령이 실행되면 `ls` 명령을 실행하여 파일 사용 권한이 변경되었는지 확인할 수 있습니다. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```

4. Data Box Disk 잠금 해제 소프트웨어에 필요한 이진 파일을 모두 설치하도록 스크립트를 실행합니다. `sudo`를 사용하여 명령을 루트로 실행합니다. 이진 파일이 성공적으로 설치되면 터미널에 영향을 주는 메모가 표시됩니다.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    스크립트는 먼저 클라이언트 컴퓨터가 지원되는 운영 체제를 실행하는지 여부를 확인합니다. 샘플 출력은 다음과 같습니다. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 

        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```

 
5. `y`를 입력하여 계속 설치합니다. 스크립트에서 설치하는 패키지는 다음과 같습니다. 
   - **epel-release** - 다음 세 가지 패키지를 포함하는 리포지토리입니다. 
   - **dislocker 및 fuse-dislocker** - 이러한 유틸리티는 BitLocker 암호화 디스크를 암호 해독하는 데 도움이 됩니다. 
   - **ntfs-3g** - NTFS 볼륨을 탑재하는 데 도움이 되는 패키지입니다. 
 
     패키지를 성공적으로 설치하면 터미널에는 해당 결과에 대한 알림이 표시됩니다.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Data Box Disk 잠금 해제 도구를 실행합니다. [디스크에 연결 및 암호 가져오기](#connect-to-disks-and-get-the-passkey)에서 얻은 Azure Portal의 암호를 입력합니다. 필요에 따라 잠금을 해제할 BitLocker 암호화 볼륨의 목록을 지정합니다. 암호 및 볼륨은 작은 따옴표 안에서 지정되어야 합니다. 

    다음 명령을 입력합니다.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:'<Your passkey from Azure portal>'          

    샘플 출력은 다음과 같습니다. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:'qwerqwerqwer'  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    데이터를 복사할 수 있는 볼륨의 탑재 지점이 표시됩니다.

7. 나중에 디스크를 다시 삽입할 수 있도록 잠금 해제 단계를 반복합니다. Data Box Disk 잠금 해제 도구와 관련하여 도움이 필요하면 `help` 명령을 사용합니다. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    샘플 출력은 다음과 같습니다. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:'<passkey from Azure_portal>' 
    
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' 
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' /Volumes:'/dev/sdbl' 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. 디스크가 잠금 해제되면 탑재 지점으로 이동하여 디스크의 콘텐츠를 볼 수 있습니다. 이제 데이터를 *BlockBlob* 또는 *PageBlob* 폴더로 복사할 준비가 되었습니다. 

    ![Data Box Disk 콘텐츠](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


디스크를 잠금 해제하는 동안 문제가 발생하면 [잠금 해제 문제 해결](data-box-disk-troubleshoot-unlock.md)을 참조하세요. 

::: zone-end

::: zone target="chromeless"

1. 디스크의 압축을 풀고 포함된 케이블을 사용하여 디스크를 클라이언트 컴퓨터에 연결합니다.
2. 데이터를 복사하는 데 사용할 동일한 컴퓨터에 Data Box Disk 도구 세트를 다운로드하여 추출합니다.

    > [!div class="nextstepaction"]
    > [Windows용 Data Box Disk 도구 집합 다운로드](https://aka.ms/databoxdisktoolswin)

    또는
    > [!div class="nextstepaction"]
    > [Linux용 Data Box Disk 도구 집합 다운로드](https://aka.ms/databoxdisktoolslinux) 

3. 디스크를 Windows 클라이언트에 잠금 해제하려면 명령 프롬프트 창을 열거나 동일한 컴퓨터에서 관리자 권한으로 Windows PowerShell을 실행합니다.

    - Data Box Disk 잠금 해제 도구가 설치된 폴더에서 다음 명령을 입력합니다.

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Azure Portal의 **일반 > 디바이스 세부 정보**에서 암호를 가져와 여기에 입력합니다. 디스크에 할당한 드라이브 문자가 표시됩니다. 
4. 디스크를 Linux 클라이언트에 잠금 해제하려면 터미널을 엽니다. 소프트웨어를 다운로드한 폴더로 이동합니다. 이러한 파일을 실행할 수 있도록 다음 명령을 입력하여 파일 사용 권한을 변경합니다. 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    스크립트를 실행하여 필요한 모든 이진 파일을 설치합니다.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Data Box Disk 잠금 해제 도구를 실행합니다. Azure Portal의 **일반 > 디바이스 세부 정보**에서 암호를 가져와 여기에 입력합니다. 필요에 따라 잠금을 해제할 BitLocker 암호화 볼륨 목록을 작은 따옴표 안에 지정합니다.

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:'<Your passkey from Azure portal>'
    ```      
5. 나중에 디스크를 다시 삽입할 수 있도록 잠금 해제 단계를 반복합니다. Data Box Disk 잠금 해제 도구와 관련하여 도움이 필요하면 도움말 명령을 사용합니다.

디스크가 잠금 해제되면 디스크의 콘텐츠를 볼 수 있습니다.

디스크 설정 및 잠금 해제하는 방법에 대한 자세한 내용은 [Data Box Disk 설정](data-box-disk-deploy-set-up.md)으로 이동하세요.

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box Disk 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
> * Data Box Disk 압축 풀기
> * 디스크에 연결 및 암호 가져오기
> * Windows 클라이언트에서 디스크 잠금 해제
> * Linux 클라이언트에서 디스크 잠금 해제


Data Box Disk에서 데이터를 복사하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Data Box Disk에서 데이터 복사](./data-box-disk-deploy-copy-data.md)

::: zone-end

