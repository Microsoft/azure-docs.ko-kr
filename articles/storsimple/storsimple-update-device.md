---
title: StorSimple 디바이스 업데이트 | Microsoft Docs
description: StorSimple 업데이트 기능을 사용하여 일반 및 유지 관리 모드 업데이트 및 핫픽스를 설치하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: d973a16c121a1e8ebee10826d135bcbb33ef748c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61409990"
---
# <a name="update-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 디바이스 업데이트
> [!NOTE]
> StorSimple의 클래식 포털은 사용되지 않습니다. StorSimple 디바이스 관리자는 사용 중단 일정에 따라 자동으로 새 Azure Portal로 이동합니다. 이 이동에 대한 메일 및 포털 알림을 받게 됩니다. 이 문서도 곧 사용 중지됩니다. 이동에 대 한 질문이 참조 [FAQ: Azure portal로 이동](storsimple-8000-move-azure-portal-faq.md)합니다.

## <a name="overview"></a>개요
StorSimple 업데이트 기능을 사용하면 쉽게 StorSimple 디바이스를 최신 상태로 유지할 수 있습니다. 업데이트 유형에 따라 Windows PowerShell 인터페이스 또는 Azure 클래식 포털을 통해 디바이스에 업데이트를 적용할 수 있습니다. 이 자습서에는 업데이트 유형 및 각 항목을 설치하는 방법을 설명합니다.

두 종류의 디바이스 업데이트를 적용할 수 있습니다. 

* 일반(또는 표준 모드) 업데이트
* 유지 관리 모드 업데이트

Azure 클래식 포털 또는 Windows PowerShell을 통해 정기적으로 업데이트를 설치할 수 있습니다. 그러나 Windows PowerShell을 사용하여 유지 관리 모드 업데이트를 설치해야 합니다. 

각 업데이트 유형은 아래에 별도로 설명됩니다.

### <a name="regular-updates"></a>일반 업데이트
일반 업데이트는 디바이스가 표준 모드에 있을 때 설치할 수 있는 무중단 업데이트입니다. 이 업데이트는 Microsoft Update 웹사이트를 통해 각 디바이스 컨트롤러에 적용됩니다. 

> [!IMPORTANT]
> 컨트롤러 장애 조치는 업데이트 과정에서 발생할 수 있습니다. 그러나 시스템 가용성 또는 작업에 영향을 주지 않습니다.
> 
> 

