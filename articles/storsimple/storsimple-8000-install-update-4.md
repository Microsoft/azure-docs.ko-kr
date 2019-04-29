---
title: StorSimple 8000 시리즈 디바이스에 업데이트 4 설치 | Microsoft Docs
description: StorSimple 8000 시리즈 디바이스에서 StorSimple 8000 시리즈 업데이트 4를 설치하는 방법에 대해 설명합니다.
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
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860511"
---
# <a name="install-update-4-on-your-storsimple-device"></a>StorSimple 디바이스에 업데이트 4 설치

## <a name="overview"></a>개요

이 자습서에서는 Azure Portal을 통해서와 핫픽스 방법을 사용하여 이전 소프트웨어 버전을 실행하는 StorSimple 디바이스에 업데이트 4를 설치하는 방법에 대해 설명합니다. 핫픽스 방법은 StorSimple 디바이스의 DATA 0 이외의 네트워크 인터페이스에 게이트웨이가 구성되어 있고 업데이트 1 이전 소프트웨어 버전에서 업데이트하려는 경우에 사용됩니다.

업데이트 4는 디바이스 소프트웨어, USM 펌웨어, LSI 드라이버 및 펌웨어, Storport 및 Spaceport, OS 보안 업데이트 및 기타 OS 업데이트의 호스트를 포함합니다.  디바이스 소프트웨어, USM 펌웨어, Spaceport, Storport 및 기타 OS 업데이트는 무중단 업데이트입니다. Azure Portal을 통해 또는 핫픽스 메서드를 통해 무중단 또는 정기적인 업데이트를 적용할 수 있습니다. 디스크 펌웨어 업데이트는 작업 중단 업데이트이며 디바이스의 Windows PowerShell 인터페이스를 사용하여 핫픽스 메서드를 통해서만 적용할 수 있습니다.

> [!IMPORTANT]
> * 설치하기 전에 일련의 수동 및 자동 전 검사를 수행하며 하드웨어 상태와 네트워크 연결 측면에서 디바이스 상태를 확인합니다. Azure Portal에서 업데이트를 적용하는 경우 이러한 사전 검사를 수행합니다.
> * Azure Portal을 통해 소프트웨어 및 기타 정기적인 업데이트를 설치하는 것이 좋습니다. 포털에서 사전 업데이트 게이트웨이 검사가 실패한 경우 (업데이트를 설치하려면) 디바이스의 Windows PowerShell 인터페이스로 이동해야 합니다. 업데이트하는 버전에 따라 업데이트 설치에 4시간(이상)이 걸릴 수 있습니다. 디바이스의 Windows PowerShell 인터페이스를 통해 유지 관리 모드 업데이트도 설치해야 합니다. 유지 관리 모드 업데이트는 중단 업데이트입니다. 이는 디바이스에 중단 시간을 발생시킵니다.
> * 선택적 StorSimple Snapshot Manager를 실행하는 경우 디바이스를 업데이트하기 전에 Snapshot Manager 버전을 업데이트 4로 업그레이드했는지 확인합니다.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Azure Portal을 통해 업데이트 4 설치
다음 단계를 수행하여 디바이스를 [업데이트 4](storsimple-update4-release-notes.md)로 업데이트합니다.

> [!NOTE]
> Microsoft는 디바이스로부터 추가적인 진단 정보를 확보할 수 있습니다. 따라서 운영 팀에서 문제가 있는 디바이스를 확인하는 경우, 디바이스로부터 정보를 수집하고 문제를 진단할 준비가 더욱 잘 갖추어져 있습니다. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

디바이스가 **StorSimple 8000 시리즈 업데이트 4(6.3.9600.17820)** 를 실행하고 있는지 확인합니다. **마지막 업데이트 날짜** 도 수정해야 합니다.

* 이제 유지 관리 모드 업데이트가 사용 가능하다고 표시됩니다(이 메시지는 업데이트를 설치한 후 최대 24시간 동안 계속 표시될 수 있음). 유지 관리 모드 업데이트는 작업 중단 업데이트이므로 디바이스 가동 중지 시간이 발생할 수 있으며, 디바이스의 Windows PowerShell 인터페이스를 통해서만 적용할 수 있습니다.

