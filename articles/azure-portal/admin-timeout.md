---
title: Azure Portal 사용자의 디렉터리 수준 비활성 시간 제한 설정 | Microsoft Docs
description: 관리자는 세션이 로그 아웃 되기 전에 최대 유휴 시간을 적용할 수 있습니다. 비활성 시간 제한 정책은 디렉터리 수준에서 설정 됩니다.
services: azure-portal
keywords: 설정, 시간 제한
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 950580048f0496fd8436901938a5b6768c61bab6
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132084"
---
# <a name="set-directory-level-inactivity-timeout"></a>디렉터리 수준 비활성 시간 제한 설정

비활성 시간 제한 설정은 사용자가 자신의 워크스테이션을 보호 하는 것을 잊은 경우 리소스를 무단 액세스 로부터 보호 하는 데 도움이 됩니다. 사용자가 잠시 동안 유휴 상태 이면 해당 Azure Portal 세션이 자동으로 로그 아웃 됩니다. [전역 관리자 역할](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) 의 관리자는 세션이 로그 아웃 되기 전에 최대 유휴 시간을 적용할 수 있습니다. 비활성 시간 제한 설정은 디렉터리 수준에서 적용 됩니다. 디렉터리에 대 한 자세한 내용은 [Active Directory Domain Services 개요](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)를 참조 하세요.

## <a name="configure-the-inactive-timeout-setting"></a>비활성 시간 제한 설정 구성

전역 관리자 인 경우 Azure Portal의 모든 사용자에 대해 유휴 시간 제한 설정을 적용 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 전역 페이지 헤더에서 **설정을** 선택 합니다.
3. 링크 텍스트 **구성 디렉터리 수준 제한 시간**을 선택 합니다.

    ![링크 텍스트가 강조 표시 된 포털 설정을 보여 주는 스크린샷](./media/admin-timeout/settings.png)

4. 새 페이지가 열립니다. **디렉터리 수준 비활성 시간 제한 구성** 페이지에서 **Azure Portal에 대 한 디렉터리 수준 유휴 시간 제한 사용** 을 선택 하 여 설정을 켭니다.
5. 그런 다음 세션이 자동으로 로그 아웃 되기 전에 사용자가 유휴 상태를 유지할 수 있는 **시간** 및 **분** 을 입력 합니다.
6. **적용**을 선택합니다.

    ![디렉터리 수준 비활성 시간 제한을 설정 하는 페이지를 보여 주는 스크린샷](./media/admin-timeout/configure.png)

비활동 제한 시간 정책이 올바르게 설정 되었는지 확인 하려면 전역 페이지 헤더에서 **알림** 을 선택 합니다. 성공 알림이 나열 되는지 확인 합니다.

  ![디렉터리 수준 비활성 시간 초과에 대 한 성공적인 알림 메시지를 보여 주는 스크린샷](./media/admin-timeout/confirmation.png)

이 설정은 새 세션에 적용 됩니다. 이미 로그인 한 사용자에 게는 즉시 적용 되지 않습니다.

> [!NOTE]
> 관리자가 디렉터리 수준 시간 제한 설정을 구성한 경우 사용자는 정책을 재정의 하 고 자신의 비활성 로그 아웃 기간을 설정할 수 있습니다. 그러나 사용자는 디렉터리 수준에서 설정 된 것 보다 작은 시간 간격을 선택 해야 합니다.
>

## <a name="next-steps"></a>다음 단계

* [Azure Portal 기본 설정 지정](set-preferences.md)
* [사용자 설정 내보내기 또는 삭제](azure-portal-export-delete-settings.md)
* [고대비 켜기 및 테마 변경](azure-portal-change-theme-high-contrast.md)
