---
title: StorSimple 8000 시리즈 디바이스의 DATA 0 설정 수정 | Microsoft Docs
description: StorSimple용 Windows PowerShell을 사용하여 StorSimple 디바이스에서 DATA 0 네트워크 인터페이스를 다시 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 3cf136c5ddec8f4998d15c597914e1f806453945
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60631586"
---
# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-8000-series-device"></a>StorSimple 8000 시리즈 디바이스에서 DATA 0 네트워크 인터페이스 설정 수정

## <a name="overview"></a>개요

Microsoft Azure StorSimple 디바이스에 DATA 0에서 DATA 5까지 6개의 네트워크 인터페이스가 있습니다. DATA 0 인터페이스는 항상 Windows PowerShell 인터페이스 또는 직렬 콘솔을 통해 구성되며 자동으로 클라우드가 활성화됩니다. Azure Portal을 통해 DATA 0 네트워크 인터페이스를 구성할 수 없습니다.

StorSimple 디바이스의 초기 배포 중 설치 마법사를 통해 DATA 0 인터페이스가 처음 구성됩니다. 디바이스가 운영 모드에 있을 때 DATA 0를 다시 구성해야 할 수 있습니다. 이 자습서에서는 StorSimple용 Windows PowerShell을 통해 DATA 0 네트워크 설정을 수정하는 두 가지 방법을 제공합니다.

이 자습서를 읽은 후에 다음을 수행할 수 있습니다.

* 설정 마법사를 통해 DATA 0 네트워크 설정 수정
* `Set-HcsNetInterface` cmdlet을 통해 DATA 0 네트워크 설정 수정

## <a name="modify-data-0-network-settings-through-setup-wizard"></a>설정 마법사를 통해 DATA 0 네트워크 설정 수정
StorSimple 디바이스의 Windows PowerShell 인터페이스에 연결하고 설치 마법사 세션을 실행하여 DATA 0 네트워크 설정을 다시 구성할 수 있습니다. DATA 0 설정을 수정하려면 다음 단계를 수행합니다.

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>설정 마법사를 통해 DATA 0 네트워크 설정을 수정하려면
1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 **디바이스 관리자 암호**를 제공합니다. 기본 암호는 `Password1`입니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
    `Invoke-HcsSetupWizard`
3. 디바이스의 DATA 0 인터페이스 구성을 도와주는 설치 마법사가 나타납니다. IP 주소, 게이트웨이 및 네트워크 마스크에 대한 새 값을 제공합니다.

> [!NOTE]
> Azure Portal에서 StorSimple 디바이스 **네트워크 설정** 블레이드를 통해 고정된 컨트롤러 IP를 다시 구성해야 합니다. 자세한 내용은 [네트워크 인터페이스 수정](storsimple-8000-modify-device-config.md#modify-network-interfaces)을 참조하세요.

## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface cmdlet을 통해 DATA 0 네트워크 설정 수정
다른 방법은 `Set-HcsNetInterface` cmdlet을 사용하여 DATA 0 네트워크 인터페이스를 다시 구성하는 방법입니다. StorSimple 디바이스의 Windows PowerShell 인터페이스에서 cmdlet이 실행됩니다. 이 절차를 사용하면 컨트롤러가 고정된 IP를 여기서 구성할 수도 있습니다. DATA 0 설정을 수정하려면 다음 단계를 수행합니다. 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Set-HcsNetInterface cmdlet을 통해 DATA 0 네트워크 설정을 수정하려면
1. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 디바이스 관리자 암호를 제공합니다. 기본 암호는 `Password1`입니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
   
    각괄호에 DATA 0에 대해 다음 값을 입력합니다.
   
   * IPv4 주소
   * IPv4 게이트웨이
   * IPv4 서브넷 마스크
   * 컨트롤러 0에 대한 고정 IPv4 주소
   * 컨트롤러 1에 대한 고정 IPv4 주소
     
     이 cmdlet을 사용하는 방법에 대한 자세한 내용은 [StorSimple용 Windows PowerShell cmdlet 참조](https://technet.microsoft.com/library/dn688161.aspx)로 이동합니다.

## <a name="next-steps"></a>다음 단계
* DATA 0 이외의 네트워크 인터페이스를 구성하려면 [Azure Portal에서 네트워크 설정 구성](storsimple-8000-modify-device-config.md)을 사용할 수 있습니다. 
* 네트워크 인터페이스를 구성할 때 문제가 발생하는 경우 [배포 문제 해결](storsimple-troubleshoot-deployment.md)을 참조하세요.