* Azure 클래식 포털을 통해 일반 업데이트를 설치하는 방법에 대한 자세한 내용은 [Azure 클래식 포털을 통해 일반 업데이트 설치](#install-regular-updates-via-the-azure-classic-portal)를 참조하세요.
* StorSimple용 Windows PowerShell을 통해 일반 업데이트를 설치할 수도 있습니다. 자세한 내용은 [StorSimple용 Windows PowerShell을 통해 일반 업데이트 설치](#install-regular-updates-via-windows-powershell-for-storsimple)를 참조하세요.

### <a name="maintenance-mode-updates"></a>유지 관리 모드 업데이트
유지 관리 모드 업데이트는 디스크 펌웨어 업그레이드 등의 강제 업데이트입니다. 이 업데이트를 사용하려면 디바이스가 유지 관리 모드여야 합니다. 세부 정보를 참조 하세요. [2 단계: 유지 관리 모드로 전환](#step2)합니다. Azure 클래식 포털을 사용하여 유지 관리 모드 업데이트를 설치할 수 없습니다. 대신 StorSimple용 Windows PowerShell을 사용해야 합니다. 

유지 관리 모드 업데이트를 설치하는 방법에 대한 세부 정보는 [StorSimple용 Windows PowerShell을 통해 유지 관리 모드 업데이트 설치](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)를 참조하세요.

> [!IMPORTANT]
> 유지 관리 모드 업데이트는 각 컨트롤러에 개별적으로 적용되어야 합니다. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Azure 클래식 포털을 통해 일반 업데이트 설치
Azure 클래식 포털을 사용하여 StorSimple 디바이스에 업데이트를 적용할 수 있습니다.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 일반 업데이트 설치
또는, StorSimple 용 Windows PowerShell을 사용하여 일반(표준 모드) 업데이트를 적용할 수 있습니다.

> [!IMPORTANT]
> StorSimple용 Windows PowerShell을 사용하여 정기적으로 업데이트를 설치할 수 있지만 Azure 클래식 포털을 통해 정기적으로 업데이트를 설치하는 것이 좋습니다. 업데이트 1부터, 포털에서 업데이트를 설치하기 전에 사전 검사를 수행합니다. 이러한 검사는 오류의 사전 파악과 더 원활한 환경을 위한 것입니다. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 유지 관리 모드 업데이트 설치
StorSimple용 Windows PowerShell을 사용하여 유지 관리 모드 업데이트를 StorSimple 디바이스에 적용합니다. 모든 I/O 요청은 이 모드에서 일시 중지됩니다. 비휘발성 임의 액세스 메모리 (NVRAM) 등의 서비스 또는 클러스터링 서비스도 중지됩니다. 이 모드를 종료하거나 입력하면 두 컨트롤러 모두 다시 부팅됩니다. 이 모드를 종료하면 모든 서비스가 다시 시작되고 정상 상태여야 합니다. (몇 분이 걸릴 수 있습니다.)

유지 관리 모드 업데이트를 적용해야 하는 경우  설치해야 하는 업데이트가 있다는 경고를 Azure 클래식 포털을 통해 수신합니다. 이 경고는 StorSimple용 Windows PowerShell을 사용하여 업데이트를 설치하기 위한 지침을 포함합니다. 디바이스를 업데이트한 후, 동일한 절차에 따라 디바이스를 일반 모드로 변경합니다. 단계별 지침을 참조 하세요. [4 단계: 유지 관리 모드 종료](#step4)합니다.

> [!IMPORTANT]
> * 유지 관리 모드에 들어가기 전에 Azure 클래식 포털의 **유지 관리** 페이지에서 **하드웨어 상태**를 확인하여 두 디바이스 컨트롤러 모두가 정상 상태인지 확인합니다. 컨트롤러가 정상 상태가 아니면 다음 단계는 Microsoft 지원에 문의하세요. 자세한 내용은 Microsoft 지원에 문의로 이동합니다. 
> * 유지 관리 모드에 있는 경우, 업데이트를 먼저 하나의 컨트롤러에 적용한 다음 다른 컨트롤러에 적용해야 합니다.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>1단계: 직렬 콘솔에 연결 <a name="step1">
먼저,  PuTTY와 같은 애플리케이션을 사용하여 직렬 콘솔에 액세스합니다. 다음 절차는 PuTTY를 사용하여 직렬 콘솔에 연결하는 방법을 설명합니다.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>2단계: 유지 관리 모드로 전환 <a name="step2">
콘솔에 연결한 후, 설치할 업데이트가 있는지 여부를 결정하고 유지 관리 모드로 전환하여 설치합니다.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>3단계: 업데이트를 설치 합니다 <a name="step3">
다음으로 업데이트를 설치합니다.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>4단계: 유지 관리 모드 종료 <a name="step4">
마지막으로, 유지 관리 모드를 종료합니다.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>StorSimple용 Windows PowerShell을 통해 핫픽스를 설치합니다.
Microsoft Azure StorSimple에 대한 업데이트와 달리 핫픽스는 공유 폴더에서 설치됩니다. 업데이트와 마찬가지로 두 종류의 핫픽스가 있습니다. 

* 일반 핫픽스 
* 유지 관리 모드 핫픽스  

다음 절차에서는 StorSimple 용 Windows PowerShell을 사용하여 일반 및 유지 관리 모드 핫픽스를 설치 하는 방법을 설명합니다.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>디바이스를 공장 기본 설정으로 다시 설정하는 경우 업데이트에 미치는 영향
디바이스를 공장 기본 설정으로 다시 설정하는 경우 업데이트가 모두 손실됩니다. 공장 재설정 디바이스를 등록하고 구성한 후, StorSimple용 Windows PowerShell 및/또는 Azure 클래식 포털을 통해 수동으로 업데이트를 설치해야 합니다. 공장 재설정에 대한 자세한 내용은 [디바이스를 공장 기본 설정으로 재설정](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스를 관리](storsimple-windows-powershell-administration.md)하는 방법을 자세히 알아봅니다.
* [StorSimple Manager 서비스를 사용하여 StorSimple 디바이스를 관리](storsimple-manager-service-administration.md)하는 방법을 자세히 알아봅니다.

