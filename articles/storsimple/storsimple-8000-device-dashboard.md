---
title: StorSimple 8000 시리즈 디바이스 요약 사용 | Microsoft Docs
description: StorSimple 디바이스 관리자 서비스 디바이스 요약과 이를 사용하여 저장소 메트릭 및 연결된 초기자를 보고 일련 번호 및 IQN을 찾는 방법을 설명합니다.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578319"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>StorSimple 디바이스 관리자 서비스에서 디바이스 요약 사용

## <a name="overview"></a>개요
StorSimple 디바이스 요약 블레이드는 Microsoft Azure StorSimple 솔루션에 포함된 모든 디바이스에 대한 정보를 제공하는 서비스 요약 블레이드와 달리, 특정 StorSimple 디바이스에 대한 정보 개요를 제공합니다.

디바이스 요약 블레이드에서는 지정된 StorSimple 디바이스 관리자에 등록된 StorSimple 8000 시리즈 디바이스에 대한 요약 보기를 제공하며 시스템 관리자의 주의가 필요한 디바이스 문제를 강조 표시합니다. 이 자습서에서는 디바이스 요약 블레이드를 소개하고, 콘텐츠와 기능 및 이 브레이드에서 수행할 수 있는 태스크에 대해 설명합니다.

디바이스 요약 블레이드는 다음 정보를 표시합니다.

![디바이스 요약 블레이드](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>관리 명령 모음

StorSimple 디바이스 블레이드에는 StorSimple 디바이스를 관리하기 위한 옵션이 표시됩니다. 블레이드 상단과 왼쪽에는 관리 명령이 표시됩니다. 이러한 옵션을 사용하여 공유 또는 볼륨을 추가하거나 디바이스를 업데이트 또는 장애 조치(failover)합니다.

![관리 명령 모음](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>기본 정보

Essentials 영역은 상태, 모델, 대상 IQN 및 소프트웨어 버전과 같은 일부 중요한 속성을 캡처합니다. 

![디바이스 Essentials](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>모니터링

* **경고** 타일에서는 경고 심각도별로 그룹화된 디바이스에 대한 모든 활성 경고의 스냅숏을 제공합니다.

    ![경고 타일](./media/storsimple-8000-device-dashboard/device-summary4.png)

    타일을 클릭하면 **경고** 블레이드가 열리고 개별 경고를 클릭하여 권장 조치를 포함하여 해당 경고에 대한 추가 세부 정보를 볼 수 있습니다. 또한 문제가 해결된 경고를 지울 수도 있습니다.

    ![경고 타일 클릭](./media/storsimple-8000-device-dashboard/device-summary10.png)

* **상태** 타일에서는 디바이스 상태를 비롯하여 디바이스의 하드웨어 구성 요소 상태에 대한 정보를 제공합니다. 디바이스 상태는 오프라인, 온라인, 비활성화 또는 설정 준비 완료 상태일 수 있습니다.

    ![상태 타일](./media/storsimple-8000-device-dashboard/device-summary5.png)

* **볼륨** 타일에서는 상태별로 그룹화된, 디바이스의 볼륨 수에 대한 요약을 제공합니다.

    ![볼륨 타일](./media/storsimple-8000-device-dashboard/device-summary6.png)

    타일을 클릭하여 **볼륨** 목록 블레이드를 연 다음 개별 볼륨을 클릭하여 해당 속성을 보거나 수정합니다.
    
    ![볼륨 타일 클릭](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    자세한 내용은 [볼륨 관리](storsimple-8000-manage-volumes-u2.md) 방법을 참조하세요.

* **사용 현황** 차트에서는 기본 기간인 지난 7일 동안 사용된 클라우드 저장소와 디바이스에서 사용된 기본 저장소를 볼 수 있습니다.

     ![사용량 타일](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     다른 시간 단위를 선택하려면 차트의 오른쪽 위 모서리에서 **편집** 옵션을 사용하세요.

     ![사용 현황 차트 편집](./media/storsimple-8000-device-dashboard/device-summary12.png)

     이 차트에서 총 기본 저장소(디바이스에 대한 호스트가 기록한 데이터의 양)와 일정 기간 동안 디바이스가 사용한 총 클라우드 저장소에 대한 메트릭을 볼 수 있습니다.
  
     이 컨텍스트에서 *Tiered Storage*는 호스트가 기록한 데이터의 총 크기를 나타내며 볼륨 유형별로 세분화될 수 있습니다. *계층화된 Tiered Storage*는 로컬에 저장된 데이터 및 클라우드에 계층화된 데이터를 모두 포함합니다. *로컬로 고정된 기본 저장소*는 로컬로 저장된 데이터만 포함합니다. 반면에 *클라우드 저장소*는 클라우드에 계층화된 총 데이터를 측정합니다. 이 저장소에는 계층화된 데이터 및 백업이 포함됩니다. 클라우드에 저장된 데이터는 중복 제거되고 압축되는 반면 기본 저장소는 데이터가 중복 제거되고 압축되기 전에 사용된 저장소의 양을 나타냅니다. (압축 비율을 파악하도록 이러한 두 값을 비교할 수 있습니다.) 기본 및 클라우드 저장소의 경우 표시되는 양은 사용자가 구성하는 추적 빈도에 따라 달라집니다. 예를 들어, 1주일 빈도를 선택하면 이전 주의 각 날짜에 대한 데이터가 차트에 표시됩니다.

     시간이 지남에 따라 사용되는 클라우드 스토리지의 크기를 보려면 **CLOUD STORAGE USED(사용된 클라우드 스토리지)** 옵션을 선택합니다. 호스트가 작성한 총 스토리지를 보려면 **계층화되고 사용된 Tiered Storage** 및 **로컬로 고정된 사용된 Tiered Storage** 옵션을 선택합니다. 
     자세한 내용은 [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스 모니터링](storsimple-monitor-device.md)을 참조하세요.


* **용량** 타일에서는 동일한 경우에 사용 가능한 총 저장소를 기준으로 디바이스에 프로비전하고 남아있는 기본 저장소를 표시합니다. **프로비전**은 사용하도록 준비되고 할당된 저장소의 양을 나타내며 **나머지**는 이 디바이스에 프로비전될 수 있는 남은 용량을 가리킵니다. 

    ![사용량 타일](./media/storsimple-8000-device-dashboard/device-summary8.png)

    계층화된 볼륨 및 로컬로 고정된 볼륨에서 용량이 프로비전된 방식을 보려면 이 타일을 클릭합니다. **나머지 계층** 용량은 클라우드를 포함하여 프로비전될 수 있는 사용 가능한 용량인 반면 **나머지 로컬**은 이 디바이스에 연결된 디스크에 남아 있는 용량입니다.

    ![사용 현황 차트 클릭](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>다음 단계
* [StorSimple 서비스 요약 블레이드](storsimple-8000-service-dashboard.md)에 대해 자세히 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

