---
title: NFS 볼륨에 대한 내보내기 정책 구성-Azure NetApp Files
description: Azure NetApp Files를 사용 하 여 NFS 볼륨에 대한 액세스를 제어 하도록 내보내기 정책을 구성 하는 방법을 설명 합니다.
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75551561"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>NFS 볼륨에 대한 내보내기 정책 구성

필요에 따라 내보내기 정책을 구성하여 Azure NetApp Files 볼륨에 대한 액세스를 제어할 수 있습니다. Azure NetApp Files 내보내기 정책은 NFS 볼륨만 지원 합니다.  NFSv3 및 NFSv4가 모두 지원 됩니다. 

## <a name="steps"></a>단계 

1.  Azure NetApp Files 탐색 창에서 **정책 내보내기** 를 클릭 합니다. 

2.  내보내기 정책 규칙을 만들도록 다음 필드에 대한 정보를 지정합니다.   
    *  **인덱스**   
        규칙에 대한 인덱스 번호를 지정합니다.  
        내보내기 정책은 최대 5개의 규칙으로 구성할 수 있습니다. 규칙은 인덱스 번호 목록의 해당 순서에 따라 평가됩니다. 더 낮은 인덱스 번호의 규칙이 먼저 평가됩니다. 예를 들어 인덱스 번호가 1인 규칙은 인덱스 번호가 2인 규칙보다 먼저 계산됩니다. 

    * **허용된 클라이언트**   
        다음 형식 중 하나에 값을 지정합니다.  
        * IPv4 주소(예: `10.1.12.24`) 
        * 비트 수로 표현된 서브넷 마스크가 있는 IPv4 주소(예: `10.1.12.10/4`)

    * **Access**  
        다음 액세스 유형 중 하나를 선택합니다.  
        * 액세스 권한 없음 
        * 읽기 및 쓰기
        * 읽기 전용

    ![내보내기 정책](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>다음 단계 
* [볼륨 관리](azure-netapp-files-manage-volumes.md)
* [가상 머신에 대한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [스냅샷 관리](azure-netapp-files-manage-snapshots.md)
