---
title: StorSimple 디바이스 모드 변경 | Microsoft Docs
description: StorSimple 디바이스 모드 및 StorSimple용 Windows PowerShell을 사용하여 디바이스 모드를 변경하는 방법을 설명합니다.
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
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: e55964beff48df6ce24d99c01975d39b662f1612
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576094"
---
# <a name="change-the-device-mode-on-your-storsimple-device"></a>StorSimple 디바이스에서 디바이스 모드 변경

이 문서에서는 StorSimple 디바이스에서 작동할 수 있는 다양한 모드에 대한 간략한 설명을 제공합니다. StorSimple 디바이스는 표준, 유지 관리 및 복구의 세 가지 모드로 작동할 수 있습니다.

이 문서를 읽은 후 다음에 대해 알 수 있습니다.

* StorSimple 디바이스 모드 정의
* StorSimple 디바이스의 현재 모드를 파악하는 방법
* 표준에서 유지 관리 모드로, 그리고 *그 반대로*

위의 관리 작업은 StorSimple 디바이스의 Windows PowerShell 인터페이스를 통해만 수행할 수 있습니다.

## <a name="about-storsimple-device-modes"></a>StorSimple 디바이스 모드

StorSimple 디바이스는 표준, 유지 관리 또는 복구 모드로 작동할 수 있습니다. 각 모드는 아래에 간단하게 설명되어 있습니다.

### <a name="normal-mode"></a>표준 모드

완전히 구성된 StorSimple 디바이스에 대한 표준 작동 모드로 정의됩니다. 기본적으로 디바이스는 표준 모드에 있어야 합니다.

### <a name="maintenance-mode"></a>유지 관리 모드

때로는 StorSimple 디바이스가 유지 관리 모드에 있어야 합니다. 이 모드를 사용하면 디바이스에서 유지 관리를 수행하고 디스크 펌웨어 관련 업데이트와 같은 중단 업데이트를 설치할 수 있습니다.

StorSimple용 Windows PowerShell을 통해서만 시스템을 유지 관리 모드로 전환할 수 있습니다. 모든 I/O 요청은 이 모드에서 일시 중지됩니다. 비휘발성 임의 액세스 메모리 (NVRAM) 등의 서비스 또는 클러스터링 서비스도 중지됩니다. 이 모드로 들어가거나 종료하면 두 컨트롤러가 모두 다시 시작됩니다. 유지 관리 모드를 종료하면 모든 서비스가 다시 시작되고 정상 상태여야 합니다. 몇 분이 걸릴 수 있습니다.

> [!NOTE]
> **유지 관리 모드는 정상적으로 작동하는 디바이스에만 지원됩니다. 하나 또는 모든 컨트롤러가 작동하지 않는 디바이스에서 지원되지 않습니다.**


### <a name="recovery-mode"></a>복구 모드

복구 모드는 "네트워크를 지원하는 Windows 안전 모드"로 설명될 수 있습니다. 복구 모드는 Microsoft 기술 지원 서비스 팀을 통해 시스템에서 진단을 수행할 수 있습니다. 복구 모드의 기본 목표는 시스템 로그를 검색하는 것입니다.

시스템이 복구 모드로 전환되는 경우 Microsoft 지원에 다음 단계를 문의해야 합니다. 자세한 내용은 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)로 이동하세요.

> [!NOTE]
> **디바이스를 복구 모드로 유지할 수 없습니다. 디바이스가 잘못된 상태에 있으면 복구 모드는 Microsoft 고객 지원 담당자가 검사할 수 있는 상태로 디바이스를 전환하려고 합니다.**

## <a name="determine-storsimple-device-mode"></a>StorSimple 디바이스 모드 확인

#### <a name="to-determine-the-current-device-mode"></a>현재 디바이스 모드를 확인하려면

1. [디바이스 직렬 콘솔 연결에 PuTTY 사용](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)단계를 수행하여 디바이스 직렬 콘솔에 로그온합니다.
2. 디바이스의 직렬 콘솔 메뉴에 있는 배너 메시지를 살펴봅니다. 이 메시지는 디바이스가 유지 관리 또는 복구 모드에 있는지를 명시적으로 나타냅니다. 메시지에 시스템 모드에 대한 특정 정보가 없는 경우 디바이스가 표준 모드에 있습니다.

