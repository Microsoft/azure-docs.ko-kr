---
title: StorSimple 8000 시리즈에서 볼륨 복제 | Microsoft Docs
description: 다른 복제 유형 및 사용에 대해 설명하고, StorSimple 8000 시리즈 디바이스에서 개별 볼륨을 복제하는 데 백업 세트를 사용하는 방법에 대해 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 12/05/2017
ms.author: alkohli
ms.openlocfilehash: 84734aefb72a3330d99c5707b461de2cd5e30484
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637907"
---
# <a name="use-the-storsimple-device-manager-service-in-azure-portal-to-clone-a-volume"></a>Azure Portal에서 StorSimple 디바이스 관리자 서비스를 사용하여 볼륨 복제

## <a name="overview"></a>개요

이 자습서에서는 **Backup 카탈로그** 블레이드를 통해 개별 볼륨을 복제하는 데 백업 세트를 사용하는 방법을 설명합니다. *임시* 및 *영구* 복제의 차이점에 대해서도 설명합니다. 이 자습서의 지침은 업데이트 3 이상을 실행하는 모든 StorSimple 8000 시리즈 디바이스에 적용됩니다.

StorSimple 디바이스 관리자 서비스 **Backup 카탈로그** 블레이드는 수동 또는 자동 백업을 수행할 때 생성되는 모든 백업 세트를 표시합니다. 그런 다음 복제할 백업 세트에서 볼륨을 선택할 수 있습니다.

 ![Backup 세트 목록](./media/storsimple-8000-clone-volume-u2/bucatalog.png)

## <a name="considerations-for-cloning-a-volume"></a>볼륨 복제 시 고려 사항

볼륨을 복제하기 전에 다음 정보를 고려하세요.

- 복제본은 일반 볼륨과 동일한 방식으로 작동합니다. 볼륨에서 사용할 수 있는 모든 작업은 복제에 사용할 수 있습니다.

- 모니터링 및 기본 백업은 복제된 볼륨에서 자동으로 비활성화됩니다. 모든 백업에 복제된 볼륨을 구성해야 합니다.

- 로컬 고정 볼륨은 계층화된 볼륨으로 복제됩니다. 복제된 볼륨을 로컬로 고정해야 하는 경우 복제 작업이 성공적으로 완료된 후 클론을 로컬로 고정된 볼륨으로 변환할 수 있습니다. 계층화된 볼륨을 로컬로 고정된 볼륨으로 변환하는 방법에 대한 자세한 내용은 [볼륨 유형 변경](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)으로 이동하세요.

- 복제 후(여전히 임시 클론일 경우) 즉시 복제된 볼륨을 계층화된 볼륨에서 로컬 고정 볼륨으로 변환하려는 경우 다음 오류 메시지로 전환에 실패합니다.

    `Unable to modify the usage type for volume {0}. This can happen if the volume being modified is a transient clone and hasn’t been made permanent. Take a cloud snapshot of this volume and then retry the modify operation.`

    다른 디바이스에 복제하는 경우 이 오류가 수신됩니다. 먼저 임시 클론을 영구 클론으로 변환시키면 성공적으로 볼륨을 로컬로 고정된 볼륨으로 변환할 수 있습니다. 임시 클론의 클라우드 스냅숏을 만들어서 영구 클론으로 변환합니다.

## <a name="create-a-clone-of-a-volume"></a>볼륨의 클론 만들기

로컬 또는 클라우드 스냅숏을 사용하여 동일한 디바이스, 다른 디바이스 또는 클라우드 어플라이언스에 클론을 만들 수 있습니다.

아래 절차는 백업 카탈로그에서 클론을 만드는 방법을 설명합니다.  복제를 시작하는 다른 방법은 **볼륨**으로 이동하여 볼륨을 선택한 후 상황에 맞는 메뉴를 호출하도록 마우스 오른쪽 단추로 클릭하고 **복제**를 선택합니다.

백업 카탈로그에서 볼륨의 클론을 만들려면 다음 단계를 수행합니다.

#### <a name="to-clone-a-volume"></a>볼륨 복제하기

1. StorSimple 디바이스 관리자 서비스로 이동한 다음 **Backup 카탈로그**를 클릭합니다.

2. 백업 세트를 다음과 같이 선택합니다.
   
   1. 해당 디바이스를 선택합니다.
   2. 드롭다운 목록에서 선택하려는 백업에 대 한 볼륨 또는 백업 정책을 선택합니다.
   3. 시간 범위를 지정합니다.
   4. **적용**을 클릭하여 이 쿼리를 실행합니다.

      선택한 볼륨와 연결된 백업을 또는 백업 정책이 백업 세트의 목록에 나타나야 합니다.
   
      ![Backup 세트 목록](./media/storsimple-8000-clone-volume-u2/bucatalog.png)
     
3. 백업 세트를 확장하여 연결된 볼륨을 보고 백업 세트에서 볼륨을 선택합니다. 마우스 오른쪽 단추를 클릭하고 상황에 맞는 메뉴에서 **복제**를 선택합니다.

   ![Backup 세트 목록](./media/storsimple-8000-clone-volume-u2/clonevol3b.png) 

