---
title: 셀프 서비스 그룹 관리 설정 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 보안 그룹 또는 Office 365 그룹을 만들고 관리하고 보안 그룹 또는 Office 365 그룹 멤버 자격을 요청합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b52604d4ad20fed83c4649f046722ed45e766c4
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097704"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Azure Active Directory에서 셀프 서비스 그룹 관리 설정 

사용자가 Azure Active Directory (Azure AD)에서 고유한 보안 그룹 또는 Office 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹 소유자는 멤버 자격 요청을 승인 또는 거부할 수 있으며 그룹 멤버 자격에 대 한 제어를 위임할 수 있습니다. 메일 사용이 가능한 보안 그룹이 나 배포 목록에는 셀프 서비스 그룹 관리 기능을 사용할 수 없습니다.

## <a name="self-service-group-membership-defaults"></a>셀프 서비스 그룹 구성원 자격 기본값

Azure Portal 또는 Azure AD PowerShell을 사용 하 여 보안 그룹을 만들 때 그룹의 소유자만 멤버 자격을 업데이트할 수 있습니다. [액세스 패널](https://account.activedirectory.windowsazure.com/r#/joinGroups) 및 모든 Office 365 그룹에서 만든 보안 그룹은 소유자 승인 또는 자동 승인 여부에 관계 없이 모든 사용자에 게 참가할 수 있습니다. 액세스 패널에서 그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다.

만든 그룹 | 보안 그룹 기본 동작 | Office 365 그룹 기본 동작
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | 소유자만 멤버를 추가할 수 있습니다.<br>표시 되지만 액세스 패널에서 조인할 수 없음 | 모든 사용자에 대해 조인 하려면 열기
[Azure Portal](https://portal.azure.com) | 소유자만 멤버를 추가할 수 있습니다.<br>표시 되지만 액세스 패널에서 조인할 수 없음<br>소유자는 그룹을 만들 때 자동으로 할당 되지 않습니다. | 모든 사용자에 대해 조인 하려면 열기
[액세스 패널](https://account.activedirectory.windowsazure.com/r#/joinGroups) | 모든 사용자에 대해 조인 하려면 열기<br>그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다. | 모든 사용자에 대해 조인 하려면 열기<br>그룹을 만들 때 멤버 자격 옵션을 변경할 수 있습니다.

## <a name="self-service-group-management-scenarios"></a>셀프 서비스 그룹 관리 시나리오

* **위임된 그룹 관리** 회사에서 사용하고 있는 SaaS 애플리케이션에 대한 액세스를 관리하는 관리자를 예로 들 수 있습니다. 이러한 액세스 권한 관리가 번거로워지자 관리자는 비즈니스 소유자에게 요청하여 새 그룹을 만듭니다. 관리자는 응용 프로그램에 대 한 액세스 권한을 새 그룹에 할당 하 고 이미 응용 프로그램에 액세스 하는 모든 사용자를 그룹에 추가 합니다. 그러면 비즈니스 소유자가 더 많은 사용자를 추가할 수 있으며 해당 사용자는 애플리케이션에 자동으로 프로비전됩니다. 비즈니스 소유자는 사용자에 대한 액세스를 관리하는 관리자를 대기할 필요가 없습니다. 관리자가 다른 비즈니스 그룹의 관리자에 게 동일한 사용 권한을 부여 하는 경우 해당 사용자는 자신의 그룹 구성원에 대 한 액세스를 관리할 수도 있습니다. 비즈니스 소유자와 관리자 모두 서로의 그룹 멤버 자격을 보거나 관리할 수 없습니다. 관리자는 계속 해당 애플리케이션에 액세스할 수 있는 모든 사용자를 보고 필요한 경우 액세스 권한을 차단할 수 있습니다.
* **셀프 서비스 그룹 관리** - 이 시나리오의 예제에서는 독립적으로 설정한 SharePoint Online 사이트를 보유한 두 사용자를 가정합니다. 이들은 상대방 팀을 해당 사이트에 액세스할 수 있도록 하려고 합니다. 이를 위해서 두 사용자는 Azure AD에 하나의 그룹을 만들고 각각 SharePoint Online에서 해당 사이트에 대한 액세스 권한을 제공할 그룹을 선택합니다. 사용자가 액세스 권한이 필요하면 액세스 패널에서 요청하고 승인을 받으면 자동으로 두 SharePoint Online 사이트에 대한 액세스 권한을 얻습니다. 이후 사이트에 액세스하는 모든 사용자가 특정 SaaS 애플리케이션에 대한 액세스 권한도 가져야 하는지를 둘 중 한 명이 결정할 수 있습니다. SaaS 애플리케이션의 관리자는 애플리케이션에 대한 액세스 권한을 SharePoint Online 사이트에 추가할 수 있습니다. 이후로 승인된 모든 요청은 두 SharePoint Online 사이트 및 해당 SaaS 애플리케이션에 대한 액세스 권한을 부여합니다.

## <a name="make-a-group-available-for-user-self-service"></a>사용자 셀프서비스에 사용할 수 있는 그룹 만들기

1. 디렉터리에 대한 전역 관리자인 계정으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
2. **사용자 및 그룹**을 선택한 다음 **그룹 설정**을 선택합니다.
3. **셀프 서비스 그룹 관리 사용**을 **예**로 설정합니다.
4. **사용자가 보안 그룹을 만들 수 있음** 또는 **사용자가 Office 365 그룹을 만들 수 있음**을 **예**로 설정합니다.
   * 이러한 설정을 사용하도록 설정하면 디렉터리의 모든 사용자가 새 보안 그룹을 만들고 이러한 그룹에 멤버를 추가할 수 있습니다. 이러한 새 그룹은 다른 모든 사용자에 대한 액세스 패널에도 표시됩니다. 그룹에 대한 정책 설정에서 허용하는 경우 다른 사용자가 이러한 그룹에 가입하도록 요청할 수 있습니다. 
   * 이러한 설정을 사용하지 않도록 설정한 경우 사용자 그룹을 만들 수 없고 기존 그룹의 소유자를 변경할 수 없습니다. 그러나 여전히 해당 그룹의 구성원을 관리하고 다른 사용자의 해당 그룹 가입 요청을 승인할 수 있습니다.

또한 **보안 그룹을 관리할 수 있는 사용자** 및 **Office 365 그룹을 관리할 수 있는 사용자**를 사용하여 사용자의 셀프 서비스 그룹 관리에 대한 액세스를 더 세밀하게 제어할 수 있습니다. **사용자가 그룹을 만들 수 있음**을 사용하도록 설정하면 테넌트의 모든 사용자가 새 그룹을 만들고 이러한 그룹에 멤버를 추가할 수 있습니다. 자신의 그룹을 만들 수 있는 개인은 지정할 수 없습니다. 다른 그룹 구성원을 그룹 소유자로 만들기 위한 개인만 지정할 수 있습니다.

**보안 그룹에 대해 셀프 서비스를 사용할 수 있는 사용자** 와 **Office 365 그룹을 관리할 수 있는 사용자** 를 **예**로 설정 하면 테넌트의 모든 사용자가 새 그룹을 만들 수 있습니다.

**보안 그룹을 관리할 수 있는 그룹** 또는 **Office 365 그룹을 관리할 수 있는 그룹**을 사용하여 단일 그룹의 멤버가 셀프 서비스를 사용할 수 있도록 지정할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory에 대한 추가 정보를 제공합니다.

* [Azure Active Directory 그룹을 사용하여 리소스에 대한 액세스 관리](../fundamentals/active-directory-manage-groups.md)
* [그룹 설정을 구성하는 Azure Active Directory cmdlets](groups-settings-cmdlets.md)
* [Azure Active Directory의 애플리케이션 관리](../manage-apps/what-is-application-management.md)
* [Azure Active Directory란?](../fundamentals/active-directory-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)
