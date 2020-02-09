---
title: Azure NetApp Files에 대한 SMB 볼륨 만들기 | Microsoft Docs
description: Azure NetApp Files에 대한 SMB 볼륨을 만드는 방법을 설명 합니다.
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
ms.date: 02/05/2020
ms.author: b-juche
ms.openlocfilehash: c65da771dd483b3a79785d4bec2b89cbeefca5c4
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77049879"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 SMB 볼륨 만들기

Azure NetApp Files는 NFS 및 SMBv3 볼륨을 지원 합니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 이 문서에서는 SMBv3 볼륨을 만드는 방법을 보여 줍니다. NFS 볼륨을 만들려면 [Azure NetApp Files에 대한 nfs 볼륨 만들기](azure-netapp-files-create-volumes.md)를 참조 하세요. 

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.   
[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files에 서브넷을 위임해야 합니다.  
[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 연결에 대한 요구 사항

 SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Active Directory 연결에 대한 요구 사항은 다음과 같습니다. 

* 사용 하는 관리자 계정은 지정할 OU (조직 구성 단위) 경로에 컴퓨터 계정을 만들 수 있어야 합니다.  

* 해당 하는 Windows Active Directory (AD) 서버에서 적절 한 포트가 열려 있어야 합니다.  
    필요한 포트는 다음과 같습니다. 

    |     Service           |     포트     |     프로토콜     |
    |-----------------------|--------------|------------------|
    |    AD 웹 서비스    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    N/A       |    Echo Reply    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    NetBIOS 이름       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    w32time            |    123       |    UDP           |

* 대상 Active Directory Domain Services에 대한 사이트 토폴로지는 Azure NetApp Files 배포 되는 Azure VNet의 모범 사례를 준수 해야 합니다.  

    Azure NetApp Files 배포 되는 가상 네트워크의 주소 공간은 기존 또는 새 Active Directory 사이트에 추가 해야 합니다 (Azure NetApp Files에서 연결할 수 있는 도메인 컨트롤러). 

* Azure NetApp Files의 [위임 된 서브넷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet) 에서 지정 된 DNS 서버에 연결할 수 있어야 합니다.  

    지원 되는 네트워크 토폴로지의 [Azure NetApp Files 네트워크 계획에 대한 지침을](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies) 참조 하세요.

    NSGs (네트워크 보안 그룹) 및 방화벽에는 Active Directory 및 DNS 트래픽 요청을 허용 하는 적절 하 게 구성 된 규칙이 있어야 합니다. 

* Azure NetApp Files 위임 된 서브넷은 모든 로컬 및 원격 도메인 컨트롤러를 포함 하 여 도메인의 모든 Active Directory Domain Services (추가) 도메인 컨트롤러에 연결할 수 있어야 합니다. 그렇지 않으면 서비스 중단이 발생할 수 있습니다.  

    Azure NetApp Files 위임 된 서브넷을 통해 연결할 수 없는 도메인 컨트롤러가 있는 경우 Azure 지원 요청을 제출 하 여 범위를 **전역** (기본값)에서 **사이트**로 변경할 수 있습니다.  Azure NetApp Files은 Azure NetApp Files 위임 된 서브넷 주소 공간이 있는 사이트의 도메인 컨트롤러와만 통신 해야 합니다.

    AD 사이트 및 서비스에 대한 [사이트 토폴로지 디자인을](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology) 참조 하세요. 
    
추가 AD 정보에 대한 Azure NetApp Files [SMB faq](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs) 를 참조 하세요. 

## <a name="create-an-active-directory-connection"></a>Active Directory 연결 만들기

1. NetApp 계정에서 **Active Directory 연결**을 클릭 한 다음 **조인**을 클릭 합니다.  

    ![연결 Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. 조인 Active Directory 창에서 다음 정보를 제공 합니다.

    * **기본 DNS**  
        Active Directory 도메인 가입 및 SMB 인증 작업에 필요한 DNS입니다. 
    * **보조 DNS**   
        중복 된 이름 서비스를 보장 하기 위한 보조 DNS 서버입니다. 
    * **도메인**  
        가입 하려는 Active Directory Domain Services의 도메인 이름입니다.
    * **SMB 서버 (컴퓨터 계정) 접두사**  
        Azure NetApp Files에서 새 계정을 만드는 데 사용할 Active Directory의 컴퓨터 계정에 대한 명명 접두사입니다.

        예를 들어 조직에서 파일 서버에 사용 하는 명명 표준이 NAS-01, NAS-02 ..., NAS-045 인 경우 접두사에 "NAS"를 입력 합니다. 

        이 서비스는 필요에 따라 Active Directory에서 추가 컴퓨터 계정을 만듭니다.

    * **조직 구성 단위 경로**  
        SMB 서버 컴퓨터 계정이 생성 되는 OU (조직 구성 단위)에 대한 LDAP 경로입니다. 즉, OU = second level, OU = first level입니다. 

        Azure Active Directory Domain Services에서 Azure NetApp Files를 사용 하는 경우 NetApp 계정에 대한 Active Directory를 구성할 때 조직 구성 단위 경로가 `OU=AADDC Computers` 됩니다.
        
    * **사용자 이름** 및 **암호** 를 포함 한 자격 증명

    ![조인 Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **조인**을 클릭합니다.  

    만든 Active Directory 연결이 표시 됩니다.

    ![연결 Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

> [!NOTE] 
> Active Directory 연결을 저장 한 후 사용자 이름 및 암호 필드를 편집할 수 있습니다. 연결을 저장 한 후에는 다른 값을 편집할 수 없습니다. 다른 값을 변경 해야 하는 경우 먼저 배포 된 SMB 볼륨을 삭제 한 다음 Active Directory 연결을 삭제 하 고 다시 만들어야 합니다.

## <a name="add-an-smb-volume"></a>SMB 볼륨 추가

1. 용량 풀 블레이드에서 **볼륨** 블레이드를 클릭 합니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    볼륨 만들기 창이 나타납니다.

3. 볼륨 만들기 창에서 **만들기** 를 클릭 하 고 다음 필드에 대한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 모든 영숫자 문자를 사용할 수 있습니다.   

        `default`를 볼륨 이름으로 사용할 수 없습니다.

    * **용량 풀**  
        볼륨을 만들 용량 풀을 지정 합니다.

    * **할당량**  
        볼륨에 할당되는 논리 스토리지의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스 하려는 Azure VNet (가상 네트워크)을 지정 합니다.  

        사용자가 지정 하는 VNet에는 Azure NetApp Files에 대해 위임 된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어 링을 통해 볼륨과 동일한 지역에 있는 VNet 에서만 액세스할 수 있습니다. Express 경로를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우에는 볼륨 만들기 페이지에서 **새로 만들기**를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp Files로 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **프로토콜** 을 클릭 하 고 다음 정보를 완료 합니다.  
    * 볼륨의 프로토콜 유형으로 **SMB** 를 선택 합니다. 
    * 드롭다운 목록에서 **Active Directory** 연결을 선택 합니다.
    * 공유 **이름**에 공유 볼륨의 이름을 지정 합니다.

    ![SMB 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **검토 + 만들기** 를 클릭 하 여 볼륨 세부 정보를 검토 합니다.  그런 다음 **만들기** 를 클릭 하 여 SMB 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시 됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계  

* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [SMB Faq](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure CLI를 사용 하 여 새 Active Directory 포리스트 설치](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
