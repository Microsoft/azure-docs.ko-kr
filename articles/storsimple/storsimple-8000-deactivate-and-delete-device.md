---
title: StorSimple 8000 시리즈 디바이스 비활성화 및 삭제 | Microsoft Docs
description: 먼저 StorSimple 디바이스를 비활성화한 후 삭제하여 서비스에서 제거하는 방법을 설명합니다.
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
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481977"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>StorSimple 디바이스 비활성화 및 삭제

## <a name="overview"></a>개요

이 문서에서는 StorSimple 디바이스 관리자 서비스에 연결된 StorSimple 디바이스를 비활성화하고 삭제하는 방법에 대해 설명합니다. 이 문서의 지침은 StorSimple Cloud Appliance를 비롯하여 StorSimple 8000 시리즈 디바이스에만 적용됩니다. StorSimple 가상 배열을 사용하는 경우 [StorSimple 가상 배열 비활성화 및 삭제](storsimple-virtual-array-deactivate-and-delete-device.md)로 이동합니다.

비활성화하면 디바이스 및 해당 StorSimple 디바이스 관리자 서비스 간의 연결이 끊깁니다. StorSimple 디바이스 서비스를 중단하고 싶을 수 있습니다(예: 디바이스를 교체 또는 업그레이드하거나 더 이상 StorSimple을 사용하지 않는 경우). 이 경우 디바이스를 삭제하려면 먼저 디바이스를 비활성화해야 합니다.

디바이스를 비활성화하면 디바이스에 로컬로 저장된 데이터에 더 이상 액세스할 수 없게 됩니다. 클라우드에 저장된 디바이스와 연결된 데이터만 복구할 수 있습니다.

> [!WARNING]
> 비활성화는 영구 작업이며 실행 취소할 수 없습니다. 비활성화된 디바이스는 공장 기본값으로 다시 설정되어야만 StorSimple 디바이스 관리자 서비스에 등록할 수 있습니다.
>
> 공장 재설정 프로세스는 디바이스에 로컬로 저장된 모든 데이터를 삭제합니다. 따라서 디바이스를 비활성화하기 전에 모든 데이터의 클라우드 스냅숏을 만들어야 합니다. 이 클라우드 스냅숏을 사용하면 이후 단계에서 모든 데이터를 복구할 수 있습니다.

이 자습서를 읽은 후에 다음을 수행할 수 있습니다.

* 디바이스를 비활성화하고 데이터를 삭제합니다.
* 디바이스를 비활성화하고 데이터를 유지합니다.

> [!NOTE]
> StorSimple의 물리적 디바이스 또는 클라우드 어플라이언스를 비활성화하려면 먼저 해당 디바이스에 의존하는 클라이언트와 호스트를 중지하거나 삭제해야 합니다.


## <a name="deactivate-and-delete-data"></a>데이터 비활성화 및 삭제

디바이스를 완전히 삭제하고 디바이스의 데이터를 보존하지 않으려는 경우 다음 단계를 완료합니다.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>디바이스를 비활성화하고 데이터를 삭제하려면

1. 디바이스를 비활성화하려면 먼저 디바이스와 연결된 모든 볼륨 컨테이너(및 볼륨)를 삭제해야 합니다. 연결된 백업을 삭제한 후에만 볼륨 컨테이너를 삭제할 수 있습니다.

    > [!NOTE]
    > StorSimple 실제 디바이스 또는 클라우드 어플라이언스를 비활성화하기 전에 삭제된 볼륨 컨테이너의 데이터가 디바이스에서 실제로 삭제되었는지 확인합니다. 클라우드 소비량 차트를 모니터링할 수 있으며, 백업을 삭제하여 클라우드 사용량이 감소했음이 확인되면 디바이스 비활성화를 진행할 수 있습니다. 이 삭제를 수행하기 전에 디바이스를 비활성화하면 데이터가 저장소 계정에서 고립되어 비용이 발생합니다.

2. 다음과 같이 디바이스를 비활성화합니다.
   
   1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다. **디바이스** 블레이드에서 비활성화하려는 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **비활성화**를 클릭합니다.

        ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. **비활성화** 블레이드에서 확인할 디바이스 이름을 입력하고 **비활성화**를 클릭합니다. 비활성화 프로세스가 시작되고 완료하는 데 몇 분이 소요됩니다.

        ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. 비활성화한 후 디바이스를 완전히 삭제할 수 있습니다. 디바이스를 삭제하면 서비스에 연결된 디바이스 목록에서 제거됩니다. 그러면 서비스에서 삭제된 디바이스를 더 이상 관리할 수 없습니다. 디바이스를 삭제하려면 다음 단계를 사용합니다.
   
   1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다. **디바이스** 블레이드에서 삭제하려는 비활성화된 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **삭제**를 클릭합니다.

        ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. **삭제** 블레이드에서 확인할 디바이스 이름을 입력하고 **삭제**를 클릭합니다. 삭제 작업을 완료하는 데는 몇 분이 소요됩니다.

        ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. 삭제가 완료되면 알림이 표시됩니다. 디바이스 목록도 삭제를 반영하도록 업데이트됩니다.

