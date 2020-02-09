---
title: 가상 컴퓨터에 대한 Azure NetApp Files 볼륨 탑재
description: Azure에서 Windows 가상 머신 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 분리 하는 방법에 대해 알아봅니다.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551542"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제 

필요에 따라 Windows 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제할 수 있습니다.  Linux 가상 머신에 대한 탑재 지침은 Azure NetApp Files에서 사용할 수 있습니다.  

1. **볼륨** 블레이드를 클릭 하 고 탑재 하려는 볼륨을 선택 합니다. 
2. 선택한 볼륨에서 **명령 탑재** 를 클릭 한 다음 지침에 따라 볼륨을 탑재 합니다. 

    ![NFS 탑재 명령](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![탑재 명령 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    NFSv 4.1을 사용 하는 경우 다음 명령을 사용 하 여 파일 시스템을 탑재 합니다. `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Azure VM이 시작 되거나 다시 부팅 될 때 NFS 볼륨이 자동으로 탑재 되도록 하려면 호스트의 `/etc/fstab` 파일에 항목을 추가 합니다. 

    예: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`은 볼륨 속성 블레이드에서 찾은 Azure NetApp Files 볼륨의 IP 주소입니다.
    * `$FILEPATH`은 Azure NetApp Files 볼륨의 내보내기 경로입니다.
    * `$MOUNTPOINT`는 NFS 내보내기를 탑재 하는 데 사용 되는 Linux 호스트에 생성 되는 디렉터리입니다.

4. NFS를 사용 하 여 Windows에 볼륨을 탑재 하려는 경우:

    a. 먼저 Unix 또는 Linux VM에 볼륨을 탑재 합니다.  
    b. 볼륨에 대해 `chmod 777` 또는 `chmod 775` 명령을 실행 합니다.  
    다. Windows에서 NFS 클라이언트를 통해 볼륨을 탑재 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 NFSv 4.1 기본 도메인 구성](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS Faq](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
