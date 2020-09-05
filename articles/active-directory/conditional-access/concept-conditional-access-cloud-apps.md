---
title: 조건부 액세스 정책에서 클라우드 앱 또는 작업 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 클라우드 앱 또는 작업 이란 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8b1440f34feb1cda2661178e520590cb9ec54f1
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948405"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>조건부 액세스: 클라우드 앱 또는 작업

클라우드 앱 또는 작업은 조건부 액세스 정책의 주요 신호에 속합니다. 관리자는 조건부 액세스 정책을 사용하여 특정 애플리케이션 또는 작업에 컨트롤을 할당할 수 있습니다.

- 관리자는 기본 제공되는 Microsoft 애플리케이션과 모든 [Azure AD 통합 애플리케이션](../manage-apps/what-is-application-management.md)(갤러리, 비갤러리 및 [애플리케이션 프록시](../manage-apps/what-is-application-proxy.md)를 통해 게시된 애플리케이션 등)을 포함한 애플리케이션 목록에서 선택할 수 있습니다.
- 관리자는 클라우드 애플리케이션이 아닌, 사용자 작업을 기반으로 하여 정책을 정의하도록 선택할 수 있습니다. 유일하게 지원되는 작업은 보안 정보 등록(미리 보기)입니다. 여기서는 조건부 액세스를 사용하여 [결합된 보안 정보 등록 환경](../authentication/howto-registration-mfa-sspr-combined.md)을 제어할 수 있습니다.

![조건부 액세스 정책을 정의하고 클라우드 앱을 지정](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 애플리케이션

기존 Microsoft 클라우드 애플리케이션 중 상당수는 선택할 수 있는 애플리케이션 목록에 포함되어 있습니다. 

관리자들은 Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다. Office 365(미리 보기) 및 Microsoft Azure 관리와 같은 일부 앱은 관련된 여러 자식 앱 또는 서비스를 포함합니다. 다음 목록은 전체 목록이 아니며 변경될 수 있습니다.

- [Office 365(미리 보기)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 및 Data Warehouse](../../azure-sql/database/conditional-access-configure.md)
- Dynamics CRM Online
- Microsoft Application Insights 분석
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 관리](#microsoft-azure-management)
- Microsoft Azure 구독 관리
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control 포털
- Microsoft Commerce Tools 인증 서비스
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 등록](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft PowerApps
- Bing에서 Microsoft Search
- Microsoft StaffHub
- Microsoft Stream
- Microsoft 팀
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI 서비스
- Project Online
- 비즈니스 온라인용 Skype
- VPN(가상 사설망)
- Windows Defender ATP

### <a name="office-365-preview"></a>Office 365(미리 보기)

Office 365는 Exchange, SharePoint 및 Microsoft 팀과 같은 클라우드 기반 생산성 및 협업 서비스를 제공합니다. Office 365 클라우드 서비스는 원활한 협업 환경을 보장하기 위해 긴밀하게 통합됩니다. Microsoft 팀과 같은 일부 앱이 SharePoint 또는 Exchange와 같은 다른 앱에 종속되어 있기 때문에 이러한 통합은 정책을 만들 때 혼동을 일으킬 수 있습니다.

Office 365(미리 보기) 앱을 사용하면 이러한 서비스를 한 번에 모두 대상으로 지정할 수 있습니다. [서비스 종속성](service-dependencies.md)과 관련된 문제들을 방지하려면 개별 클라우드 앱을 대상으로 지정하는 대신, 새 Office 365(미리 보기) 앱을 사용하는 것이 좋습니다. 이 애플리케이션 그룹을 대상으로 지정하면 일관되지 않은 정책 및 종속성으로 인해 발생할 수 있는 문제들을 방지하는 데 도움이 됩니다.

관리자는 Office 365(미리 보기) 앱을 포함하고 정책에서 선택한 특정 앱을 제외하여 원한다면 정책에서 특정 앱을 제외하도록 선택할 수 있습니다.

Office 365(미리 보기) 클라이언트 앱에 포함된 주요 애플리케이션:

   - Microsoft Flow
   - Microsoft Forms
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft 팀
   - Office 365 Exchange Online
   - Office 365 SharePoint Online
   - Office 365 Search Service
   - Office 365 Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - PowerApps
   - 비즈니스 온라인용 Skype
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure 관리

Microsoft Azure 관리 애플리케이션에는 여러 가지 기본 서비스가 포함되어 있습니다. 

   - Azure portal
   - Azure Resource Manager 공급자
   - 클래식 배포 모델 API
   - Azure PowerShell
   - Visual Studio 구독 관리자 포털
   - Azure DevOps
   - Azure Data Factory 포털

> [!NOTE]
> Microsoft Azure 관리 애플리케이션은 Azure PowerShell에 적용되며 이는 Azure Resource Manager API를 호출합니다. Microsoft Graph를 호출하는 Azure AD PowerShell에는 적용되지 않습니다.

## <a name="other-applications"></a>다른 애플리케이션

Microsoft 앱 외에도 관리자는 Azure AD에 등록된 애플리케이션을 조건부 액세스 정책에 추가할 수 있습니다. 이러한 애플리케이션에는 다음이 포함될 수 있습니다. 

- [Azure AD 애플리케이션 프록시](../manage-apps/what-is-application-proxy.md)를 통해 게시된 애플리케이션
- [갤러리에서 추가된 애플리케이션](../manage-apps/add-application-portal.md)
- [갤러리에 없는 사용자 지정 애플리케이션](../manage-apps/view-applications-portal.md)
- [앱 제공 컨트롤러 및 네트워크를 통해 게시된 레거시 애플리케이션](../manage-apps/secure-hybrid-access.md)
- [암호 기반 Single Sign-On](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md) 를 사용 하는 응용 프로그램

> [!NOTE]
> 조건부 액세스 정책은 서비스에 액세스하기 위한 요구 사항을 설정하므로 클라이언트(퍼블릭/네이티브) 애플리케이션에 적용할 수 없습니다. 다시 말해, 정책은 클라이언트(퍼블릭/네이티브) 애플리케이션에 직접 설정되지 않으며 다만 클라이언트가 서비스를 호출할 때 적용됩니다. 예를 들어, SharePoint 서비스에서 설정된 정책은 SharePoint를 호출하는 클라이언트에 적용됩니다. Exchange에 설정된 정책은 Outlook 클라이언트를 사용하여 이메일에 액세스하려고 할 때 적용됩니다. 따라서 클라우드 앱 선택기에서 클라이언트(퍼블릭/네이티브) 애플리케이션을 사용할 수 없으며, 테넌트에 등록된 클라이언트(퍼블릭/네이티브) 애플리케이션의 애플리케이션 설정에서 조건부 액세스 옵션을 사용할 수 없습니다. 


## <a name="user-actions"></a>사용자 작업

사용자 작업은 사용자가 수행할 수 있는 작업입니다. 현재 지원되는 작업은 **보안 정보 등록** 밖에 없습니다.이 작업에서는 결합 등록에 대해 사용하도록 설정된 사용자가 보안 정보를 등록하려고 할 때 조건부 액세스 정책을 적용할 수 있습니다. 자세한 내용은 [결합된 보안 정보 등록](../authentication/concept-registration-mfa-sspr-combined.md) 문서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 조건](concept-conditional-access-conditions.md)

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)
- [클라이언트 애플리케이션 종속성](service-dependencies.md)