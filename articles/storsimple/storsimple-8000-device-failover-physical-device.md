---
title: StorSimple 8000 시리즈 실제 디바이스로 StorSimple 장애 조치(failover), 재해 복구 | Microsoft Docs
description: 다른 실제 디바이스로 StorSimple 8000 시리즈 실제 디바이스를 장애 조치(failover)하는 방법에 대해 알아봅니다.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60577246"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>StorSimple 8000 시리즈 실제 디바이스에 장애 조치

## <a name="overview"></a>개요

이 자습서에서는 재해가 발생하는 경우 StorSimple 8000 시리즈 실제 디바이스를 다른 StorSimple 실제 디바이스로 장애 조치(failover)하는 데 필요한 단계를 설명합니다. StorSimple에서는 디바이스 장애 조치(failover) 기능을 사용하여 데이터 센터의 원본 물리적 디바이스에서 다른 물리적 디바이스로 데이터를 마이그레이션합니다. 이 자습서의 지침은 소프트웨어 버전 업데이트 3 이상을 실행하는 StorSimple 8000 시리즈 물리적 디바이스에 적용됩니다.

디바이스 장애 조치(failover) 및 재해 복구에 사용되는 방법에 대해 자세히 알아보려면 [StorSimple 8000 시리즈 디바이스에 대한 장애 조치(failover) 및 재해 복구](storsimple-8000-device-failover-disaster-recovery.md)로 이동하세요.

StorSimple 물리적 디바이스를 StorSimple Cloud Appliance로 장애 조치(failover)하려면 [StorSimple Cloud Appliance로 장애 조치(failover)](storsimple-8000-device-failover-cloud-appliance.md)로 이동하세요. 실제 디바이스를 자체 디바이스로 장애 조치(failover)하려면 [동일한 StorSimple 물리적 디바이스로 장애 조치(failover)](storsimple-8000-device-failover-same-device.md)로 이동하세요.


## <a name="prerequisites"></a>필수 조건

- 디바이스 장애 조치(failover)에 대한 고려 사항을 검토했는지 확인하세요. 자세한 내용을 보려면 [디바이스 장애 조치(failover)에 대한 일반적인 고려 사항](storsimple-8000-device-failover-disaster-recovery.md)으로 이동하세요.

- 데이터 센터에서 배포된 StorSimple 8000 시리즈 실제 디바이스가 있어야 합니다. 디바이스는 업데이트 3 이후 소프트웨어 버전을 실행해야 합니다. 자세한 내용은 [온-프레미스 StorSimple 디바이스 배포](storsimple-8000-deployment-walkthrough-u2.md)를 참조하세요.


## <a name="steps-to-fail-over-to-a-physical-device"></a>실제 디바이스로 장애 조치하는 단계

대상 실제 디바이스에 디바이스를 복원하려면 다음 단계를 수행합니다.

1. 장애 조치하려는 볼륨 컨테이너에 연결된 클라우드 스냅숏이 있는지 확인합니다. 자세한 내용을 보려면 [StorSimple 디바이스 관리자 서비스를 사용하여 백업 만들기](storsimple-8000-manage-backup-policies-u2.md)로 이동합니다.
2. StorSimple 디바이스 관리자로 이동한 다음 **디바이스**를 클릭합니다. **디바이스** 블레이드에서 서비스와 연결된 디바이스 목록으로 이동합니다.
    ![디바이스 선택](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. 원본 디바이스를 선택하고 클릭합니다. 원본 디바이스에는 장애 조치(failover)하려는 볼륨 컨테이너가 있습니다. **설정 > 볼륨 컨테이너**로 이동합니다.
4. 다른 디바이스에 장애 조치하려는 볼륨 컨테이너를 선택합니다. 볼륨 컨테이너를 클릭하여 이 컨테이너 내에 볼륨의 목록을 표시합니다. 볼륨을 선택하고 마우스 오른쪽 단추를 클릭한 다음, **오프라인으로 전환**을 클릭하여 볼륨을 오프라인으로 전환합니다. 볼륨 컨테이너의 모든 볼륨에 이 프로세스를 반복합니다.
5. 다른 디바이스에 장애 조치하려는 모든 볼륨 컨테이너에 이전 단계를 반복합니다.
6. **디바이스** 블레이드로 다시 이동합니다. 명령 모음에서 **장애 조치(failover)** 를 클릭합니다.
    ![장애 조치(failover) 클릭](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. **장애 조치(failover)** 블레이드에서 다음 단계를 수행합니다.
   
   1. **원본**을 클릭합니다. 클라우드 스냅숏과 연결된 볼륨이 있는 볼륨 컨테이너가 표시됩니다. 표시된 컨테이너만이 장애 조치의 대상이 됩니다. 볼륨 컨테이너 목록에서 장애 조치할 볼륨 컨테이너를 선택합니다. **클라우드 스냅숏과 연결된 볼륨 컨테이너와 오프라인 볼륨만 표시됩니다.**

       ![원본 선택](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. **대상**을 클릭합니다. 이전 단계에서 선택한 볼륨 컨테이너의 경우 사용 가능한 디바이스의 드롭다운 목록에서 대상 디바이스를 선택합니다. 원본 볼륨 컨테이너를 수용할 용량이 충분한 디바이스만 목록에 표시됩니다.

        ![대상 선택](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. 마지막으로 **요약**에서 모든 장애 조치 설정을 검토합니다. 설정을 검토한 후에 선택한 볼륨 컨테이너의 볼륨이 오프라인 상태임을 나타내는 확인란을 선택합니다. **확인**을 클릭합니다.

       ![장애 조치(failover) 설정 검토](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple은 장애 조치 작업을 만듭니다. **작업** 블레이드를 통해 장애 조치(failover) 작업을 모니터링하려면 작업 알림을 클릭합니다.

    장애 조치된 볼륨 컨테이너에 로컬 볼륨이 있는 경우 컨테이너에서 (계층화된 볼륨이 아닌) 각 로컬 볼륨에 대한 개별 복원 작업이 표시됩니다. 이러한 복원 작업을 완료하려면 상당한 시간이 걸릴 수 있습니다. 장애 조치 작업이 이전에 완료될 가능성이 있습니다. 이러한 볼륨은 복원 작업이 완료된 후에 로컬 보장을 갖습니다.

    ![장애 조치(failover) 작업 모니터링](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. 장애 조치를 완료한 후 **디바이스** 블레이드로 이동합니다.
   
   1. 장애 조치 프로세스에 대한 대상 디바이스로 사용된 디바이스를 선택합니다.

       ![디바이스 선택](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. **볼륨 컨테이너** 블레이드로 이동합니다. 이전 디바이스의 볼륨과 함께 모든 볼륨 컨테이너가 나열됩니다.

       ![대상 볼륨 컨테이너 보기](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>다음 단계

* 장애 조치(failover)를 수행한 후에 [StorSimple 디바이스 비활성화 또는 삭제](storsimple-8000-deactivate-and-delete-device.md)를 해야 할 수도 있습니다.
* StorSimple 디바이스 관리자 서비스를 사용하는 방법에 대한 자세한 내용을 보려면 [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스 관리](storsimple-8000-manager-service-administration.md)로 이동하세요.

