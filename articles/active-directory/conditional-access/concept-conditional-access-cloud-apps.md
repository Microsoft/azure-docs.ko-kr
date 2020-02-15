---
title: 조건부 액세스 정책에서 클라우드 앱 또는 작업-Azure Active Directory
description: Azure AD 조건부 액세스 정책의 클라우드 앱 또는 작업 이란?
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
ms.openlocfilehash: a9d2780e09c099d76aa2ef4ec2638a410793481d
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186364"
---
# <a name="conditional-access-cloud-apps-or-actions"></a>조건부 액세스: 클라우드 앱 또는 작업

클라우드 앱 또는 작업은 조건부 액세스 정책의 주요 신호입니다. 관리자는 조건부 액세스 정책을 사용 하 여 특정 응용 프로그램이 나 동작에 컨트롤을 할당할 수 있습니다.

- 관리자는 기본 제공 Microsoft 응용 프로그램 및 갤러리, 비 갤러리 및 [응용 프로그램 프록시](../manage-apps/what-is-application-proxy.md)를 통해 게시 된 응용 프로그램을 비롯 한 모든 [Azure AD 통합 응용](../manage-apps/what-is-application-management.md) 프로그램을 포함 하는 응용 프로그램 목록에서 선택할 수 있습니다.
- 관리자는 클라우드 응용 프로그램을 기반으로 하지 않고 사용자 작업에 정책을 정의 하도록 선택할 수 있습니다. 유일 하 게 지원 되는 동작은 보안 정보 등록 (미리 보기)을 사용 하 여 결합 된 [보안 정보 등록 환경을](../authentication/howto-registration-mfa-sspr-combined.md)중심으로 제어를 적용 하는 조건부 액세스를 허용 합니다.

![조건부 액세스 정책 정의 및 클라우드 앱 지정](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft 클라우드 애플리케이션

기존 Microsoft 클라우드 응용 프로그램 대부분은 선택할 수 있는 응용 프로그램 목록에 포함 되어 있습니다. 

관리자는 Microsoft의 다음 클라우드 앱에 조건부 액세스 정책을 할당할 수 있습니다. Office 365 (미리 보기) 및 Microsoft Azure 관리와 같은 일부 앱은 관련 된 여러 자식 앱 또는 서비스를 포함 합니다. 다음 목록은 완전 하지 않으며 변경 될 수 있습니다.

- [Office 365 (미리 보기)](#office-365-preview)
- Azure Analysis Services
- Azure DevOps
- [Azure SQL Database 및 데이터 웨어하우스](../../sql-database/sql-database-conditional-access.md)
- Dynamics CRM Online
- Microsoft Application Insights 분석
- [Microsoft Azure Information Protection](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure 관리](#microsoft-azure-management)
- Microsoft Azure 구독 관리
- Microsoft Cloud App Security
- Microsoft Commerce Tools Access Control 포털
- Microsoft Commerce Tools 인증 서비스
- Microsoft Flow
- Microsoft Forms
- Microsoft Intune
- [Microsoft Intune 등록](https://docs.microsoft.com/intune/enrollment/multi-factor-authentication)
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

### <a name="office-365-preview"></a>Office 365 (미리 보기)

Office 365은 Exchange, SharePoint 및 Microsoft 팀과 같은 클라우드 기반 생산성 및 공동 작업 서비스를 제공 합니다. Office 365 클라우드 서비스는 원활한 공동 작업 환경을 보장 하기 위해 긴밀 하 게 통합 됩니다. 이러한 통합은 Microsoft 팀과 같은 일부 앱이 SharePoint 또는 Exchange와 같은 다른 앱에 종속 되어 있기 때문에 정책을 만들 때 혼동을 일으킬 수 있습니다.

Office 365 (미리 보기) 앱을 사용 하면 이러한 서비스를 한 번에 모두 대상으로 지정할 수 있습니다. 개별 클라우드 앱을 대상으로 지정 하는 대신 새 Office 365 (미리 보기) 앱을 사용 하는 것이 좋습니다. 이 응용 프로그램 그룹을 대상으로 지정 하면 일관 되지 않은 정책 및 종속성으로 인해 발생할 수 있는 문제를 방지할 수 있습니다.

관리자는 Office 365 (미리 보기) 앱을 포함 하 고 정책에서 선택한 특정 앱을 제외 하 여 정책에서 특정 앱을 제외 하도록 선택할 수 있습니다.

Office 365 (미리 보기) 클라이언트 앱에 포함 된 주요 응용 프로그램:

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

Microsoft Azure 관리 응용 프로그램에는 여러 기본 서비스가 포함 되어 있습니다. 

   - Azure portal
   - Azure Resource Manager 공급자
   - 클래식 배포 모델 Api
   - Azure PowerShell
   - Visual Studio 구독 관리자 포털
   - Azure DevOps
   - Azure Data Factory 포털

> [!NOTE]
> Microsoft Azure 관리 응용 프로그램은 Azure Resource Manager API를 호출 하는 Azure PowerShell에 적용 됩니다. Microsoft Graph를 호출 하는 Azure AD PowerShell에는 적용 되지 않습니다.

## <a name="other-applications"></a>다른 애플리케이션

Microsoft 앱 외에도 관리자는 조건부 액세스 정책에 Azure AD 등록 된 응용 프로그램을 추가할 수 있습니다. 이러한 응용 프로그램에는 다음이 포함 될 수 있습니다. 

- [Azure AD 응용 프로그램 프록시](../manage-apps/what-is-application-proxy.md) 를 통해 게시 된 응용 프로그램
- [갤러리에서 추가 된 응용 프로그램](../manage-apps/add-application-portal.md)
- [갤러리에 없는 사용자 지정 응용 프로그램](../manage-apps/add-non-gallery-app.md)
- [앱 배달 컨트롤러 및 네트워크를 통해 게시 된 레거시 응용 프로그램](../manage-apps/secure-hybrid-access.md)

## <a name="user-actions"></a>사용자 작업

사용자 작업은 사용자가 수행할 수 있는 작업입니다. 현재 지원 되는 작업은 **보안 정보 등록 (미리 보기)** 입니다 .이를 통해 결합 된 등록을 사용 하도록 설정 된 사용자가 보안 정보를 등록 하려고 할 때 조건부 액세스 정책을 적용할 수 있습니다. 자세한 내용은 [결합 된 보안 정보 등록 (미리 보기)](../authentication/concept-registration-mfa-sspr-combined.md)문서에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스: 조건](concept-conditional-access-conditions.md)

- [조건부 액세스 공통 정책](concept-conditional-access-policy-common.md)
- [클라이언트 응용 프로그램 종속성](service-dependencies.md)
