---
title: StorSimple Virtual Array 디바이스 관리자 암호 변경 | Microsoft Docs
description: Azure Portal 또는 StorSimple Virtual Array 웹 UI를 사용하여 디바이스 관리자 암호를 변경하는 방법을 설명합니다.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 11490814-d9fd-4dc7-9c3b-55dd2c23eaf1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5308badf439254062a8aefca1840eb21bc234ace
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60580415"
---
# <a name="change-the-storsimple-virtual-array-device-administrator-password-via-storsimple-device-manager"></a>StorSimple Device Manager를 통해 StorSimple Virtual Array 디바이스 관리자 암호 변경

## <a name="overview"></a>개요

Windows PowerShell 인터페이스를 사용하여 StorSimple Virtual Array에 액세스할 때 디바이스 관리자 암호를 입력해야 합니다. StorSimple 디바이스를 처음으로 프로비전하고 시작할 때 기본 암호는 *Password1*입니다. 데이터 보안을 위해 처음 로그인하면 기본 암호가 만료되며, 암호를 변경해야 합니다.

프로덕션 환경에 디바이스가 배포된 후에는 언제든지 로컬 웹 UI 또는 Azure Portal을 사용하여 디바이스 관리자 암호를 변경할 수 있습니다. 이 문서에서는 각 절차에 대해 설명합니다.

 ![디바이스 블레이드](./media/storsimple-virtual-array-change-device-admin-password/ova-devices-blade.png)

## <a name="use-the-azure-portal-to-change-the-password"></a>Azure Portal을 사용하여 암호 변경

Azure Portal을 통해 디바이스 관리자 암호를 변경하려면 다음 단계를 수행합니다.

#### <a name="to-change-the-device-administrator-password-via-the-azure-portal"></a>Azure Portal을 통해 디바이스 관리자 암호를 변경하려면

1. 서비스 방문 페이지에서 서비스를 선택하고 서비스 이름을 두 번 클릭한 다음, **관리** 섹션 내에서 **디바이스**를 클릭합니다. 그러면 모든 StorSimple Virtual Array 디바이스를 나열하는 **디바이스** 블레이드가 열립니다.

2. **디바이스** 블레이드에서 암호를 변경해야 하는 디바이스를 두 번 클릭합니다.

3. 디바이스의 **설정** 블레이드에서 **보안**을 클릭합니다.

4. **보안 설정** 블레이드에서 다음을 수행합니다.
   
   1. **디바이스 관리자 암호** 섹션으로 스크롤합니다. 8자에서 15자를 포함하는 관리자 암호를 입력합니다.
   2. 암호를 확인합니다.
   3. 블레이드 위쪽에서 **저장**을 클릭합니다.

디바이스 관리자 암호가 이제 업데이트되었습니다. 수정된 암호를 사용하여 로컬에서 디바이스에 액세스할 수 있습니다.

![보안 설정 블레이드](./media/storsimple-virtual-array-change-device-admin-password/ova-change-device-pwd.png)

## <a name="use-the-local-web-ui-to-change-the-password"></a>로컬 웹 UI를 사용하여 암호 변경

로컬 웹 UI를 통해 디바이스 관리자 암호를 변경하려면 다음 단계를 수행합니다.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>로컬 웹 UI를 통해 디바이스 관리자 암호를 변경하려면

1. 로컬 웹 UI에서 디바이스에 대한 **유지 관리** > **암호 변경**을 클릭합니다.
   
    ![password1 변경](./media/storsimple-virtual-array-change-device-admin-password/image40.png)
2. **현재 암호**를 입력합니다.
3. **새 암호**를 제공합니다. 암호의 길이는 8자 이상이어야 합니다. 대문자, 소문자, 숫자 및 특수 문자 이렇게 4가지 중 3가지가 포함되어야 합니다.
   
    암호는 마지막 24개의 암호와 동일할 수 없으니 유의하세요.
4. 확인을 위해 암호를 다시 입력합니다.
   
    ![password2 변경](./media/storsimple-virtual-array-change-device-admin-password/image41.png)
5. 페이지 아래쪽에서 **적용**을 클릭합니다. 이제 새 암호가 적용됩니다. 암호 변경이 실패하면 다음 오류가 표시됩니다.
   
    ![암호 오류](./media/storsimple-virtual-array-change-device-admin-password/image42.png)
   
    암호가 성공적으로 업데이트되면 알림이 표시됩니다. 그런 다음, 수정된 암호를 사용하여 로컬에서 디바이스에 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계
[StorSimple 가상 배열을 관리](storsimple-ova-web-ui-admin.md)하는 방법을 알아봅니다.

