---
title: StorSimple 암호 변경 | Microsoft Docs
description: StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 스냅숏 관리자 및 디바이스 관리자 암호를 변경하는 방법에 대해 설명합니다.
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
ms.openlocfilehash: ab874bbdcd47a4bfa9abfba721afa46d0f23a338
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60638011"
---
# <a name="use-the-storsimple-device-manager-service-to-change-your-storsimple-passwords"></a>StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 암호 변경

## <a name="overview"></a>개요
Azure Portal의 **디바이스 관리자** 옵션에는 StorSimple 디바이스 관리자 서비스로 관리되는 StorSimple 디바이스에서 다시 구성할 수 있는 모든 디바이스 매개 변수가 포함되어 있습니다. 이 자습서에서는 **디바이스 설정**에서 **보안** 옵션을 사용하여 디바이스 관리자 또는 StorSimple Snapshot Manager 암호를 변경하는 방법에 대해 설명합니다.

## <a name="change-the-device-administrator-password"></a>디바이스 관리자 암호 구성
Windows PowerShell 인터페이스를 사용하여 StorSimple 디바이스에 액세스할 때 디바이스 관리자 암호를 입력해야 합니다. StorSimple 디바이스를 서비스에 처음으로 등록할 때 이 인터페이스의 기본 암호는 *Password1*입니다. 데이터 보안을 위해 등록 과정 마지막에 이 암호를 변경해야 합니다. 이 암호를 변경하지 않고 등록 과정을 종료할 수 없습니다. 자세한 내용은 [3단계: StorSimple용 Windows PowerShell을 통해 디바이스 구성 및 등록](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)을 참조하세요.

등록하는 동안 먼저 Windows PowerShell 인터페이스를 통해 설정된 암호는 나중에 Azure Portal을 통해 변경할 수 있습니다. 디바이스 관리자 암호를 변경하려면 다음 단계를 수행합니다.

#### <a name="to-change-the-device-administrator-password"></a>디바이스 관리자 암호 변경 방법
1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다.

2. 디바이스를 나열하는 테이블에서 해당 암호를 변경하려는 디바이스를 클릭합니다.

    ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. **설정** 블레이드에서 **디바이스 설정 &gt; 보안**으로 이동합니다.

    ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. **보안 설정** 블레이드에서 **암호**를 클릭하여 디바이스 관리자 암호를 변경합니다.

    ![](./media/storsimple-8000-change-passwords/changepwd3.png)

5. **암호** 블레이드에서 8자에서 15자를 포함하는 관리자 암호를 입력합니다. 암호는 대문자, 소문자, 숫자 및 특수 문자 중 3가지 이상의 조합이어야 합니다.

6. 암호를 확인합니다.

    ![](./media/storsimple-8000-change-passwords/changepwd4.png)

7. **저장**을 클릭하고 확인하라는 메시지가 표시되면 **예**를 클릭합니다.

    ![](./media/storsimple-8000-change-passwords/changepwd6.png)

이제 디바이스 관리자 암호를 업데이트해야 합니다. 이 수정된 암호를 사용하여 Windows PowerShell 인터페이스에 액세스할 수 있습니다.

## <a name="set-the-storsimple-snapshot-manager-password"></a>StorSimple 스냅숏 관리자 암호 설정
StorSimple 스냅숏 관리자 소프트웨어는 Windows 호스트에 상주하며 관리자가 로컬 및 클라우드 스냅숏의 형태로 StorSimple 디바이스의 백업을 관리할 수 있습니다.

StorSimple 스냅숏 관리자에서 디바이스를 구성하면, 디바이스 IP 주소 및 암호를 입력하여 저장소 디바이스를 인증하라는 메시지가 표시됩니다.

Azure Portal을 통해 StorSimple Snapshot Manager에 대한 암호를 설정하거나 변경할 수 있습니다. 다음 단계를 수행하여 StorSimple Snapshot Manager 암호를 설정하거나 변경합니다.

#### <a name="to-set-the-storsimple-snapshot-manager-password"></a>StorSimple Snapshot Manager 암호를 설정하려면
1. StorSimple 디바이스 관리자 서비스로 이동하고 **디바이스**를 클릭합니다.

2. 디바이스를 나열하는 테이블에서 해당 StorSimple Snapshot Manager 암호를 설정하거나 변경하려는 디바이스를 클릭하여 선택합니다.

     ![](./media/storsimple-8000-change-passwords/changepwd1.png)

3. **설정** 블레이드에서 **디바이스 설정 &gt; 보안**으로 이동합니다.

     ![](./media/storsimple-8000-change-passwords/changepwd2.png)

4. **보안 설정** 블레이드에서 **암호**를 클릭하여 StorSimple Snapshot Manager 암호를 설정하거나 변경합니다.

     ![](./media/storsimple-8000-change-passwords/changepwd3.png) 

5. **암호** 블레이드에서 14자 또는 15자인 암호를 입력합니다. 암호에 대문자, 소문자, 숫자 및 특수 문자 중 3가지 이상의 조합이 포함되어 있는지 확인합니다.

6. 암호를 확인합니다.

     ![](./media/storsimple-8000-change-passwords/changepwd5.png)

7. **저장**을 클릭하고 확인하라는 메시지가 표시되면 **예**를 클릭합니다.

     ![](./media/storsimple-8000-change-passwords/changepwd6.png)

이제 StorSimple 스냅숏 관리자 암호가 업데이트됩니다.

## <a name="next-steps"></a>다음 단계
* [StorSimple 보안](storsimple-8000-security.md)에 대해 자세히 알아봅니다.
* [디바이스 구성을 수정](storsimple-8000-modify-device-config.md)하는 방법을 자세히 알아봅니다.
* [StorSimple 디바이스 관리자 서비스를 사용하여 StorSimple 디바이스를 관리하는 방법](storsimple-8000-manager-service-administration.md)에 대해 자세히 알아봅니다.

