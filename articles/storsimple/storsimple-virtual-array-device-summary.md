---
title: StorSimple Virtual Array 디바이스 요약 블레이드 | Microsoft Docs
description: StorSimple 디바이스 관리자에 대한 디바이스 요약 블레이드 및 이 기능을 사용하여 StorSimple 가상 배열의 상태를 모니터링하는 방법을 설명합니다.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61408509"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>StorSimple 가상 배열에 연결된 StorSimple 디바이스 관리자에 대한 디바이스 요약 블레이드 사용

## <a name="overview"></a>개요

StorSimple 디바이스 관리자 디바이스 블레이드에서는 지정된 StorSimple 디바이스 관리자에 등록된 StorSimple 가상 배열의 요약 보기를 제공하며 시스템 관리자의 주의가 필요한 해당 디바이스 문제를 강조 표시합니다. 이 자습서에서는 디바이스 요약 블레이드를 소개하고, 콘텐츠와 기능 및 이 브레이드에서 수행할 수 있는 태스크에 대해 설명합니다.

디바이스 요약 블레이드는 다음 정보를 표시합니다.

![디바이스 대시보드](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>관리

StorSimple 디바이스 블레이드에는 StorSimple 디바이스를 관리하기 위한 옵션이 표시됩니다. 블레이드 상단과 왼쪽에는 관리 명령이 표시됩니다. 이러한 옵션을 사용하여 공유 또는 볼륨을 추가하거나 가상 배열을 업데이트 또는 장애 조치합니다.

Essentials 영역은 상태, 모델, 소프트웨어 버전뿐만 아니라 배열 **웹 UI**에 대한 링크와 같은 일부 중요한 속성을 캡처합니다. 사용자가 내부 네트워크에 있는 경우 [로컬 웹 UI](storsimple-ova-web-ui-admin.md)를 직접 시작하여 가상 배열을 관리할 수 있습니다.

![디바이스 Essentials](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple 디바이스 요약

* **경고** 타일에서는 경고 심각도별로 그룹화된 가상 배열에 대한 모든 활성 경고의 스냅숏을 제공합니다. 타일을 클릭하면 **경고** 블레이드가 열리고 개별 경고를 클릭하여 권장 조치를 포함하여 해당 경고에 대한 추가 세부 정보를 볼 수 있습니다. 또한 문제가 해결된 경고를 지울 수도 있습니다.

* **용량** 타일에서는 동일한 경우에 사용 가능한 총 저장소를 기준으로 모든 가상 디바이스에 프로비전하고 남아있는 기본 저장소를 표시합니다. **프로비전**은 사용하도록 준비되고 할당된 저장소의 양을 나타내며 **나머지**는 이 디바이스에 프로비전될 수 있는 남은 용량을 가리킵니다. **나머지 계층** 용량은 클라우드를 포함하여 프로비전될 수 있는 사용 가능한 용량인 반면 **나머지 로컬**은 이 가상 배열에 연결된 디스크에 남아 있는 용량입니다.

* **사용** 차트에서 기본 시간 간격인 지난 7일 동안 사용된 클라우드 저장소뿐만 아니라 가상 배열에 사용되는 기본 저장소도 볼 수 있습니다. 차트의 오른쪽 위 모서리에서 **편집** 옵션을 사용하여 다른 시간 단위를 선택합니다.

* **공유** 또는 **볼륨** 타일에서는 상태별로 그룹화된 디바이스에서 공유 또는 볼륨의 수를 간략하게 설명합니다. 타일을 클릭하여 **공유** 또는 **볼륨** 목록 블레이드를 연 다음 개별 공유 또는 볼륨을 클릭하여 해당 속성을 확인하거나 수정합니다. 자세한 내용은 [공유 관리](storsimple-virtual-array-manage-shares.md) 또는 [볼륨 관리](storsimple-virtual-array-manage-volumes.md) 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계
방법 배우기:
- [StorSimple 가상 배열에서 공유 관리](storsimple-virtual-array-manage-shares.md)
    
- [StorSimple 가상 배열에서 볼륨 관리](storsimple-virtual-array-manage-volumes.md)