3. **복제** 블레이드에서 다음 단계를 수행합니다.
   
   1. 대상 디바이스를 식별합니다. 클론이 만들어지는 위치입니다. 동일한 디바이스를 선택하거나 다른 디바이스를 지정할 수 있습니다.

      > [!NOTE]
      > 복제에 필요한 용량은 대상 디바이스에서 사용 가능한 용량보다 작아야 합니다.
       
   2. 해당 클론에 대한 고유 볼륨 이름을 지정합니다. 이름은 3자에서 127자 사이여야 합니다.
      
       > [!NOTE]
       > 로컬로 고정된 볼륨을 복제하더라도 **다른 이름으로 볼륨 복제** 필드는 **계층화됨**이 됩니다. 이 설정을 변경할 수 없습니다. 그러나 로컬로 고정된 복제된 볼륨도 필요한 경우 복제본을 성공적으로 만든 후 로컬로 고정된 볼륨으로 복제본을 변환할 수 있습니다. 계층화된 볼륨을 로컬로 고정된 볼륨으로 변환하는 방법에 대한 자세한 내용은 [볼륨 유형 변경](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)으로 이동하세요.
          
   3. **연결된 호스트** 아래에서 클론에 대한 ACR(액세스 제어 레코드)을 지정합니다. 새 ACR을 추가하거나 기존 목록에서 선택할 수 있습니다. ACR은 이 클론에 액세스할 수 있는 호스트를 결정합니다.
      
       ![Backup 세트 목록](./media/storsimple-8000-clone-volume-u2/clonevol3a.png) 

   4. **복제**를 클릭하여 작업을 완료합니다.

4. 클론 작업이 시작되고 클론이 성공적으로 만들어지면 알림이 표시됩니다. 작업 알림을 클릭하거나 **작업** 블레이드로 이동하여 복제 작업을 모니터링합니다.

    ![Backup 세트 목록](./media/storsimple-8000-clone-volume-u2/clonevol5.png)

7. 복제 작업이 완료되면 디바이스로 이동한 다음 **볼륨**을 클릭합니다. 볼륨의 목록에서 원본 볼륨이 있는 동일한 볼륨 컨테이너에서 방금 만든 클론이 표시되어야 합니다.

    ![Backup 세트 목록](./media/storsimple-8000-clone-volume-u2/clonevol6.png)

이러한 방식으로 만들어진 클론은 임시 클론입니다. 복제 유형에 대한 자세한 내용은 [임시 및 영구 복제본 비교](#transient-vs-permanent-clones)를 참조하세요.


## <a name="transient-vs-permanent-clones"></a>임시 및 영구 복제본 비교
다른 디바이스에 복제하는 경우 임시 클론을 만듭니다. 백업 세트로부터 StorSimple 디바이스 관리자에서 관리하는 다른 디바이스에 특정 볼륨을 복제할 수 있습니다. 임시 클론에는 원본 볼륨의 데이터에 대한 참조가 있으며 대상 디바이스에서 로컬로 읽고 쓰는 동안 해당 데이터를 사용합니다.

임시 클론의 클라우드 스냅숏을 수행한 후 결과 클론은 *영구* 클론이 됩니다. 이 과정에서 데이터 복사본이 클라우드에서 생성되고, 이 데이터를 복사하는 시간은 데이터의 크기 및 Azure 지연 시간에 따라 결정됩니다(Azure 간 복사). 이 프로세스는 며칠에서 몇 주까지 걸릴 수 있습니다. 임시 클론은 영구 클론이 되며, 복제된 원본 볼륨 데이터에 대한 참조가 없습니다.

## <a name="scenarios-for-transient-and-permanent-clones"></a>임시 및 영구 클론에 대한 시나리오
다음 섹션에서는 임시 및 영구 클론을 사용할 수 있는 예제 상황에 대해 설명합니다.

### <a name="item-level-recovery-with-a-transient-clone"></a>임시 클론을 사용하여 항목 수준 복구
1년 된 Microsoft PowerPoint 프레젠테이션 파일을 복구해야 합니다. IT 관리자는 해당 시간에서 특정 백업을 식별하고 볼륨을 필터링합니다. 그런 다음 관리자는 해당 볼륨을 복제하고, 필요한 파일을 찾아 사용자에게 제공합니다. 이 시나리오에서는 임시 클론이 사용됩니다.

### <a name="testing-in-the-production-environment-with-a-permanent-clone"></a>영구 클론을 사용하여 프로덕션 환경에서 테스트
프로덕션 환경에서 테스트 버그를 확인해야 합니다. 프로덕션 환경에서 볼륨의 클론을 만들고 이 클론의 클라우드 스냅숏을 만들어서 독립적으로 복제된 볼륨을 만듭니다. 이 시나리오에서는 영구 클론이 사용됩니다.

## <a name="next-steps"></a>다음 단계
* [백업 세트에서 StorSimple 볼륨을 복원](storsimple-8000-restore-from-backup-set-u2.md)하는 방법에 대해 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 알아봅니다.

