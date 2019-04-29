---
title: StorSimple 8000 시리즈 디바이스 요약 사용 | Microsoft Docs
description: StorSimple 서비스 요약 블레이드 및 이 블레이드를 사용하여 StorSimple 솔루션의 상태를 모니터링하는 방법을 설명합니다.
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
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60633143"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 디바이스에 대한 서비스 요약 블레이드 사용

## <a name="overview"></a>개요

StorSimple 디바이스 관리자 서비스 요약 블레이드는 StorSimple 디바이스 관리자 서비스에 연결된 모든 디바이스에 대한 요약 보기를 통해 시스템 관리자의 주의가 필요한 정보를 중점적으로 제공합니다. 이 자습서에서는 서비스 요약 블레이드를 소개하고, 대시보드 콘텐츠와 기능 및 이 페이지에서 수행할 수 있는 태스크에 대해 설명합니다.

![서비스 요약](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>관리 명령

StorSimple 서비스 요약 블레이드에는 StorSimple 디바이스 관리자 서비스 및 이 서비스에 등록된 StorSimple 8000 시리즈 디바이스를 관리하는 옵션이 표시됩니다. 블레이드 상단과 왼쪽에는 관리 명령이 표시됩니다.

![명령 모음](./media/storsimple-8000-service-dashboard/service-summary2.png)

이 옵션을 사용하여 공유 또는 볼륨을 추가하거나 StorSimple 디바이스에서 실행 중인 다양한 작업을 모니터링하는 것과 같은 다양한 작업을 수행합니다.


## <a name="essentials"></a>기본 정보

Essentials 영역은 StorSimple 디바이스 관리자를 만든 리소스 그룹, 위치 및 구독과 같은 중요한 속성 중 일부를 캡처합니다.

![기본 정보](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple 디바이스 관리자 서비스 요약

* **경고** 타일에서는 경고 심각도별로 그룹화된 모든 디바이스 간의 모든 활성 경고에 대한 스냅숏을 제공합니다.

    ![경고 타일](./media/storsimple-8000-service-dashboard/service-summary4.png)

    타일을 클릭하면 **경고** 블레이드가 열리고 여기에서 개별 경고를 클릭하여 권장 조치를 포함하여 해당 경고에 대한 추가적인 정보를 자세히 볼 수 있습니다. 또한 문제가 해결된 경고를 지울 수도 있습니다.

    ![경고 타일 클릭](./media/storsimple-8000-service-dashboard/service-summary8.png)

* **용량** 타일에서는 모든 디바이스에서 사용 가능한 총 저장소를 기준으로 모든 디바이스에 프로비전하고 남아있는 기본 저장소를 보여 줍니다. **프로비전**은 사용하도록 준비되고 할당된 저장소의 양을 나타내며 **나머지**는 모든 디바이스에 프로비전될 수 있는 남은 용량을 가리킵니다.

    ![용량 타일](./media/storsimple-8000-service-dashboard/service-summary6.png)

    **나머지 계층** 용량은 클라우드를 포함하여 프로비전될 수 있는 사용 가능한 용량인 반면 **나머지 로컬**은 StorSimple 8000 시리즈 디바이스에 연결된 디스크에 남아 있는 용량입니다.


* **사용량** 차트에서 디바이스에 대한 관련 메트릭을 볼 수 있습니다. 기본 시간 간격인 지난 7일 동안 디바이스에서 사용된 클라우드 저장소는 물론, 모든 디바이스에 걸쳐 사용되는 기본 저장소도 볼 수 있습니다. 

    ![사용량 타일](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    다른 시간 단위를 선택하려면 차트의 오른쪽 위 모서리에서 **편집** 옵션을 사용하세요.

     ![사용량 타일 클릭](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![차트 데이터 내보내기](./media/storsimple-8000-service-dashboard/service-summary11.png)

* **디바이스** 타일에서는 디바이스 상태별로 그룹화된 StorSimple 디바이스 관리자에서 StorSimple 8000 시리즈 디바이스 수의 요약을 제공합니다. 

    ![디바이스 타일](./media/storsimple-8000-service-dashboard/service-summary5.png)

    이 타일을 클릭하여 **디바이스** 목록 블레이드를 연 다음 개별 디바이스를 클릭하여 디바이스에 특정된 디바이스 요약을 살펴봅니다. 또한 지정된 디바이스 요약 블레이드에서 디바이스 특정 작업을 수행할 수 있습니다. 디바이스 요약 블레이드에 대한 자세한 내용은 [디바이스 요약 블레이드](storsimple-8000-device-dashboard.md)로 이동합니다.

    ![디바이스 타일 클릭](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>작업 로그 보기

StorSimple 디바이스 관리자 내에서 수행된 다양한 작업을 보려면 StorSimple 서비스 요약 블레이드의 왼쪽에 있는 **작업 로그** 링크를 클릭합니다. 그러면 **작업 로그** 블레이드로 이동하여 최근에 수행한 작업의 요약을 볼 수 있습니다.

![활동 로그](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>다음 단계

* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-8000-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

