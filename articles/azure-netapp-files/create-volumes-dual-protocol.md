---
title: Azure NetApp Files에 대 한 이중 프로토콜 (NFSv3 및 SMB) 볼륨 만들기 Microsoft Docs
description: LDAP 사용자 매핑을 지 원하는 NFSv3 및 SMB의 이중 프로토콜을 사용 하는 볼륨을 만드는 방법에 대해 설명 합니다.
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
ms.date: 8/11/2020
ms.author: b-juche
ms.openlocfilehash: f4cc253de0de9d099cfc4881f48182cf9b2a1616
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88134584"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 이중 프로토콜 (NFSv3 및 SMB) 볼륨 만들기

Azure NetApp Files에서는 NFS (NFSv3 및 NFSv 4.1), SMBv3 또는 이중 프로토콜을 사용 하 여 볼륨을 만들 수 있습니다. 이 문서에서는 LDAP 사용자 매핑을 지 원하는 NFSv3 및 SMB의 이중 프로토콜을 사용 하는 볼륨을 만드는 방법을 보여 줍니다.  


## <a name="before-you-begin"></a>시작하기 전에 

* 용량 풀을 설정해야 합니다.  
    [용량 풀 설정을](azure-netapp-files-set-up-capacity-pool.md)참조 하세요.   
* Azure NetApp Files에 서브넷을 위임해야 합니다.  
    [Azure NetApp Files에 서브넷 위임을](azure-netapp-files-delegate-subnet.md)참조 하세요.

## <a name="considerations"></a>고려 사항

* [Active Directory 연결에 대 한 요구 사항을](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections)충족 하는지 확인 합니다. 
* DNS 서버에 역방향 조회 영역을 만든 다음 해당 역방향 조회 영역에 AD 호스트 컴퓨터의 포인터 (PTR) 레코드를 추가 합니다. 그렇지 않으면 이중 프로토콜 볼륨 만들기가 실패 합니다.
* NFS 클라이언트가 최신 상태이 고 운영 체제에 대 한 최신 업데이트를 실행 중인지 확인 합니다.

## <a name="create-a-dual-protocol-volume"></a>이중 프로토콜 볼륨 만들기

1.  용량 풀 블레이드에서 **볼륨** 블레이드를 클릭합니다. **+ 볼륨 추가**를 클릭하여 볼륨을 만듭니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  볼륨 만들기 창에서 **만들기**를 클릭 하 고 기본 사항 탭에서 다음 필드에 대 한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 영숫자 문자를 사용할 수 있습니다.   

        를 `default` 볼륨 이름으로 사용할 수 없습니다.

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
        
        서브넷을 위임하지 않은 경우에는 볼륨 만들기 페이지에서 **새로 만들기**를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes**를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 Vnet에서 하나의 서브넷만 Azure NetApp Files로 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 볼륨에 기존 스냅숏 정책을 적용 하려면 **고급 섹션 표시** 를 클릭 하 여 확장 하 고 풀 다운 메뉴에서 스냅숏 정책을 선택 합니다. 

        스냅숏 정책을 만드는 방법에 대 한 자세한 내용은 [스냅숏 정책 관리](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)를 참조 하세요.

        ![고급 선택 표시](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. **프로토콜**을 클릭한 후, 다음 작업을 완료합니다.  
    * 볼륨에 대 한 프로토콜 유형으로 **이중 프로토콜 (NFSv3 및 SMB)** 을 선택 합니다.   

    * 드롭다운 목록에서 **Active Directory** 연결을 선택 합니다.  
    사용 하는 Active Directory에는 서버 루트 CA 인증서가 있어야 합니다. 

    * 볼륨에 대 한 **볼륨 경로** 를 지정 합니다.   
    이 볼륨 경로는 공유 볼륨의 이름입니다. 이름은 알파벳 문자로 시작 해야 하 고 각 구독 및 각 지역 내에서 고유 해야 합니다.  

    * 사용할 **보안 스타일** 을 NTFS (기본값) 또는 UNIX로 지정 합니다.

    * 필요에 따라 [볼륨에 대 한 내보내기 정책을 구성](azure-netapp-files-configure-export-policy.md)합니다.

    ![이중 프로토콜 지정](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. **검토 + 만들기**를 클릭하여 볼륨 정보를 검토합니다. 그런 다음 **만들기** 를 클릭 하 여 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Active Directory 인증 기관 공용 루트 인증서 업로드  

1.  [인증 기관 설치](https://docs.microsoft.com/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) 를 따라 추가 인증 기관을 설치 및 구성 합니다. 

2.  Mmc 스냅인을 사용 하 여 [인증서 보기](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) 를 따라 mmc 스냅인 및 인증서 관리자 도구를 사용 합니다.  
    인증서 관리자 스냅인을 사용 하 여 로컬 장치에 대 한 루트 또는 발급 인증서를 찾습니다. 다음 설정 중 하나에서 인증서 관리 스냅인 명령을 실행 해야 합니다.  
    * 도메인에 가입 하 고 루트 인증서가 설치 된 Windows 기반 클라이언트 
    * 루트 인증서를 포함 하는 도메인의 다른 컴퓨터  

3. 루트 인증서를 내보냅니다.  
    인증서를 Base-64로 인코딩된 x.509 ()로 내보내야 합니다. CER) 형식: 

    ![인증서 내보내기 마법사](../media/azure-netapp-files/certificate-export-wizard.png)

4. 이중 프로토콜 볼륨의 NetApp 계정으로 이동 하 고, **연결 Active Directory**을 클릭 하 고, **조인 Active Directory** 창을 사용 하 여 루트 CA 인증서를 업로드 합니다.  

    ![서버 루트 CA 인증서](../media/azure-netapp-files/server-root-ca-certificate.png)

    DNS에서 인증 기관 이름을 확인할 수 있는지 확인 합니다. 이 이름은 인증서의 "발급 대상" 또는 "발급자" 필드입니다.  

    ![인증서 정보](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX 특성 관리

Active Directory 사용자 및 컴퓨터 MMC 스냅인을 사용 하 여 UID, 홈 디렉터리 및 기타 값과 같은 POSIX 특성을 관리할 수 있습니다.  다음 예에서는 Active Directory 특성 편집기를 보여 줍니다.  

![Active Directory 특성 편집기](../media/azure-netapp-files/active-directory-attribute-editor.png) 


## <a name="configure-the-nfs-client"></a>NFS 클라이언트 구성 

Nfs 클라이언트를 구성 하려면 [Azure NetApp Files에 대 한 nfs 클라이언트 구성](configure-nfs-clients.md) 의 지침을 따르세요.  

## <a name="next-steps"></a>다음 단계  

* [이중 프로토콜 Faq](azure-netapp-files-faqs.md#dual-protocol-faqs)
* [Azure NetApp Files에 대한 NFS 클라이언트 구성](configure-nfs-clients.md) 
