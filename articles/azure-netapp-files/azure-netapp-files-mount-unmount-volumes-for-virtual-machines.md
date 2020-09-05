---
title: 가상 컴퓨터에 대 한 Azure NetApp Files 볼륨 탑재
description: Azure에서 Windows 가상 머신 또는 Linux 가상 머신에 대 한 볼륨을 탑재 또는 분리 하는 방법에 대해 알아봅니다.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 08/28/2020
ms.openlocfilehash: f9dc54959979d00d57536e3a3fa2262d27e28f96
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89072199"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제 

필요에 따라 Windows 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제할 수 있습니다.  Linux 가상 머신에 대한 탑재 지침은 Azure NetApp Files에서 사용할 수 있습니다.  

> [!IMPORTANT] 
> NFS 볼륨에 액세스할 수 있으려면 하나 이상의 내보내기 정책이 있어야 합니다.

1. **볼륨** 블레이드를 클릭 하 고 탑재 하려는 볼륨을 선택 합니다. 
2. 선택한 볼륨에서 **명령 탑재** 를 클릭 한 다음 지침에 따라 볼륨을 탑재 합니다. 

    ![NFS 탑재 명령](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![탑재 명령 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * NFS 볼륨을 탑재 하는 경우 명령에 옵션을 사용 하 여 `vers` 탑재 하려는 `mount` 볼륨에 해당 하는 nfs 프로토콜 버전을 지정 해야 합니다. 
    * NFSv 4.1을 사용 하는 경우 다음 명령을 사용 하 여 파일 시스템을 탑재 합니다.  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > NFSv 4.1을 사용 하는 경우 내보내기를 탑재 하는 모든 Vm이 고유한 호스트 이름을 사용 하는지 확인 합니다.

3. Azure VM이 시작 되거나 다시 부팅 될 때 NFS 볼륨이 자동으로 탑재 되도록 하려면 호스트의 파일에 항목을 추가 합니다 `/etc/fstab` . 

    예: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` 볼륨 속성 블레이드에서 찾은 Azure NetApp Files 볼륨의 IP 주소입니다.
    * `$FILEPATH` Azure NetApp Files 볼륨의 내보내기 경로입니다.
    * `$MOUNTPOINT` 는 NFS 내보내기를 탑재 하는 데 사용 되는 Linux 호스트에 생성 되는 디렉터리입니다.

4. NFS를 사용 하 여 Windows에 볼륨을 탑재 하려는 경우:

    a. 먼저 Unix 또는 Linux VM에 볼륨을 탑재 합니다.  
    b. 볼륨에 `chmod 777` `chmod 775` 대해 또는 명령을 실행 합니다.  
    다. Windows에서 NFS 클라이언트를 통해 볼륨을 탑재 합니다.
    
5. NFS Kerberos 볼륨을 탑재 하려면 [nfsv 4.1 kerberos 암호화 구성](configure-kerberos-encryption.md) 에서 추가 정보를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 NFSv4.1 기본 도메인 구성](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [네트워크 파일 시스템 개요](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
* [NFS Kerberos 볼륨 탑재](configure-kerberos-encryption.md#kerberos_mount)
