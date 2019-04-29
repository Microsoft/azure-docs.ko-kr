---
title: 8000 시리즈 디바이스에 대한 StorSimple 장애 조치(failover), 재해 복구 | Microsoft Docs
description: 자체적으로, 다른 실제 디바이스 또는 클라우드 어플라이언스로 StorSimple 디바이스의 장애를 조치하는 방법을 알아봅니다.
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
ms.openlocfilehash: 079a2f153f257040d1899a33c9e255d633e526ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576375"
---
# <a name="failover-and-disaster-recovery-for-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 디바이스에 대한 장애 조치 및 재해 복구

## <a name="overview"></a>개요

이 문서에서는 StorSimple 8000 시리즈 디바이스에 대한 디바이스 장애 조치 기능 및 재해가 발생할 경우 이 기능을 사용하여 StorSimple 디바이스를 복구하는 방법을 설명합니다. StorSimple에서는 디바이스 장애 조치(failover)를 사용하여 데이터 센터의 원본 디바이스에서 다른 대상 디바이스로 데이터를 마이그레이션합니다. 이 문서의 지침은 소프트웨어 버전 업데이트 3 이상을 실행하는 StorSimple 8000 시리즈 물리적 디바이스 및 클라우드 어플라이언스에 적용됩니다.

StorSimple에서는 **디바이스** 블레이드를 사용하여 재해 발생 시 디바이스 장애 조치 기능을 시작합니다. 이 블레이드는 StorSimple 디바이스 관리자 서비스에 연결된 모든 StorSimple 디바이스를 나열합니다.

![디바이스 블레이드](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)


## <a name="disaster-recovery-dr-and-device-failover"></a>DR(재해 복구) 및 디바이스 장애 조치

DR(재해 복구) 시나리오에서 기본 디바이스가 작동을 중지합니다. StorSimple에서는 기본 디바이스를 _소스_로 사용하여 다른 _대상_ 디바이스에 연결된 클라우드 데이터를 이동시킵니다. 이 프로세스는 *장애 조치*라고 합니다. 다음 그래픽에서는 장애 조치(failover)의 프로세스를 보여줍니다.

![디바이스 장애 조치는 어떻게 되나요?](./media/storsimple-8000-device-failover-disaster-recovery/failover-dr-flow.png)

장애 조치의 대상 디바이스는 물리적 디바이스 또는 클라우드 어플라이언스일 수 있습니다. 대상 디바이스는 원본 디바이스와 동일하거나 다른 지리적 위치에 있을 수 있습니다.

장애 조치하는 동안 마이그레이션할 볼륨 컨테이너를 선택할 수 있습니다. 그런 다음 StorSimple에서는 이러한 볼륨 컨테이너의 소유권을 원본 디바이스에서 대상 디바이스로 변경합니다. 볼륨 컨테이너가 소유권을 변경하면 StorSimple에서는 원본 디바이스에서 이러한 컨테이너를 삭제합니다. 삭제가 완료된 후에 대상 디바이스를 장애 복구(failback)할 수 있습니다. _장애 복구_는 원래의 원본 디바이스에 다시 소유권을 전송합니다.

### <a name="cloud-snapshot-used-during-device-failover"></a>디바이스 장애 조치 동안 사용된 클라우드 스냅숏

