---
title: NetApp Azure Files를 사용하여 스냅숏 관리 | Microsoft Docs
description: Azure NetApp Files를 사용하여 볼륨에 대한 스냅숏을 만들거나 스냅숏에서 새 볼륨으로 복원하는 방법을 설명합니다.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 01387d0c219c86f33762b9c3fbf9f81cf04b4455
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61086852"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>NetApp Azure Files를 사용하여 스냅숏 관리

Azure NetApp Files를 사용하여 볼륨에 대한 주문형 스냅숏을 만들거나 스냅숏에서 새 볼륨으로 복원할 수 있습니다.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>볼륨에 대한 주문형 스냅숏 만들기

요청이 있는 경우에만 스냅숏을 만들 수 있습니다. 스냅숏 정책은 현재 지원되지 않습니다.

1.  [볼륨] 블레이드에서 **스냅숏**을 클릭합니다.

    ![스냅숏으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **+ 스냅숏 추가**를 클릭하여 볼륨에 대한 주문형 스냅숏을 만듭니다.

    ![스냅숏 추가](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  새 스냅숏 창에 만들고 있는 새 스냅숏의 이름을 제공합니다.   

    ![새 스냅숏](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **확인**을 클릭합니다. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>새 볼륨으로 스냅숏 복원

현재 새 볼륨으로만 스냅숏을 복원할 수 있습니다. 
1. 스냅숏 목록을 표시하려면 볼륨 블레이드에서 **스냅숏 관리** 블레이드로 이동합니다. 
2. 복원할 스냅숏을 선택합니다.  
3. 스냅숏 이름을 마우스 오른쪽 단추로 클릭하고 메뉴 옵션에서 **새 볼륨으로 복원**을 선택합니다.  

    ![새 볼륨으로 스냅숏 복원](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. 새 볼륨 창에서 새 볼륨에 대한 정보를 제공합니다.  
    * **이름**   
        만들고 있는 볼륨의 이름을 지정합니다.  
        
        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **파일 경로**     
        새 볼륨에 대한 내보내기 경로를 만드는 데 사용할 파일 경로를 지정합니다. 내보내기 경로는 볼륨을 탑재하고 액세스하는 데 사용됩니다.   
        
        탑재 대상은 NFS 서비스 IP 주소의 엔드포인트입니다. 자동으로 생성됩니다.   
        
        파일 경로 이름에는 문자, 숫자 및 하이픈("-")만 포함할 수 있습니다. 이름은 16자~40자여야 합니다. 

    * **할당량**  
        볼륨에 할당되는 논리 저장소의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.

    *   **가상 네트워크**  
        볼륨에 액세스하려는 Azure Vnet(가상 네트워크)을 지정합니다.  
        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files는 동일한 Vnet 또는 Vnet 피어링을 통해 볼륨과 동일한 지역에 있는 Vnet에서만 액세스할 수 있습니다. Expressroute를 통해 온-프레미스 네트워크에서 볼륨을 액세스할 수 있습니다. 

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files 서비스에 위임되어야 합니다. 서브넷 필드 아래에서 **새로 만들기**를 선택하여 새 서브넷을 만들 수 있습니다.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. **확인**을 클릭합니다.   
    스냅숏이 복원된 새 볼륨은 볼륨 블레이드에 표시됩니다.

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)