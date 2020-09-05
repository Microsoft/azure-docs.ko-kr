---
title: 자습서 - Azure Portal에서 Linux 가상 머신 백업
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Backup으로 Linux 가상 머신을 보호하는 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1865d307e7b79e7e40b0e98f2dcfa2535b0313fd
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551710"
---
# <a name="tutorial-back-up-and-restore-files-for-linux-virtual-machines-in-azure"></a>자습서: Azure의 Linux 가상 머신을 위한 파일 백업 및 복원

정기적으로 백업을 수행하여 데이터를 보호할 수 있습니다. Azure Backup은 지역 중복 복구 자격 증명 모음에 저장되는 복구 지점을 만듭니다. 복구 지점에서 복원하는 경우 전체 VM 또는 특정 파일을 복원할 수 있습니다. 이 문서에서는 nginx를 실행하는 Linux VM으로 단일 파일을 복원하는 방법에 대해 설명합니다. 사용할 VM이 아직 없는 경우 [Linux 빠른 시작](quick-create-cli.md)을 사용하여 만들 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * VM 백업 만들기
> * 매일 백업 예약
> * 백업에서 파일 복원

## <a name="backup-overview"></a>Backup 개요

Azure Backup 서비스에서 백업을 시작하면 백업 확장을 트리거하여 특정 시점 스냅샷을 수행합니다. Azure Backup 서비스는 Linux에서 _VMSnapshotLinux_ 확장을 사용합니다. VM을 실행하는 경우 확장은 첫 번째 VM 백업 중에 설치됩니다. VM이 실행되고 있지 않을 경우 Backup 서비스가 기본 스토리지의 스냅샷을 생성합니다(VM이 중지되었을 때는 애플리케이션 쓰기가 수행되지 않음).

기본적으로 Azure Backup에서는 Linux VM용으로 파일 시스템 일치 백업을 가져옵니다. 그러나 [사전 스크립트 및 사후 스크립트 프레임워크를 사용하여 애플리케이션 일치 백업](../../backup/backup-azure-linux-app-consistent.md)을 가져오도록 Azure Backup을 구성할 수 있습니다. Azure Backup 서비스가 스냅샷을 생성하면 데이터가 자격 증명 모음으로 전송됩니다. 효율성을 극대화하기 위해 이 서비스는 이전 백업 이후에 변경된 데이터 블록만 식별하여 전송합니다.

데이터 전송이 완료되면 스냅샷이 제거되고 복구 지점이 생성됩니다.


## <a name="create-a-backup"></a>백업 만들기
예약된 매일 백업을 Recovery Services 자격 증명 모음에 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **가상 머신**를 선택합니다. 
3. 목록에서 백업할 VM을 선택합니다.
4. VM 블레이드의 **작업** 섹션에서 **백업**을 클릭합니다. **백업 사용** 블레이드가 열립니다.
5. **Recovery Services 자격 증명 모음**에서 **새로 만들기**를 클릭하고 새 자격 증명 모음의 이름을 제공합니다. 새 자격 증명 모음이 가상 머신과 동일한 리소스 그룹과 위치에 만들어집니다.
6. **Backup 정책**을 클릭합니다. 이 예제에서는 기본값을 그대로 유지하고 **확인**을 클릭합니다.
7. **Backup 사용** 블레이드에서 **Backup 사용**을 클릭합니다. 이렇게 하면 기본 일정에 따라 매일 백업이 만들어집니다.
10. 초기 복구 지점을 만들려면 **Backup** 블레이드에서 **지금 Backup**을 클릭합니다.
11. **지금 Backup** 블레이드에서 달력 모양 아이콘을 클릭하고 달력 컨트롤을 사용하여 이 복구 지점을 유지할 마지막 날을 선택하고 **Backup**을 클릭합니다.
12. VM에 대한 **Backup** 블레이드에서 완료된 복구 지점의 수가 표시됩니다.

    ![복구 지점](./media/tutorial-backup-vms/backup-complete.png)

첫 번째 백업에는 약 20분 정도 걸립니다. 백업이 완료되면 이 자습서의 다음 부분으로 진행합니다.

## <a name="restore-a-file"></a>파일 복원