## <a name="change-the-storsimple-device-mode"></a>StorSimple 디바이스 모드 변경

StorSimple 디바이스를 유지 관리 모드(표준 모드에서)에 배치하여 유지 관리를 수행하거나 유지 관리 모드 업데이트를 설치할 수 있습니다. 유지 관리 모드로 들어가거나 종료하려면 다음 절차를 수행하세요.

> [!IMPORTANT]
> 유지 관리 모드에 들어가기 전에 Azure Portal에서 디바이스에 대한 **디바이스 설정 &gt; 하드웨어 상태**에 액세스하여 두 디바이스 컨트롤러 모두가 정상 상태인지 확인합니다. 하나 또는 모든 컨트롤러가 정상 상태가 아니면 다음 단계는 Microsoft 지원에 문의하세요. 자세한 내용은 [Microsoft 지원에 문의](storsimple-8000-contact-microsoft-support.md)로 이동하세요.
 

#### <a name="to-enter-maintenance-mode"></a>유지 관리 모드로 전환하려면

1. [디바이스 직렬 콘솔 연결에 PuTTY 사용](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console)단계를 수행하여 디바이스 직렬 콘솔에 로그온합니다.
2. 직렬 콘솔 메뉴에서 옵션 1, **모든 권한으로 로그인**을 선택합니다. 메시지가 표시되면 **디바이스 관리자 암호**를 제공합니다. 기본 암호는 `Password1`입니다.
3. 명령 프롬프트에 다음을 입력합니다. 
   
    `Enter-HcsMaintenanceMode`
4. 유지 관리 모드에서는 모든 I/O 요청이 중단되며 Azure Portal에 대한 연결이 끊어짐을 알리는 경고 메시지와 확인 요청 메시지가 표시됩니다. **Y** 를 입력하여 유지 관리 모드로 전환합니다.
5. 두 컨트롤러가 모두 다시 시작됩니다. 다시 시작이 완료되면 디바이스가 유지 관리 모드임을 나타내는 직렬 콘솔 배너가 표시됩니다. 샘플 출력은 다음과 같습니다.

```
    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Controller0>Enter-HcsMaintenanceMode
    Checking device state...

    In maintenance mode, your device will not service IOs and will be disconnected from the Microsoft Azure StorSimple Manager service. Entering maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to enter maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Passive
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>

```

#### <a name="to-exit-maintenance-mode"></a>유지 관리 모드를 종료하려면

1. 디바이스 직렬 콘솔에 로그온합니다. 배너 메시지에서 디바이스가 유지 관리 모드에 있는지 확인합니다.
2. 명령 프롬프트에 다음을 입력합니다.
   
    `Exit-HcsMaintenanceMode`
3. 경고 메시지와 확인 메시지가 표시됩니다. **Y** 를 입력하여 유지 관리 모드를 종료합니다.
4. 두 컨트롤러가 모두 다시 시작됩니다. 다시 시작이 완료되면 디바이스가 정상 모드임을 나타내는 직렬 콘솔 배너가 표시됩니다. 샘플 출력은 다음과 같습니다.

```
    -----------------------MAINTENANCE MODE------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0
    ---------------------------------------------------------------

    Controller0>Exit-HcsMaintenanceMode
    Checking device state...

    Before exiting maintenance mode, ensure that any updates that are required on both controllers have been applied. Failure to install on each controller could result in data corruption. Exiting maintenance mode will end the current session and reboot both controllers, which takes a few minutes to complete. Are you sure you want to exit maintenance mode?
    [Y] Yes [N] No (Default is "Y"): Y

    <BOTH CONTROLLERS RESTART>

    ---------------------------------------------------------------
    Microsoft Azure StorSimple Appliance Model 8100
    Name: 8100-SHX0991003G44MT
    Software Version: 6.3.9600.17820
    Copyright (C) 2014 Microsoft Corporation. All rights reserved.
    You are connected to Controller0 - Active
    ---------------------------------------------------------------

    Serial Console Menu
    [1] Log in with full access
    [2] Log into peer controller with full access
    [3] Connect with limited access
    [4] Change language
    Please enter your choice>
```

## <a name="next-steps"></a>다음 단계

StorSimple 디바이스에서 [표준 및 유지 관리 모드 업데이트를 적용](storsimple-update-device.md) 하는 방법을 알아봅니다.

