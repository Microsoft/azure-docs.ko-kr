---
title: Azure NetApp Files에 대한 볼륨 만들기 | Microsoft Docs
description: Azure NetApp Files에 대한 볼륨을 만드는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/12/2019
ms.author: b-juche
ms.openlocfilehash: fc748ee993855c77f25f9b115ea472df4281acec
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764367"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 볼륨 만들기

각 용량 풀에는 최대 500개의 볼륨이 있을 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. Azure NetApp 파일은 SMBv3 및 NFS 볼륨을 지원합니다. 

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.   
[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files에 서브넷을 위임해야 합니다.  
[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>NFS 볼륨 만들기

1.  클릭 합니다 **볼륨** 용량 풀 블레이드에서 블레이드입니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    만들기 볼륨 창이 나타납니다.

3.  볼륨 창 만들기를 클릭 **만들기** 하 고 다음 필드에 대 한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **용량 풀**  
        만들 볼륨 용량 풀을 지정 합니다.

    * **할당량**  
        볼륨에 할당되는 논리 저장소의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스하려는 Azure Vnet(가상 네트워크)을 지정합니다.  

        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 영역에 있는 VNet에서만 액세스할 수 있습니다. 또한 Expressroute를 통해 온-프레미스 네트워크에서 볼륨을 액세스할 수 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우에는 볼륨 만들기 페이지에서 **새로 만들기**를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 Vnet에서 하나의 서브넷만 Azure NetApp 파일에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 클릭 **프로토콜**을 선택한 후 **NFS** 볼륨에 대 한 프로토콜 유형으로 합니다.   
    * 지정 된 **file-path** 새 볼륨에 대 한 내보내기 경로 만드는 데 사용할 합니다. 내보내기 경로는 볼륨을 탑재하고 액세스하는 데 사용됩니다.

        파일 경로 이름에는 문자, 숫자 및 하이픈("-")만 포함할 수 있습니다. 이름은 16자~40자여야 합니다.  

    * 필요에 따라 [NFS 볼륨에 대 한 내보내기 정책을 구성 합니다.](azure-netapp-files-configure-export-policy.md)

    ![NFS 프로토콜을 지정 합니다.](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. 클릭 **검토 + 만들기** 볼륨 세부 정보를 검토 합니다.  클릭 **만들기** NFS 볼륨을 만들려고 합니다.

    볼륨 페이지에서 만든 볼륨이 표시 됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="create-an-smb-volume"></a>SMB 볼륨 만들기

Azure NetApp 파일은 SMBv3 볼륨을 지원합니다. SMB 볼륨을 추가 하기 전에 Active Directory 연결을 생성 해야 합니다. 

### <a name="create-an-active-directory-connection"></a>Active Directory 연결 만들기

1. NetApp 계정에서 클릭 **Active Directory 연결**, 클릭 **조인**합니다.  

    ![Active Directory 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Active Directory 연결 창에서 다음 정보를 제공 합니다.

    * **기본 DNS**   
        기본 Active Directory Domain Services 사용에 대 한 Azure NetApp 파일에 대 한 도메인 컨트롤러 IP 주소입니다. 
    * **보조 DNS**  
        보조 Active Directory Domain Services 사용에 대 한 Azure NetApp 파일에 대 한 도메인 컨트롤러 IP 주소입니다. 
    * **도메인**  
        참가 하려는 프로그램 Active Directory Domain Services의 도메인 이름입니다.
    * **SMB 서버 (컴퓨터 계정) 접두사**  
        이 Azure NetApp 파일에서 새 계정 만들기에 사용할 Active Directory에서 컴퓨터 계정에 대 한 명명 접두사입니다.

        예를 들어, 파일 서버에 대 한 조직에서 사용 하는 명명 표준 NAS-01,..., NAS-02 이면 NAS-045 있다면 입력 "NA" 접두사에 대 한 합니다. 

        서비스는 필요에 따라 Active Directory에서 추가 컴퓨터 계정을 만들 됩니다.

    * **조직 구성 단위 경로**  
        SMB 서버 컴퓨터 계정을 만들어지는 조직 구성 단위 (OU)에 대 한 LDAP 경로입니다. 즉, OU = 두 번째 수준, OU = 첫 번째 수준입니다. 
    * 자격 증명을 포함 하 여 **사용자 이름** 고 **암호**

    ![Active Directory 조인](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **조인**을 클릭합니다.  

    사용자가 만든 Active Directory 연결에는 다음이 표시 됩니다.

    ![Active Directory 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>SMB 볼륨을 추가 합니다.

1. 클릭 합니다 **볼륨** 용량 풀 블레이드에서 블레이드입니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    만들기 볼륨 창이 나타납니다.

3. 볼륨 창 만들기를 클릭 **만들기** 하 고 다음 필드에 대 한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **파일 경로**  
        새 볼륨에 대한 내보내기 경로를 만드는 데 사용할 파일 경로를 지정합니다. 내보내기 경로는 볼륨을 탑재하고 액세스하는 데 사용됩니다.   
     
        파일 경로 이름에는 문자, 숫자 및 하이픈("-")만 포함할 수 있습니다. 이름은 16자~40자여야 합니다.  

    * **용량 풀**  
        만들 볼륨 용량 풀을 지정 합니다.

    * **할당량**  
        볼륨에 할당되는 논리 저장소의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스하려는 Azure Vnet(가상 네트워크)을 지정합니다.  

        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 영역에 있는 VNet에서만 액세스할 수 있습니다. 또한 Expressroute를 통해 온-프레미스 네트워크에서 볼륨을 액세스할 수 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우에는 볼륨 만들기 페이지에서 **새로 만들기**를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 Vnet에서 하나의 서브넷만 Azure NetApp 파일에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. 클릭 **프로토콜** 하 고 다음 정보를 완료 합니다.  
    * 선택 **SMB** 볼륨에 대 한 프로토콜 유형으로 합니다. 
    * 선택 하면 **Active Directory** 드롭 다운 목록에서 연결 합니다.
    * 공유 볼륨의 이름을 지정 **공유 이름**합니다.

    ![SMB 프로토콜을 지정 합니다.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. 클릭 **검토 + 만들기** 볼륨 세부 정보를 검토 합니다.  누른 **만들기** 에 SMB 볼륨을 만듭니다.

    볼륨 페이지에서 만든 볼륨이 표시 됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [탑재 하거나 Windows 또는 Linux virtual machines에 대 한 볼륨을 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [NFS 볼륨에 대해 내보내기 정책을 구성 합니다.](azure-netapp-files-configure-export-policy.md)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