DR 다음으로 가장 최근의 클라우드 백업은 대상 디바이스에 데이터를 복원하는 데 사용됩니다. 클라우드 스냅숏에 대한 자세한 내용은 [StorSimple 디바이스 관리자 서비스를 사용하여 수동 백업](storsimple-8000-manage-backup-policies-u2.md#take-a-manual-backup)을 참조하세요.

StorSimple 8000 시리즈에서 백업 정책을 백업에 연결합니다. 동일한 볼륨에 여러 백업 정책이 있는 경우 StorSimple에서는 최대 수의 볼륨을 가진 백업 정책을 선택합니다. StorSimple에서는 선택된 백업 정책에서 가장 최근의 백업을 사용하여 대상 디바이스에서 데이터를 복원합니다.

*defaultPol* 및 *customPol*이라는 두 개의 백업 정책이 있는 경우를 가정합니다.

* *defaultPol*: 하나의 볼륨 *vol1*, 매일 오후 10 시 30 분부터 실행 됩니다.
* *customPol*: 4 개의 볼륨 *vol1*, *vol2*를 *vol3*를 *vol4*, 매일 오후 10 시부터 실행 됩니다.

이 경우에 StorSimple에서는 더 많은 볼륨을 포함하는 크래시 일관성에 대한 우선 순위를 지정하고 *customPol*을 사용합니다. 이 정책에서 가장 최근의 백업은 데이터를 복원하는 데 사용됩니다. 백업 정책을 만들고 관리하는 방법에 대한 자세한 내용은 [StorSimple 디바이스 관리자 서비스를 사용하여 백업 정책 관리](storsimple-8000-manage-backup-policies-u2.md)로 이동하세요.

## <a name="common-considerations-for-device-failover"></a>디바이스 장애 조치에 대한 일반 고려 사항

디바이스를 장애 조치하기 전에 다음 정보를 검토합니다.

* 디바이스 장애 조치를 시작하기 전에 볼륨 컨테이너 내의 모든 볼륨은 오프라인이어야 합니다. 계획되지 않은 장애 조치에서 StotSimple 볼륨은 자동으로 오프라인 상태가 됩니다. 하지만 계획된 장애 조치를 수행하는 경우(DR을 테스트) 모든 볼륨을 오프라인으로 수행해야 합니다.
* 연결되어 있는 클라우드 스냅숏이 있는 볼륨 컨테이너만이 DR에 나열됩니다. 데이터를 복구하려면 연결된 클라우드 스냅숏이 있는 하나 이상의 볼륨 컨테이너가 있어야 합니다.
* 클라우드 스냅숏이 여러 볼륨 컨테이너에 걸쳐 있는 경우 StorSimple은 이러한 볼륨 컨테이너를 집합으로 장애 조치합니다. 가끔 로컬 스냅숏이 여러 볼륨 컨테이너에 걸쳐 있지만 연결된 클라우드 스냅숏이 그렇지 않은 경우 StorSimple이 로컬 스냅숏을 장애 조치하고 DR 이후의 로컬 데이터가 손실됩니다.
* DR에 사용 가능한 대상 디바이스는 선택한 볼륨 컨테이너를 수용하기에 충분한 공간이 있는 디바이스입니다. 충분한 공간이 없는 디바이스는 대상 디바이스로 나열되지 않습니다.
* 디바이스는 클라우드에서 데이터에 액세스한 후 로컬로 저장해야 하므로, DR 이후에(제한된 기간 동안) 데이터 액세스 성능에 상당한 영향을 받을 수 있습니다.

#### <a name="device-failover-across-software-versions"></a>여러 소프트웨어 버전 간의 디바이스 장애 조치(Failover)

배포의 StorSimple 디바이스 관리자 서비스에는 다른 소프트웨어 버전을 실행하는 여러 물리적 디바이스 및 클라우드 디바이스가 있을 수 있습니다.

다음 표를 사용하여 다른 소프트웨어 버전을 실행하는 다른 디바이스로 장애 조치(failover)하거나 장애 복구할 수 있는지 여부 및 DR 중에 볼륨 형식이 동작하는 방법을 결정합니다.

#### <a name="failover-and-failback-across-software-versions"></a>소프트웨어 버전 간에 장애 조치(failover) 및 장애 복구(failback)

| 장애 조치(Failover)/장애 복구(failback) 원본 | 물리적 디바이스 | 클라우드 어플라이언스 |
| --- | --- | --- |
| 업데이트 3~업데이트 4 |계층화된 볼륨은 계층화된 볼륨으로 장애 조치됩니다. <br></br>로컬 고정 볼륨은 로컬 고정 볼륨으로 장애 조치됩니다. <br></br> 업데이트 4 디바이스에서 스냅숏을 만드는 경우 장애 조치 이후에 [열 지도 기반 추적](storsimple-update4-release-notes.md#whats-new-in-update-4)이 시작됩니다. |로컬 고정 볼륨은 계층화된 볼륨으로 장애 조치됩니다. |
| 업데이트 4~업데이트 3 |계층화된 볼륨은 계층화된 볼륨으로 장애 조치됩니다. <br></br>로컬 고정 볼륨은 로컬 고정 볼륨으로 장애 조치됩니다. <br></br> 복원하는 데 사용되는 Backup은 heatmap 메타데이터를 유지합니다. <br></br>heatmap 기반 추적은 장애 복구를 수행하는 업데이트 3에서 사용할 수 없습니다. |로컬 고정 볼륨은 계층화된 볼륨으로 장애 조치됩니다. |


## <a name="device-failover-scenarios"></a>디바이스 장애 조치 시나리오

재해가 발생할 경우 다음과 같이 StorSimple 디바이스에 장애 조치를 선택할 수 있습니다.

* [실제 디바이스로](storsimple-8000-device-failover-physical-device.md)
* [자체적으로](storsimple-8000-device-failover-same-device.md)
* [클라우드 어플라이언스로](storsimple-8000-device-failover-cloud-appliance.md)

이전의 문서는 위의 장애 조치 사례 각각에 대한 자세한 단계를 제공합니다.


## <a name="failback"></a>장애 복구

업데이트 3 이상 버전의 경우 StorSimple도 장애 복구를 지원합니다. 장애 복구는 장애 조치와 반대로 대상이 원본이 되고, 이제 장애 조치 중에 원래 원본 디바이스는 대상 디바이스가 됩니다. 

장애 복구 중에 StorSimple은 기본 위치에 데이터를 다시 동기화하고, I/O 및 애플리케이션 작업을 중단하고, 원래 위치로 다시 전환합니다.

장애 조치가 완료되면 StorSimple은 다음 작업을 수행합니다.

* StorSimple은 장애 조치되는 볼륨 컨테이너를 원본 디바이스에서 정리합니다.
* StorSimple은 볼륨 컨테이너당 백그라운드 작업(장애 조치됨)를 원본 디바이스에서 시작합니다. 작업이 진행 중인 동안 장애 복구를 시도하면 결과에 대한 알림이 수신됩니다. 장애 복구를 시작하려면 해당 작업이 완료될 때까지 기다립니다.
* 볼륨 컨테이너의 삭제를 완료하는 데 걸리는 시간은 데이터의 양, 데이터의 사용 기간, 백업 수 및 작업에 사용 가능한 네트워크 대역폭 등과 같은 다양한 요소에 따라 달라집니다.

테스트 장애 조치 또는 테스트 장애 복구를 계획 중인 경우 적은 양의 데이터(Gbs)가 있는 볼륨 컨테이너를 테스트하는 것이 좋습니다. 일반적으로 장애 조치가 완료되고 24시간 후에 장애 복구를 시작할 수 있습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

Q. **DR이 실패하거나 부분적으로 성공한 경우 어떻게 되나요?**

a. DR이 실패하면 다시 시도하는 것이 좋습니다. 두 번째 디바이스 장애 조치 작업은 첫 번째 작업의 진행률을 인식하고 해당 시점부터 시작됩니다.

Q. **디바이스 장애 조치를 진행 중인 동안에 디바이스를 삭제할 수 있나요?**

a. DR을 진행 중인 동안에는 디바이스를 삭제할 수 없습니다. DR이 완료된 후에만 디바이스를 삭제할 수 있습니다. **작업** 블레이드에서 디바이스 장애 조치 작업 진행률을 모니터링할 수 있습니다.

Q. **원본 디바이스에 있는 로컬 데이터를 삭제하도록 원본 디바이스에서 언제 가비지 수집이 시작되나요?**

a. 가비지 수집은 디바이스가 완전히 정리된 후에만 원본 디바이스에서 사용할 수 있습니다. 정리에는 볼륨, 백업 개체(데이터 아님), 볼륨 컨테이너 및 정책 등 원본 디바이스에서 장애 조치한 개체를 정리하는 것이 포함됩니다.

Q. **원본 디바이스에서 볼륨 컨테이너와 연결된 삭제 작업이 실패하면 어떻게 되나요?**

a.  삭제 작업에 실패하면 볼륨 컨테이너를 수동으로 삭제할 수 있습니다. **디바이스** 블레이드에서 원본 디바이스를 선택하고 **볼륨 컨테이너**를 클릭합니다. 장애 조치한 볼륨 컨테이너를 선택하고 블레이드 하단에서 **삭제**를 클릭합니다. 원본 디바이스에서 장애 조치한 모든 볼륨 컨테이너를 삭제한 후에 장애 복구를 시작할 수 있습니다. 자세한 내용은 [볼륨 컨테이너 삭제](storsimple-8000-manage-volume-containers.md#delete-a-volume-container)로 이동합니다.

## <a name="business-continuity-disaster-recovery-bcdr"></a>비즈니스 연속성 재해 복구(BCDR)

비즈니스 연속성 재해 복구(BCDR) 시나리오는 전체 Azure 데이터 센터의 작동이 중지되는 경우 발생합니다. 이 시나리오는 StorSimple 디바이스 관리자 서비스 및 연결된 StorSimple 디바이스에 영향을 줄 수 있습니다.

재해가 발생하기 직전에 StorSimple 디바이스가 등록되면 이러한 디바이스는 공장 재설정을 거쳐야 합니다. 재해가 발생한 후에 Azure Portal에서 StorSimple 디바이스가 오프라인으로 표시됩니다. 포털에서 이 디바이스를 삭제해야 합니다. 디바이스를 공장 기본값으로 다시 설정하고 서비스에서 다시 등록합니다.

## <a name="next-steps"></a>다음 단계

디바이스 장애 조치를 수행할 준비가 되면 자세한 지침은 다음 시나리오 중 하나를 선택합니다.

- [다른 실제 디바이스로 장애 조치](storsimple-8000-device-failover-physical-device.md)
- [동일한 디바이스로 장애 조치](storsimple-8000-device-failover-same-device.md)
- [StorSimple Cloud Appliance로 장애 조치](storsimple-8000-device-failover-cloud-appliance.md)

디바이스를 장애 조치한 경우 다음 옵션 중 하나를 선택합니다.

* [StorSimple 디바이스 비활성화 및 삭제](storsimple-8000-deactivate-and-delete-device.md)
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스 관리](storsimple-8000-manager-service-administration.md)