* [핫픽스를 다운로드하려면](#to-download-hotfixes)에 나열된 단계를 사용하여 유지 관리 모드 업데이트를 다운로드한 후 KB4011837을 검색한 후 다운로드합니다. 이 KB는 디스크 펌웨어 업데이트를 설치합니다(다른 업데이트가 이미 설치되어 있어야 함). [유지 관리 모드 핫픽스 설치 및 확인](#to-install-and-verify-maintenance-mode-hotfixes) 에 나열된 단계를 따라 유지 관리 모드 업데이트를 설치합니다.

## <a name="install-update-4-as-a-hotfix"></a>핫픽스로 업데이트 4 설치
Azure Portal을 통해 업데이트 4를 설치하는 것이 좋습니다.

Azure Portal을 통해 업데이트를 설치하려고 할 때 게이트웨이 검사에 실패하는 경우 이 절차를 따릅니다. 비데이터 0 네트워크 인터페이스에 할당된 게이트웨이가 있고 디바이스가 업데이트 1 이전의 소프트웨어 버전을 실행하는 경우 확인이 실패합니다.

핫픽스 방법을 사용하여 업그레이드할 수 있는 소프트웨어 버전은 다음과 같습니다.

* 업데이트 0.1, 0.2, 0.3
* 업데이트 1, 1.1, 1.2
* 업데이트 2, 2.1, 2.2
* 업데이트 3, 3.1


핫픽스 메서드에는 다음 세 단계가 포함됩니다.

1. Microsoft Update 카탈로그에서 핫픽스를 다운로드합니다.
2. 일반 모드 핫픽스를 설치 및 확인합니다.
3. 유지 관리 모드 핫픽스를 설치 및 확인합니다.

#### <a name="download-updates-for-your-device"></a>디바이스에 대한 업데이트 다운로드

다음 핫픽스를 사전 설정된 순서대로 제안된 폴더에 다운로드하고 설치해야 합니다.

| 순서 | KB | 설명 | 업데이트 유형 | 설치 시간 |폴더에 설치|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |소프트웨어 업데이트 |일반  <br></br>중단 없음 |~ 25분 |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI 드라이버 및 펌웨어 업데이트 <br> USM 펌웨어 업데이트(버전 3.38) |일반  <br></br>중단 없음 |~ 3시간 <br> (2A. + 2B. + 2C 포함)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |OS 보안 업데이트 패키지 <br> Windows Server 2012 R2 다운로드 |일반  <br></br>중단 없음 |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |OS 업데이트 패키지 <br> Windows Server 2012 R2 다운로드 |일반  <br></br>중단 없음 |- |SecondOrderUpdate|

앞의 표에 나와 있는 모든 업데이트 맨 위에 디스크 펌웨어 업데이트를 설치해야 할 수도 있습니다. `Get-HcsFirmwareVersion` cmdlet을 실행하여 디스크 펌웨어 업데이트가 필요한지를 확인할 수 있습니다. 펌웨어 버전 `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002` ,`0106`을 실행하는 경우 이러한 업데이트를 설치할 필요가 없습니다.

| 순서 | KB | 설명 | 업데이트 유형 | 설치 시간 | 폴더에 설치|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |디스크 펌웨어 |유지 관리 <br></br>중단 |~ 30분 | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * 이 절차는 업데이트 4에만 적용하도록 한 번만 수행해야 됩니다. Azure Portal을 사용하여 후속 업데이트를 적용할 수 있습니다.
> * 업데이트 3 또는 3.1에서 업데이트하는 경우 전체 설치 시간은 4시간에 가깝습니다.
> * 이 절차에 따라 업데이트를 적용하기 전에 두 디바이스 컨트롤러가 온라인 상태이고 모든 하드웨어 구성 요소가 정상 상태인지 확인합니다.

다음 단계에 따라 핫픽스를 다운로드하고 설치합니다.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>다음 단계
[업데이트 4 릴리스](storsimple-update4-release-notes.md)에 대해 자세히 알아봅니다.

