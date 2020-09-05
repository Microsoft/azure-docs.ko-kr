---
title: NetApp Azure Files를 사용하여 스냅샷 관리 | Microsoft Docs
description: Azure NetApp Files를 사용 하 여 스냅숏을 만들고 관리 하는 방법을 설명 합니다.
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
ms.openlocfilehash: d70558efb1ea54f069981062e5379d995dbeddd2
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950343"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>NetApp Azure Files를 사용하여 스냅샷 관리

Azure NetApp Files는 주문형 스냅숏 만들기 및 스냅숏 정책 사용을 지원 하 여 자동 스냅숏 생성을 예약 합니다.  스냅숏을 새 볼륨으로 복원할 수도 있습니다.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>볼륨에 대한 주문형 스냅샷 만들기

요청 시 볼륨 스냅숏을 만들 수 있습니다. 

1.  스냅숏을 만들 볼륨으로 이동 합니다. **스냅숏**을 클릭 합니다.

    ![스냅샷으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  **+ 스냅샷 추가**를 클릭하여 볼륨에 대한 주문형 스냅샷을 만듭니다.

    ![스냅샷 추가](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  새 스냅샷 창에 만들고 있는 새 스냅샷의 이름을 제공합니다.   

    ![새 스냅샷](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. **확인**을 클릭합니다. 

## <a name="manage-snapshot-policies"></a>스냅숏 정책 관리

스냅숏 정책을 사용 하 여 볼륨 스냅숏이 자동으로 수행 되도록 예약할 수 있습니다. 필요에 따라 스냅숏 정책을 수정 하거나 더 이상 필요 하지 않은 스냅숏 정책을 삭제할 수도 있습니다.  

### <a name="register-the-feature"></a>기능 등록

**스냅숏 정책** 기능은 현재 미리 보기로 제공 됩니다. 이 기능을 처음 사용 하는 경우 먼저 기능을 등록 해야 합니다. 

1. 기능을 등록 합니다. 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```

2. 기능 등록의 상태를 확인 합니다. 

    > [!NOTE]
    > **RegistrationState** `Registering` 로 변경 하기 전까지 최대 60 분 동안 registrationstate 상태가 될 수 있습니다 `Registered` . 계속 하기 전에 상태가 **등록** 될 때까지 기다립니다.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSnapshotPolicy
    ```
Azure CLI 명령을 사용 하 여 [`az feature register`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-register) 기능을 [`az feature show`](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-show) 등록 하 고 등록 상태를 표시할 수도 있습니다. 

### <a name="create-a-snapshot-policy"></a>스냅숏 정책 만들기 

스냅숏 정책을 사용 하면 매시간, 매일, 매주 또는 매월 주기로 스냅숏 생성 빈도를 지정할 수 있습니다. 또한 볼륨에 대해 유지할 최대 스냅숏 수를 지정 해야 합니다.  

1.  NetApp 계정 보기에서 **스냅숏 정책**을 클릭 합니다.

    ![스냅숏 정책 탐색](../media/azure-netapp-files/snapshot-policy-navigation.png)

2.  스냅숏 정책 창에서 정책 상태를 **사용**으로 설정 합니다. 

3.  **매시간,** **매일**, **매주**또는 **매월** 탭을 클릭 하 여 매시간, 매일, 매주 또는 매월 스냅숏 정책을 만듭니다. **유지할 스냅숏의 수**를 지정 합니다.  

    볼륨에 허용 되는 최대 스냅숏 수에 대 한 [Azure NetApp Files 리소스 제한을](azure-netapp-files-resource-limits.md) 참조 하세요. 

    다음 예에서는 매시간 스냅숏 정책 구성을 보여 줍니다. 

    ![스냅숏 정책 매시간](../media/azure-netapp-files/snapshot-policy-hourly.png)

    다음 예제에서는 일별 스냅숏 정책 구성을 보여 줍니다.

    ![매일 스냅숏 정책](../media/azure-netapp-files/snapshot-policy-daily.png)

    다음 예에서는 주별 스냅숏 정책 구성을 보여 줍니다.

    ![매주 스냅숏 정책](../media/azure-netapp-files/snapshot-policy-weekly.png)

    다음 예에서는 월간 스냅숏 정책 구성을 보여 줍니다.

    ![스냅숏 정책 매월](../media/azure-netapp-files/snapshot-policy-monthly.png) 

4.  **저장**을 클릭합니다.  

추가 스냅숏 정책을 만들어야 하는 경우 3 단계를 반복 합니다.
만든 정책이 스냅숏 정책 페이지에 표시 됩니다.

볼륨에서 스냅숏 정책을 사용 하려면 해당 [정책을 볼륨에 적용](azure-netapp-files-manage-snapshots.md#apply-a-snapshot-policy-to-a-volume)해야 합니다. 

### <a name="apply-a-snapshot-policy-to-a-volume"></a>볼륨에 스냅숏 정책 적용

만든 스냅숏 정책을 볼륨에 사용 하려면 해당 정책을 볼륨에 적용 해야 합니다. 

1.  **볼륨** 페이지로 이동 하 여 스냅숏 정책을 적용 하려는 볼륨을 마우스 오른쪽 단추로 클릭 하 고 **편집**을 선택 합니다.

    ![볼륨 오른쪽 클릭 메뉴](../media/azure-netapp-files/volume-right-cick-menu.png) 

2.  편집 창의 **스냅숏 정책**에서 볼륨에 사용할 정책을 선택 합니다.  **확인** 을 클릭 하 여 정책을 적용 합니다.  

    ![스냅숏 정책 편집](../media/azure-netapp-files/snapshot-policy-edit.png) 

### <a name="modify-a-snapshot-policy"></a>스냅숏 정책 수정 

기존 스냅숏 정책을 수정 하 여 정책 상태, 스냅숏 빈도 (매시간, 매일, 매주 또는 매월) 또는 유지할 스냅숏 수를 변경할 수 있습니다.  
 
1.  NetApp 계정 보기에서 **스냅숏 정책**을 클릭 합니다.

2.  수정 하려는 스냅숏 정책을 마우스 오른쪽 단추로 클릭 한 다음 **편집**을 선택 합니다.

    ![스냅숏 정책 마우스 오른쪽 단추 클릭 메뉴](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  표시 되는 스냅숏 정책 창에서 변경한 다음 **저장**을 클릭 합니다. 

### <a name="delete-a-snapshot-policy"></a>스냅숏 정책 삭제 

더 이상 유지 하지 않을 스냅숏 정책을 삭제할 수 있습니다.   

1.  NetApp 계정 보기에서 **스냅숏 정책**을 클릭 합니다.

2.  수정 하려는 스냅숏 정책을 마우스 오른쪽 단추로 클릭 한 다음 **삭제**를 선택 합니다.

    ![스냅숏 정책 마우스 오른쪽 단추 클릭 메뉴](../media/azure-netapp-files/snapshot-policy-right-click-menu.png) 

3.  **예** 를 클릭 하 여 스냅숏 정책의 삭제를 확인 합니다.   

    ![스냅숏 정책 삭제 확인](../media/azure-netapp-files/snapshot-policy-delete-confirm.png) 

## <a name="restore-a-snapshot-to-a-new-volume"></a>새 볼륨으로 스냅샷 복원

현재 새 볼륨으로만 스냅샷을 복원할 수 있습니다. 
1. 볼륨 **블레이드에서 스냅숏을 선택 하** 여 스냅숏 목록을 표시 합니다. 
2. 복원할 스냅숏을 마우스 오른쪽 단추로 클릭 하 고 메뉴 옵션에서 **새 볼륨으로 복원을** 선택 합니다.  

    ![새 볼륨으로 스냅샷 복원](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

3. 볼륨 만들기 창에서 새 볼륨에 대 한 정보를 제공 합니다.  
    * **이름**   
        만들고 있는 볼륨의 이름을 지정합니다.  
        
        이름은 각 리소스 그룹 내에서 고유해야 합니다. 3자 이상이어야 합니다.  영숫자 문자를 사용할 수 있습니다.

    * **할당량**  
        볼륨에 할당 하려는 논리적 저장소 크기를 지정 합니다.  

    ![새 볼륨으로 복원](../media/azure-netapp-files/snapshot-restore-new-volume.png) 

4. **검토 + 만들기**를 클릭 합니다.  **만들기**를 클릭합니다.   
    새 볼륨은 스냅숏에 사용 되는 것과 동일한 프로토콜을 사용 합니다.   
    스냅샷이 복원된 새 볼륨은 볼륨 블레이드에 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