실수로 파일을 삭제하거나 변경한 경우 파일 복구를 사용하여 백업 자격 증명 모음에서 파일을 복구할 수 있습니다. 파일 복구는 VM에서 실행되는 스크립트를 사용하여 복구 지점을 로컬 드라이브로 탑재합니다. 이러한 드라이브는 12시간 동안 탑재된 상태로 유지되므로 복구 지점에서 파일을 복사하여 VM에 복원할 수 있습니다.  

이 예제에서는 기본 nginx 웹 페이지인 /var/www/html/index.nginx-debian.html을 복구하는 방법을 보여 줍니다. 이 예제에서 VM의 공용 IP 주소는 *13.69.75.209*입니다. 다음을 사용하여 VM의 IP 주소를 찾을 수 있습니다.

 ```azurecli
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. 로컬 컴퓨터에서 브라우저를 열고 VM의 공용 IP 주소를 입력하여 기본 nginx 웹 페이지를 확인합니다.

    ![기본 nginx 웹 페이지](./media/tutorial-backup-vms/nginx-working.png)

1. VM에 SSH를 수행합니다

    ```bash
    ssh 13.69.75.209
    ```

2. /var/www/html/index.nginx-debian.html을 삭제합니다.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. 로컬 컴퓨터에서 Ctrl+F5를 눌러 브라우저를 새로 고친 다음 기본 nginx 페이지가 사라졌는지 확인합니다.

    ![기본 nginx 웹 페이지](./media/tutorial-backup-vms/nginx-broken.png)
    
1. 로컬 컴퓨터에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
6. 왼쪽 메뉴에서 **가상 머신**를 선택합니다. 
7. 목록에서 VM을 선택합니다.
8. VM 블레이드의 **설정** 섹션에서 **Backup**을 클릭합니다. **Backup** 블레이드가 열립니다. 
9. 블레이드 위쪽의 메뉴에서 **파일 복구**를 선택합니다. **파일 복구** 블레이드가 열립니다.
10. **1단계: 복구 지점 선택**의 드롭다운에서 복구 지점을 선택합니다.
11. **2단계: 스크립트를 다운로드하여 파일 찾아보기 및 복구**에서 **실행 파일 다운로드** 단추를 클릭합니다. 다운로드한 파일을 로컬 컴퓨터에 저장합니다.
7. **스크립트 다운로드**를 클릭하여 스크립트 파일을 로컬로 다운로드합니다.
8. Bash 프롬프트를 열고 다음을 입력한 다음 *Linux_myVM_05-05-2017.sh*를 다운로드한 스크립트의 올바른 경로와 파일 이름으로, *azureuser*를 VM의 사용자 이름으로 그리고 *13.69.75.209*를 VM에 대한 공용 IP 주소로 바꿉니다.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. 로컬 컴퓨터에서 VM에 대한 SSH 연결을 엽니다.

    ```bash
    ssh 13.69.75.209
    ```
    
10. VM에서 스크립트 파일에 실행 권한을 추가합니다.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. VM에서 스크립트를 실행하여 복구 지점을 파일 시스템으로 탑재합니다.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. 스크립트의 출력에서 탑재 지점에 대한 경로를 제공합니다. 출력은 다음과 비슷합니다.

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. VM에서 기본 nginx 웹 페이지를 탑재 지점에서 파일을 삭제한 위치로 다시 복사합니다.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. 로컬 컴퓨터에서 기본 nginx 페이지를 보여 주는 VM의 IP 주소에 연결된 브라우저 탭을 엽니다. CTRL+F5를 눌러 브라우저 페이지를 새로 고칩니다. 이제 기본 페이지가 다시 작동하는지 확인해야 합니다.

    ![기본 nginx 웹 페이지](./media/tutorial-backup-vms/nginx-working.png)

18. 로컬 컴퓨터에서 Azure Portal용 브라우저 탭으로 돌아가고, **3단계: 복구 후 디스크 분리**에서 **디스크 분리** 단추를 클릭합니다. 이 단계를 수행하지 않으면 12시간 후에 탑재 지점에 대한 연결이 자동으로 끊깁니다. 12시간이 지나면 새 스크립트를 다운로드하여 새 탑재 지점을 만들어야 합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * VM 백업 만들기
> * 매일 백업 예약
> * 백업에서 파일 복원

가상 머신 모니터링에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [가성 머신 제어](tutorial-govern-resources.md)
