---
title: Azure NetApp Files에 대한 SMB 볼륨 만들기 | Microsoft Docs
description: 이 문서에서는 Azure NetApp Files에서 SMBv3 볼륨을 만드는 방법을 보여 줍니다. Active Directory 연결 및 도메인 서비스에 대 한 요구 사항에 대해 알아봅니다.
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
ms.topic: how-to
ms.date: 08/26/2020
ms.author: b-juche
ms.openlocfilehash: e85a78582c0f7aac188198ad91f9ac91ddf62961
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950377"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 SMB 볼륨 만들기

Azure NetApp Files에서는 NFS (NFSv3 및 NFSv 4.1), SMBv3 또는 이중 프로토콜 (NFSv3 및 SMB)을 사용 하 여 볼륨을 만들 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 이 문서에서는 SMBv3 볼륨을 만드는 방법을 보여줍니다.

## <a name="before-you-begin"></a>시작하기 전에 
용량 풀을 설정해야 합니다.   
[용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)   
Azure NetApp Files에 서브넷을 위임해야 합니다.  
[Azure NetApp Files에 서브넷 위임](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Active Directory 연결에 대한 요구 사항

 SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Active Directory 연결에 대한 요구 사항은 다음과 같습니다. 

* 사용하는 관리자 계정은 사용자가 지정할 OU(조직 구성 단위) 경로에 머신 계정을 만들 수 있어야 합니다.  

* 해당하는 Windows Active Directory(AD) 서버에 적절한 포트가 열려 있어야 합니다.  
    필요한 포트는 다음과 같습니다. 

    |     서비스           |     포트     |     프로토콜     |
    |-----------------------|--------------|------------------|
    |    AD 웹 서비스    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    ICMPv4             |    해당 없음       |    Echo Reply    |
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

* 대상 Active Directory Domain Services에 대한 사이트 토폴로지는 지침, 특히 Azure NetApp Files가 배포되는 Azure VNet 관련 지침을 준수해야 합니다.  

    Azure NetApp Files에서 연결할 수 있는 도메인 컨트롤러가 있는 신규 또는 기존 Active Directory 사이트에 Azure NetApp Files가 배포되는 가상 네트워크의 주소 공간을 추가해야 합니다. 

* 지정된 DNS 서버는 Azure NetApp Files의 [위임된 서브넷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)에서 연결할 수 있어야 합니다.  

    지원되는 네트워크 토폴로지는 [Azure NetApp Files 네트워크 계획 지침](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies)을 참조하세요.

    NSG(네트워크 보안 그룹)와 방화벽에 Active Directory 및 DNS 트래픽 요청을 허용하는 규칙이 적절히 구성되어 있어야 합니다. 

* Azure NetApp Files에 위임된 서브넷은 모든 로컬 및 원격 도메인 컨트롤러를 포함하여 도메인의 모든 Active Directory Domain Services(ADDS) 도메인 컨트롤러에 연결할 수 있어야 합니다. 그렇지 않으면 서비스 중단이 발생할 수 있습니다.  

    Azure NetApp Files에 위임된 서브넷에서 연결할 수 없는 도메인 컨트롤러가 있는 경우 Active Directory 연결을 만드는 동안 Active Directory 사이트를 지정할 수 있습니다.  Azure NetApp Files가 Azure NetApp Files에 위임된 서브넷 주소 공간이 있는 사이트의 도메인 컨트롤러와만 통신해야 합니다.

    AD 사이트 및 서비스에 대한 [사이트 토폴로지 디자인](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology)을 참조하세요. 
    
<!--
* Azure NetApp Files supports DES, Kerberos AES 128, and Kerberos AES 256 encryption types (from the least secure to the most secure). The user credentials used to join Active Directory must have the highest corresponding account option enabled that matches the capabilities enabled for your Active Directory.   

    For example, if your Active Directory has only the AES-128 capability, you must enable the AES-128 account option for the user credentials. If your Active Directory has the AES-256 capability, you must enable the AES-256 account option (which also supports AES-128). If your Active Directory does not have any Kerberos encryption capability, Azure NetApp Files uses DES by default.  

    You can enable the account options in the properties of the Active Directory Users and Computers Microsoft Management Console (MMC):   

    ![Active Directory Users and Computers MMC](../media/azure-netapp-files/ad-users-computers-mmc.png)
-->

추가 AD 정보에 대한 Azure NetApp Files [SMB FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)를 참조하세요. 

## <a name="decide-which-domain-services-to-use"></a>사용할 도메인 서비스 결정 

Azure NetApp Files는 AD 연결을 위해 [Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology)(ADDS)와 Azure Active Directory Domain Services(AADDS)를 모두 지원합니다.  AD 연결을 만들기 전에 ADDS를 사용할지 아니면 AADDS를 사용할지 결정해야 합니다.  

자세한 내용은 [자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)를 참조하세요. 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Azure NetApp Files에 기본 [Active Directory 사이트 및 서비스](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) 범위를 사용할 수 있습니다. 이 옵션을 사용하면 [Azure NetApp Files에서 액세스할 수 있는](azure-netapp-files-network-topologies.md) Active Directory Domain Services(ADDS) 도메인 컨트롤러에 대한 읽기 및 쓰기가 가능합니다. 또한 이 옵션은 지정된 Active Directory 사이트 및 서비스 사이트에 없는 도메인 컨트롤러와 서비스가 통신하는 것을 방지합니다. 

ADDS를 사용할 때 사이트 이름을 찾기 위해 Active Directory Domain Services를 담당하는 조직의 관리 그룹에 문의할 수 있습니다. 아래 예에서는 사이트 이름이 표시되는 Active Directory 사이트 및 서비스 플러그 인을 보여줍니다. 

![Active Directory 사이트 및 서비스](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Azure NetApp Files에 대한 AD 연결을 구성하는 경우 **AD 사이트 이름** 필드의 범위에 사이트 이름을 지정합니다.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Azure Active Directory Domain Services(AADDS) 구성 및 지침은 [Microsoft Azure Active Directory Domain Services 설명서](https://docs.microsoft.com/azure/active-directory-domain-services/)를 참조하세요.

Azure NetApp Files에는 다음과 같은 추가 AADDS 고려 사항이 적용됩니다. 

* AADDS가 배포되는 VNet 또는 서브넷이 Azure NetApp Files 배포와 동일한 Azure 지역에 있는지 확인합니다.
* Azure NetApp Files가 배포되는 지역에서 다른 VNet을 사용하는 경우 두 VNet 간에 피어링을 만들어야 합니다.
* Azure NetApp Files는 `user` 및 `resource forest` 형식을 지원합니다.
* 동기화 유형에 대해 `All` 또는 `Scoped`를 선택할 수 있습니다.   
    `Scoped`를 선택하는 경우 SMB 공유에 액세스하기 위해 올바른 Microsoft Azure Active Directory 그룹을 선택했는지 확인합니다.  확실하지 않은 경우 `All` 동기화 유형을 사용할 수 있습니다.
* Enterprise 또는 Premium SKU를 사용해야 합니다. 표준 SKU는 지원되지 않습니다.

Active Directory 연결을 만들 때 AADDS에 대한 다음 사항에 유의해야 합니다.

* AADDS 메뉴에서 **기본 DNS**, **보조 DNS**및 **AD DNS 도메인 이름**에 대한 정보를 찾을 수 있습니다.  
DNS 서버의 경우 Active Directory 연결 구성에 2개의 IP 주소가 사용됩니다. 
* **조직 구성 단위 경로**는 `OU=AADDC Computers`입니다.  
이 설정은 **Active Directory 연결**의 **NetApp 계정**에서 구성합니다.

  ![조직 구성 단위 경로](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* **사용자 이름** 자격 증명은 Microsoft Azure Active Directory 그룹 **Microsoft Azure Active Directory DC 관리자**의 구성원인 사용자일 수 있습니다.


## <a name="create-an-active-directory-connection"></a>Active Directory 연결 만들기

1. NetApp 계정에서 **Active Directory 연결**을 클릭하고 **조인**을 클릭합니다.  

    ![Active Directory 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Active Directory 조인 창에서 사용하려는 도메인 서비스에 따라 다음 정보를 제공합니다.  

    사용하는 도메인 서비스에 대한 자세한 내용은 [사용할 도메인 서비스 결정](#decide-which-domain-services-to-use)을 참조하세요. 

    * **기본 DNS**  
        Active Directory 도메인 가입 및 SMB 인증 작업에 필요한 DNS입니다. 
    * **보조 DNS**   
        중복 이름 서비스 확인을 위한 보조 DNS 서버입니다. 
    * **AD DNS 도메인 이름**  
        가입하려는 Active Directory Domain Services의 도메인 이름입니다.
    * **AD 사이트 이름**  
        도메인 컨트롤러 검색이 제한될 사이트 이름입니다.
    * **SMB 서버(컴퓨터 계정) 접두사**  
        Azure NetApp Files에서 새 계정을 만드는 데 사용할 Active Directory의 머신 계정에 대한 명명 접두사입니다.

        예를 들어 조직에서 파일 서버에 사용하는 명명 표준이 NAS-01, NAS-02..., NAS-045인 경우 접두사로 "NAS"를 입력합니다. 

        필요에 따라 Active Directory에 추가 머신 계정이 만들어집니다.

        > [!IMPORTANT] 
        > Active Directory 연결을 만든 후 SMB 서버 접두사의 이름을 바꾸면 작업이 중단됩니다. SMB 서버 접두사의 이름을 바꾼 후에는 기존 SMB 공유를 다시 탑재해야 합니다.

    * **조직 구성 단위 경로**  
        SMB 서버 머신 계정이 만들어질 OU(조직 구성 단위)의 LDAP 경로(예: OU=두 번째 수준,OU=첫 번째 수준)입니다. 

        Azure Active Directory Domain Services와 함께 Azure NetApp Files를 사용하는 경우 NetApp 계정에 대해 Active Directory를 구성할 때 조직 구성 단위 경로는 `OU=AADDC Computers`입니다.

     * **백업 정책 사용자**  
        Azure NetApp Files에 사용하기 위해 만든 컴퓨터 계정에 대한 높은 권한이 필요한 추가 계정을 포함할 수 있습니다. 지정된 계정은 파일 또는 폴더 수준에서 NTFS 권한을 변경할 수 있습니다. 예를 들어 Azure NetApp Files에서 SMB 파일 공유로 데이터를 마이그레이션하는 데 사용되는 권한 없는 서비스 계정을 지정할 수 있습니다.  

        **백업 정책 사용자** 기능은 현재 미리 보기 상태입니다. 이 기능을 처음 사용 하는 경우이 기능을 사용 하기 전에 등록 합니다. 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        기능 등록의 상태를 확인 합니다. 

        > [!NOTE]
        > **RegistrationState** `Registering` 로 변경 하기 전까지 최대 60 분 동안 registrationstate 상태가 될 수 있습니다 `Registered` . 계속 하기 전에 상태가 **등록** 될 때까지 기다립니다.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Azure CLI 명령을 사용 하 여 [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) 기능을 [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) 등록 하 고 등록 상태를 표시할 수도 있습니다. 

    * **사용자 이름**과 **암호**를 포함한 자격 증명

    ![Active Directory 조인](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. **조인**을 클릭합니다.  

    만든 Active Directory 연결이 나타납니다.

    ![Active Directory 연결](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>SMB 볼륨 추가

1. 용량 풀 블레이드에서 **볼륨** 블레이드를 클릭합니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다.  
    볼륨 만들기 창이 나타납니다.

3. 볼륨 만들기 창에서 **만들기** 를 클릭 하 고 기본 사항 탭에서 다음 필드에 대 한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 영숫자 문자를 사용할 수 있습니다.   

        `default`를 볼륨 이름으로 사용할 수 없습니다.

    * **용량 풀**  
        볼륨을 만들 용량 풀을 지정합니다.

    * **할당량**  
        볼륨에 할당되는 논리 스토리지의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **가상 네트워크**  
        볼륨에 액세스하려는 Microsoft Azure Virtual Network(VNet)를 지정합니다.  

        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 영역에 있는 VNet에서만 액세스할 수 있습니다. Express Route를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우 볼륨 만들기 페이지에서 **새로 만들기**를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 볼륨에 기존 스냅숏 정책을 적용 하려면 **고급 섹션 표시** 를 클릭 하 여 확장 하 고 풀 다운 메뉴에서 스냅숏 정책을 선택 합니다. 

        스냅숏 정책을 만드는 방법에 대 한 자세한 내용은 [스냅숏 정책 관리](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)를 참조 하세요.

        ![고급 선택 표시](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. **프로토콜**을 클릭하고 다음 정보를 입력합니다.  
    * 볼륨의 프로토콜 유형으로 **SMB**를 선택합니다. 
    * 드롭다운 목록에서 **Active Directory** 연결을 선택합니다.
    * **공유 이름**에 공유 볼륨의 이름을 지정합니다.

    ![SMB 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **검토 + 만들기**를 클릭하여 볼륨 정보를 검토합니다.  그런 다음, **만들기**를 클릭하여 SMB 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="control-access-to-an-smb-volume"></a>SMB 볼륨에 대한 액세스 제어  

SMB 볼륨에 대한 액세스는 사용 권한을 통해 관리합니다.  

### <a name="share-permissions"></a>공유 사용 권한  

기본적으로 새 볼륨에는 **모든 사람/모든 권한** 공유 권한이 있습니다. 도메인 관리자 그룹의 구성원은 Azure NetApp Files 볼륨에 사용되는 컴퓨터 계정에서 컴퓨터 관리를 사용하여 공유 사용 권한을 변경할 수 있습니다.

![SMB 탑재 경로](../media/azure-netapp-files/smb-mount-path.png) 
![공유 사용 권한 설정](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 파일 및 폴더 사용 권한  

Windows SMB 클라이언트에서 개체 속성의 **보안** 탭을 사용하여 파일 또는 폴더에 대한 사용 권한을 설정할 수 있습니다.
 
![파일 및 폴더 사용 권한 설정](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>다음 단계  

* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [SMB FAQ](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#smb-faqs)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
* [Azure 명령줄 인터페이스를 사용하여 새 Active Directory 포리스트 설치](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
