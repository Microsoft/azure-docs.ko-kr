---
title: StorSimple 8000 시리즈 업데이트 5 릴리스 정보 | Microsoft Docs
description: StorSimple 8000 시리즈 업데이트 5의 새로운 기능, 문제 및 해결 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: 462cbd6261723aa91bbfd23292611e758a800ed2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844094"
---
# <a name="storsimple-8000-series-update-5-release-notes"></a>StorSimple 8000 시리즈 업데이트 5 릴리스 정보

## <a name="overview"></a>개요

다음 릴리스 정보는 새로운 기능에 대해 설명하고 StorSimple 8000 시리즈 업데이트 5에 대한 중요한 미해결 문제를 식별합니다. 또한 이 릴리스에 포함된 StorSimple 소프트웨어 업데이트 목록을 포함합니다.

업데이트 5는 업데이트 0.1~업데이트 4를 실행하는 모든 StorSimple 디바이스에 적용할 수 있습니다. 업데이트 5에 연결된 디바이스 버전은 6.3.9600.17845입니다.

StorSimple 솔루션에 업데이트를 배포하기 전에 릴리스 정보에 포함된 정보를 검토하세요.

> [!IMPORTANT]
> * 업데이트 5는 필수 업데이트로, 즉시 설치해야 합니다. 자세한 내용은 [업데이트 5 적용](storsimple-8000-install-update-5.md) 방법을 참조하세요.
> * 업데이트 5에는 디바이스 소프트웨어, 디스크 펌웨어, OS 보안 및 기타 OS 업데이트가 있습니다. 이 업데이트를 설치하는 데는 4시간 정도 걸립니다. 디스크 펌웨어 업데이트는 중단 업데이트이며 사용자 디바이스에 대한 가동 중지 시간이 발생합니다. 디바이스를 최신 상태로 유지하려면 업데이트 5를 적용하는 것이 좋습니다.
> * 새 릴리스의 경우, 업데이트의 단계적 롤아웃을 수행하기 때문에 즉시 업데이트를 볼 수는 없습니다. 이러한 업데이트가 곧 제공될 예정이니 몇 일 후에 업데이트를 다시 검색하세요.

## <a name="whats-new-in-update-5"></a>업데이트 5의 새로운 기능

업데이트 5에는 다음과 같은 주요 향상 기능 및 버그 수정이 포함되어 있습니다.

* **AAD(Azure Active Directory)를 사용하여 StorSimple 디바이스 관리자 서비스에서 인증 받기** – 업데이트 5부터 StorSimple 디바이스 관리자 서비스에서 인증을 받는 데 Azure Active Directory가 사용됩니다. 기존 인증 메커니즘은 2017년 12월부터 지원이 중지될 예정입니다. 모든 사용자는 방화벽 규칙에 새 인증 URL을 포함해야 합니다. 자세한 내용은 [StorSimple 디바이스에 대한 네트워킹 요구 사항에 나열된 인증 URL](storsimple-8000-system-requirements.md#url-patterns-for-azure-portal)로 이동하세요.

    인증 URL이 방화벽 규칙에 포함되지 않은 경우 StorSimple 디바이스가 서비스에서 인증을 받을 수 없다는 중요한 알림이 표시됩니다. 이 경고가 표시되면 새 인증 URL을 포함해야 합니다. 자세한 내용은 [StorSimple 네트워킹 경고](storsimple-8000-manage-alerts.md#networking-alerts)로 이동합니다.

* **새 버전의 StorSimple Snapshot Manager** - 새 버전의 StorSimple Snapshot Manager는 업데이트 5에서 제공되고 업데이트 4 이상을 실행하는 모든 StorSimple 디바이스와 호환됩니다. 이 버전으로 업데이트하는 것이 좋습니다. 이전 버전의 StorSimple Snapshot Manager는 업데이트 3 이전을 실행하는 StorSimple 디바이스에 사용됩니다. [StorSimple Snapshot Manager의 해당 버전을 다운로드](https://www.microsoft.com/en-us/download/details.aspx?id=44220)하고 [StorSimple Snapshot Manager 배포](storsimple-snapshot-manager-deployment.md)를 참조합니다.


## <a name="issues-fixed-in-update-5"></a>업데이트 5에서 해결된 문제

다음 표에는 업데이트 5에서 해결된 문제가 요약되어 있습니다.

| 아닙니다. | 기능 | 문제 | 실제 디바이스에 적용 | 가상 디바이스에 적용 |
| --- | --- | --- | --- | --- |
| 1 |Windows PowerShell 원격 기능 |이전 릴리스에서는 Windows PowerShell을 통해 StorSimple Cloud Appliance에 대해 원격 연결을 설정하는 동안 오류가 발생합니다. 이 문제는 근본 원인이 파악되었고 이 릴리스에서 수정되었습니다. |아닙니다. |예 |
| 2 |대역폭 템플릿 |이전 릴리스에서는 대역폭 템플릿에 문제가 있어서 디바이스가 구성된 것보다 더 낮은 대역폭을 발생했습니다. 이 문제는 이 릴리스에서 해결되었습니다. |예 |예 |
| 3 |장애 조치(failover) |이전 릴리스에서는 대량의 볼륨이 있는 디바이스가 업데이트 4를 실행하는 다른 디바이스로 장애 조치(failover)되면 액세스 제어 레코드를 적용하려고 할 때 프로세스가 실패합니다. 이 문제는 이 릴리스에서 해결되었습니다. |예 |예 |



## <a name="known-issues-in-update-5-from-previous-releases"></a>업데이트 5의 알려진 이전 릴리스 문제

업데이트 5에 알려진 새로운 문제는 없습니다. 이전 릴리스에서 업데이트 5로 이어지는 문제 목록은 [업데이트 3 릴리스 정보](storsimple-update3-release-notes.md#known-issues-in-update-3)로 이동합니다.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-5"></a>업데이트 5의 SAS(Serial attached SCSI) 컨트롤러 및 펌웨어 업데이트

이 릴리스에는 SAS 컨트롤러, LSI 드라이버 및 펌웨어 업데이트가 있습니다. 이러한 업데이트를 설치하는 방법에 대한 자세한 내용은 StorSimple 디바이스의 [업데이트 5 설치](storsimple-8000-install-update-5.md)를 참조하세요.

## <a name="storsimple-cloud-appliance-updates-in-update-5"></a>업데이트 5의 StorSimple Cloud Appliance 업데이트

이 업데이트는 StorSimple 클라우드 어플라이언스에 적용할 수 없습니다(가상 디바이스라고도 함). 새 클라우드 어플라이언스는 업데이트 5 이미지를 사용하여 만들어야 합니다. StorSimple Cloud Appliance를 만드는 방법에 대한 자세한 내용은 [StorSimple Cloud Appliance 배포 및 관리](storsimple-8000-cloud-appliance-u2.md)로 이동합니다.

## <a name="next-step"></a>다음 단계

StorSimple 디바이스에 [업데이트 5를 설치](storsimple-8000-install-update-5.md)하는 방법을 알아봅니다.