## <a name="deactivate-and-retain-data"></a>데이터 비활성화 및 보존

디바이스는 삭제하지만 데이터는 유지하고 싶은 경우 다음 단계를 완료합니다.

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>디바이스를 비활성화하고 데이터를 유지하려면
1. 디바이스를 비활성화합니다. 디바이스의 모든 볼륨 컨테이너 및 스냅숏은 유지됩니다.
   
   1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다. **디바이스** 블레이드에서 비활성화하려는 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **비활성화**를 클릭합니다.

         ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. **비활성화** 블레이드에서 확인할 디바이스 이름을 입력하고 **비활성화**를 클릭합니다. 비활성화 프로세스가 시작되고 완료하는 데 몇 분이 소요됩니다.

         ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. 이제 볼륨 컨테이너와 연결된 스냅숏을 장애 조치(failover)할 수 있습니다. 이에 대한 절차를 보려면 [StorSimple 디바이스에 대한 장애 조치 및 재해 복구](storsimple-8000-device-failover-disaster-recovery.md)로 이동하세요.
3. 비활성화 및 장애 조치(failover) 후 디바이스를 완전히 삭제할 수 있습니다. 디바이스를 삭제하면 서비스에 연결된 디바이스 목록에서 제거됩니다. 그러면 서비스에서 삭제된 디바이스를 더 이상 관리할 수 없습니다. 디바이스를 삭제하려면 다음 단계를 완료합니다.
   
   1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다. **디바이스** 블레이드에서 삭제하려는 비활성화된 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **삭제**를 클릭합니다.

       ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. **삭제** 블레이드에서 확인할 디바이스 이름을 입력하고 **삭제**를 클릭합니다. 삭제 작업을 완료하는 데는 몇 분이 소요됩니다.

       ![StorSimple 디바이스 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. 삭제가 완료되면 알림이 표시됩니다. 디바이스 목록도 삭제를 반영하도록 업데이트됩니다.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>클라우드 어플라이언스 비활성화 및 삭제

StorSimple Cloud Appliance를 포털에서 비활성화하면 가상 컴퓨터 및 프로비전할 때 만든 리소스의 할당이 취소되고 삭제됩니다. 클라우드 어플라이언스가 비활성화된 후 이전 상태로 복원할 수 없습니다.

![StorSimple Cloud Appliance 비활성화](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

비활성화하면 다음 작업이 진행됩니다.

* StorSimple Cloud Appliance가 서비스에서 제거됩니다.
* StorSimple Cloud Appliance의 가상 머신이 삭제됩니다.
* OS 디스크 및 StorSimple Cloud Appliance에 대해 만든 데이터 디스크가 유지됩니다. 이러한 엔터티를 사용하지 않는 경우 수동으로 삭제해야 합니다.
* 프로비전 중 만든 호스티드 서비스 및 Virtual Network는 유지됩니다. 이러한 엔터티를 사용하지 않는 경우 수동으로 삭제해야 합니다.
* StorSimple Cloud Appliance에서 만든 클라우드 스냅숏은 유지됩니다.

클라우드 어플라이언스가 비활성화되면 디바이스 목록에서 삭제할 수 있습니다. 비활성화된 디바이스를 선택하고 마우스 오른쪽 단추를 클릭한 다음, **삭제**를 클릭합니다. 디바이스가 삭제되면 StorSimple에서 알림을 표시하며 디바이스 목록도 업데이트됩니다.

## <a name="next-steps"></a>다음 단계

* 비활성화된 디바이스를 공장 기본 설정으로 복원하려면 [디바이스를 공장 기본 설정으로 초기화](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)로 이동합니다.
* 기술 지원을 받으려면 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)하세요.
* StorSimple 디바이스 관리자 서비스를 사용하는 방법을 자세히 알아보려면 [Use the StorSimple Device Manager service to administer your StorSimple device](storsimple-8000-manager-service-administration.md)(StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스 관리)로 이동하세요.

