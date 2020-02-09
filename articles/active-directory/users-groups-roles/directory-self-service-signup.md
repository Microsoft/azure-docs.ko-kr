---
title: 전자 메일 확인 사용자에 대 한 셀프 서비스 등록-Azure AD | Microsoft Docs
description: Azure Active Directory (Azure AD) 테넌트에서 셀프 서비스 등록 사용
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 953837e22cdd3ba8a54d702eac61461739db82d2
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74027642"
---
# <a name="what-is-self-service-sign-up-for-azure-active-directory"></a>Azure Active Directory에 대 한 셀프 서비스 등록 이란?

이 문서에서는 셀프 서비스 등록을 사용 하 여 Azure Active Directory (Azure AD)에서 조직을 채우는 방법을 설명 합니다. 관리 되지 않는 Azure AD 조직에서 도메인 이름을 사용 하려는 경우 [관리자 권한으로 관리 되지 않는 디렉터리 가져오기](domains-admin-takeover.md)를 참조 하세요.

## <a name="why-use-self-service-sign-up"></a>셀프 서비스 등록을 사용 하는 이유
* 고객이 원하는 서비스를 더욱 빠르게 제공합니다.
* 서비스에 대한 메일 기반 제공 사항을 만듭니다.
* 사용자가 쉽게 기억할 수 있는 업무용 메일 별칭을 사용 하 여 id를 만들 수 있는 전자 메일 기반 등록 흐름을 만듭니다.
* 셀프 서비스로 작성된 Azure AD 디렉터리를 관리되는 디렉터리로 변환하여 다른 서비스에 사용할 수 있습니다.

## <a name="terms-and-definitions"></a>용어 및 정의
* **셀프 서비스 등록**: 사용자가 클라우드 서비스에 등록 하 고 해당 전자 메일 도메인을 기반으로 Azure AD에서 id가 자동으로 생성 되는 방법입니다.
* **관리되지 않는 Azure AD 디렉터리**: ID가 생성되는 디렉터리입니다. 관리되지 않는 디렉터리는 전역 관리자가 없는 디렉터리입니다.
* **메일로 확인된 사용자**: Azure AD의 사용자 계정의 한 유형입니다. 셀프 서비스 제공 사항에 등록한 후 자동으로 생성된 ID를 갖는 사용자를 메일로 확인된 사용자라고 합니다. 메일로 확인된 사용자는 creationmethod=EmailVerified로 태그가 지정된 디렉터리의 일반 멤버입니다.

## <a name="how-do-i-control-self-service-settings"></a>셀프 서비스 설정을 제어하는 방법
관리자는 현재 두 개의 셀프 서비스 컨트롤을 보유하며 다음을 제어할 수 있습니다.

* 사용자가 메일을 통해 디렉터리를 조인할 수 있습니다.
* 사용자가 애플리케이션 및 서비스를 자신이 사용하도록 라이선스를 허여할 수 있습니다.

### <a name="how-can-i-control-these-capabilities"></a>이러한 기능은 어떻게 제어할 수 있나요?
관리자는 Azure AD cmdlet인 다음의 Set-MsolCompanySettings 매개 변수를 사용하여 이러한 기능을 구성할 수 있습니다.

* **AllowEmailVerifiedUsers** 디렉터리를 만들거나 조인할 수 있는지 여부를 제어합니다. 이 매개 변수를 $false로 설정하면 메일로 확인된 사용자가 디렉터리를 조인할 수 없습니다.
* **AllowAdHocSubscriptions** 는 사용자가 셀프 서비스 등록을 수행할 수 있는 기능을 제어 합니다. 이 매개 변수를 $false로 설정 하면 사용자가 셀프 서비스 등록을 수행할 수 없습니다.
  
AllowEmailVerifiedUsers 및 AllowAdHocSubscriptions는 관리 되거나 관리 되지 않는 디렉터리에 적용 될 수 있는 디렉터리 수준 설정입니다. 예제는 다음과 같습니다.

* contoso.com과 같은 확인된 도메인을 사용하여 디렉터리를 관리합니다.
* 다른 디렉터리에서 B2B 공동 작업을 사용 하 여 contoso.com의 홈 디렉터리에 존재 하지 않는 사용자 (userdoesnotexist@contoso.com)를 초대 합니다.
* 홈 디렉터리에는 AllowEmailVerifiedUsers가 설정되어 있습니다.

이전 조건이 true인 경우, 멤버 사용자는 홈 디렉터리에서 생성되고, B2B 게스트 사용자는 초대 디렉터리에서 생성됩니다.

Flow 및 PowerApps 평가판 등록은 **AllowAdHocSubscriptions** 설정에 의해 제어 되지 않습니다. 자세한 내용은 다음 문서를 참조하세요.

* [내 기존 사용자가 Power BI를 사용하기 시작하지 않도록 방지하는 방법](https://support.office.com/article/Power-BI-in-your-Organization-d7941332-8aec-4e5e-87e8-92073ce73dc5#bkmk_preventjoining)
* [조직의 Flow Q&A](https://docs.microsoft.com/flow/organization-q-and-a)

### <a name="how-do-the-controls-work-together"></a>컨트롤이 어떻게 작동하나요?
이러한 두 매개 변수를 함께 사용 하 여 셀프 서비스 등록을 보다 정확 하 게 제어할 수 있습니다. 예를 들어 다음 명령을 사용 하면 사용자가 셀프 서비스 등록을 수행할 수 있지만 해당 사용자에 게 Azure AD의 계정이 이미 있는 경우에만 (즉, 전자 메일을 통해 확인 된 계정을 만들어야 하는 사용자는 먼저 셀프 서비스 등록을 수행할 수 없습니다.)

```powershell
    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true
```

다음 순서도는 이러한 매개 변수의 다양 한 조합과 디렉터리 및 셀프 서비스 등록에 대 한 결과 조건을 설명 합니다.

![셀프 서비스 등록 제어 순서도](./media/directory-self-service-signup/SelfServiceSignUpControls.png)

매개 변수 사용 방법에 대한 자세한 내용 및 예는 [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure AD에 사용자 지정 도메인 이름 추가](../fundamentals/add-custom-domain.md)
* [Azure PowerShell을 설치하고 구성하는 방법](/powershell/azure/overview)
* [Azure PowerShell](/powershell/azure/overview)
* [Azure Cmdlet 참조](/powershell/azure/get-started-azureps)
* [Set-MsolCompanySettings](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0)
* [관리 되지 않는 디렉터리에서 회사 또는 학교 계정 닫기](users-close-account.md)
