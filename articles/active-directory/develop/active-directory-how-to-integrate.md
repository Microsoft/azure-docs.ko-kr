---
title: Azure Active Directory와 통합 하는 방법 | Microsoft Docs
description: 응용 프로그램을 Azure Active Directory와 통합 하 고, 간소화 된 로그인, id 관리, multi-factor authentication 및 access control과 같은 기능에 대 한 리소스를 얻는 이점에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: dbbe501b27bf7ae4d4cb4bc00501aeb1fb06819e
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161225"
---
# <a name="integrating-with-azure-active-directory"></a>Azure Active Directory와의 통합

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

이 문서에서는 응용 프로그램을 Azure Active Directory (Azure AD)와 통합 하 고 통합을 위해 리소스를 가져오는 이점에 대해 알아봅니다. Azure AD는 클라우드 응용 프로그램에 대 한 엔터프라이즈급 id 관리를 조직에 제공 합니다. Azure AD 통합은 사용자에게 효율적인 로그인 환경을 제공하고 애플리케이션이 IT 정책을 준수하도록 돕습니다.

## <a name="how-to-integrate"></a>통합 방법

여러 가지 방법으로 애플리케이션을 Azure AD와 통합할 수 있습니다. 애플리케이션에 맞게 이 시나리오를 활용하세요.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>응용 프로그램에 로그인 하는 방법으로 Azure AD 지원

**로그인 충돌을 줄이고 지원 비용을 절감합니다.** Azure AD를 사용하여 애플리케이션에 로그인하면 사용자는 이름 및 암호를 기억하지 않아도 됩니다. 개발자는 저장하고 보호할 암호가 줄어듭니다. 잊어버린 암호 재설정을 처리하지 않으므로 상당히 효율적입니다. Azure AD에서는 Office 365, Microsoft Azure 등과 같이 전 세계에서 인기가 많은 다양한 클라우드 애플리케이션에 로그인할 수 있도록 지원합니다. 약 500만 개의 조직에서 4억 3천 만명 이상이 사용 중이므로 사용자가 이미 Azure AD에 로그인되어 있을 가능성이 높습니다. [Azure AD 로그인 지원 추가](authentication-scenarios.md)에 대해 자세히 알아보세요.

**애플리케이션 등록을 단순화합니다.**  애플리케이션을 등록하는 동안 등록 양식을 사전에 입력하거나 완전히 제거할 수 있도록 Azure AD가 사용자에 대한 필수 정보를 보낼 수 있습니다. 사용자는 소셜 미디어 및 모바일 애플리케이션에서 본 것과 비슷한 친숙한 동의 환경을 통해 자신의 Azure AD 계정을 사용하여 애플리케이션을 등록할 수 있습니다. 모든 사용자는 IT 담당자의 도움 없이 Azure AD와 통합된 애플리케이션을 등록 및 로그인할 수 있습니다. [Azure AD 계정 로그인을 위해 애플리케이션 등록](../../app-service/configure-authentication-provider-aad.md)에 대해 자세히 알아보세요.

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>사용자를 찾고, 사용자 프로 비전을 관리 하 고, 응용 프로그램에 대 한 액세스를 제어 합니다.

**디렉터리에서 사용자를 찾습니다.**  다른 사람을 초대하거나 액세스 권한을 부여할 때 사용자가 메일 주소를 입력할 필요 없이 Graph API를 사용하면 조직에서 다른 사람을 검색하고 찾는 데 도움이 됩니다. 사용자는 조직 계층의 세부 정보 보기 등 친숙한 주소록 유형의 인터페이스를 사용하여 찾아볼 수 있습니다. [Graph API](active-directory-graph-api.md)에 대해 자세히 알아보세요.

**Active Directory 그룹 및 고객이 이미 관리하는 메일 그룹을 다시 사용합니다.**  Azure AD에는 고객이 이미 메일 배포에 사용하고 액세스를 관리하는 그룹이 포함되어 있습니다. 고객이 애플리케이션에서 별도의 그룹 집합을 만들고 관리할 필요 없이 Graph API를 사용하여 그룹을 다시 사용하세요. 로그인 토큰의 애플리케이션에 그룹 정보가 전송될 수도 있습니다. [Graph API](active-directory-graph-api.md)에 대해 자세히 알아보세요.

**Azure AD를 사용하여 애플리케이션에 대한 액세스 권한이 있는 사용자를 제어합니다.**  Azure AD의 관리자 및 애플리케이션 소유자는 애플리케이션에 대한 액세스 권한을 특정 사용자 및 그룹에게 할당할 수 있습니다. Graph API를 사용하여 이 목록을 읽고 리소스의 프로비전 및 프로비전 해제와 애플리케이션 내의 액세스를 제어할 수 있습니다.

**Azure AD를 사용하여 역할 기반 Access Control을 사용합니다.**  관리자와 애플리케이션 소유자는 Azure AD에 애플리케이션을 등록할 때 정의하는 역할에 사용자 및 그룹을 할당할 수 있습니다. 역할 정보는 로그인 토큰의 애플리케이션에 전송되며 Graph API를 사용해서도 읽을 수 있습니다. [Azure AD를 사용하여 권한 부여](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/)에 대해 자세히 알아보세요.

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>사용자의 프로필, 일정, 메일, 연락처, 파일 등에 액세스

**Azure AD는 Office 365 및 기타 Microsoft 비즈니스 서비스의 권한 부여 서버입니다.**  애플리케이션 로그인에 Azure AD를 지원하거나 OAuth 2.0을 사용하는 Azure AD 사용자 계정에 현재 사용자 계정 연결을 지원하는 경우 사용자의 프로필, 일정, 이메일, 연락처, 파일 및 기타 정보에 대한 읽기 및 쓰기 권한을 요청할 수 있습니다. 사용자의 일정에 매끄럽게 이벤트를 쓸 수 있으며 사용자의 OneDrive에 파일을 읽거나 쓸 수 있습니다. [Office 365 API 액세스](https://msdn.microsoft.com/office/office365/howto/platform-development-overview)에 대해 자세히 알아보세요.

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Azure 및 Office 365 마켓플레이스에서 응용 프로그램 홍보

**이미 Azure AD를 사용 중인 수백만 개의 조직에 애플리케이션을 홍보합니다.**  이러한 마켓플레이스를 검색하고 찾아보는 사용자는 자격 있는 클라우드 서비스 고객이 되어 이미 하나 이상의 클라우드 서비스를 사용하고 있습니다. [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/)에서 애플리케이션 홍보하기에 대해 자세히 알아보세요.

**사용자가 애플리케이션에 등록하면 사용자의 Azure AD 액세스 패널 및 Office 365 앱 시작 관리자에 표시됩니다.**  사용자가 나중에 애플리케이션으로 쉽고 빠르게 돌아갈 수 있어 사용자 참여를 향상시킵니다. [Azure AD 액세스 패널](../user-help/active-directory-saas-access-panel-introduction.md)에 대해 자세히 알아보세요.

### <a name="secure-device-to-service-and-service-to-service-communication"></a>안전한 장치-서비스 및 서비스 간 통신

**서비스 및 디바이스의 ID 관리에 Azure AD를 사용하면 써야 할 코드를 줄일 수 있으며 IT 담당자가 액세스를 관리할 수 있습니다.**  서비스 및 디바이스는 OAuth를 사용하여 Azure AD에서 토큰을 가져올 수 있으며 이 토큰으로 웹 API에 액세스할 수 있습니다. Azure AD를 사용하면 복잡한 인증 코드를 작성할 필요가 없습니다. 서비스 및 디바이스의 ID가 Azure AD에 저장되므로 IT 담당자는 키 및 해지를 애플리케이션에서 별도로 수행하지 않고도 한 곳에서 관리할 수 있습니다.

## <a name="benefits-of-integration"></a>통합의 이점

Azure AD와 통합하면 추가 코드를 작성할 필요가 없다는 이점이 있습니다.

### <a name="integration-with-enterprise-identity-management"></a>엔터프라이즈 id 관리와의 통합

**애플리케이션이 IT 정책을 준수하도록 합니다.**  조직에서는 Azure AD와 조직의 엔터프라이즈 ID 관리 시스템을 통합하므로 조직을 떠날 경우 IT 담당자가 추가 단계를 수행할 필요 없이 자동으로 애플리케이션에 대한 액세스 권한이 사라집니다. IT 담당자는 애플리케이션에 액세스할 수 있는 사람을 관리하고 필요한 액세스 정책(예: 다단계 인증)을 결정할 수 있어 복잡한 회사 정책을 준수하기 위해 작성해야 하는 코드가 줄어듭니다. Azure AD는 애플리케이션에 로그인한 사람의 세부적인 감사 로그를 관리자에게 제공하므로 IT 담당자가 사용 현황을 추적할 수 있습니다.

**Azure AD는 애플리케이션을 AD와 통합할 수 있도록 Active Directory를 클라우드로 확장합니다.**  전 세계 많은 조직에서 Active Directory를 기본 로그인 및 ID 관리 시스템으로 사용하며 애플리케이션에 AD와 함께 작업하도록 요구합니다. Azure AD와 통합하면 응용 프로그램이 Active Directory와 통합됩니다.

### <a name="advanced-security-features"></a>고급 보안 기능

**다단계 인증.**  Azure AD는 네이티브 다단계 인증을 제공합니다. 애플리케이션에 액세스하기 위해 IT 관리자가 다단계 인증을 요구할 수 있으므로 이 지원을 직접 코딩할 필요가 없습니다. [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)에 대해 자세히 알아보세요.

**비정상적인 로그인 감지.**  Azure AD는 의심스러운 활동을 감지하고 발생 가능한 문제를 IT 관리자에게 알리는 기계 학습 알고리즘을 사용하면서 하루 10억 개가 넘는 로그인을 처리합니다. Azure AD 로그인을 지원함으로써 애플리케이션이 보호됩니다. [Azure Active Directory 액세스 보고서 보기](../active-directory-view-access-usage-reports.md)에 대해 자세히 알아보세요.

**조건부 액세스.**  관리자는 다단계 인증 외에도 특정 조건을 충족해야 사용자가 애플리케이션에 로그인할 수 있도록 요구할 수 있습니다. 클라이언트 디바이스의 IP 주소 범위, 지정된 그룹의 멤버 자격, 액세스에 사용되는 디바이스의 상태 조건을 설정할 수 있습니다. [Azure Active Directory 조건부 액세스](../active-directory-conditional-access-azure-portal.md)에 대해 자세히 알아보세요.

### <a name="easy-development"></a>손쉬운 개발

**산업 표준 프로토콜.**  Microsoft는 산업 표준을 지원합니다. Microsoft id 플랫폼은 업계 표준 OAuth 2.0 및 Openid connect Connect 1.0 프로토콜을 지원 합니다. [Microsoft id 플랫폼 인증 프로토콜](active-directory-v2-protocols.md)에 대해 자세히 알아보세요.

**오픈 소스 라이브러리.**  Microsoft는 인기 있는 언어 및 플랫폼에 대해 개발에 박차를 가하기 위해 완벽하게 지원되는 오픈 소스 라이브러리를 제공합니다. 소스 코드는 Apache 2.0에 따라 사용이 허가되며, 원하는 대로 소스 코드를 분기하여 프로젝트에 다시 제공할 수 있습니다. [MSAL (Microsoft 인증 라이브러리)](reference-v2-libraries.md)에 대해 자세히 알아보세요.

### <a name="worldwide-presence-and-high-availability"></a>전 세계의 현재 상태 및 고가용성

**Azure AD는 전 세계 데이터 센터에 배포되어 24시간 내내 관리 및 모니터링됩니다.**  Azure AD는 Microsoft Azure 및 Office 365의 ID 관리 시스템이며 전 세계 28곳의 데이터 센터에 배포됩니다. 디렉터리 데이터는 3개 이상의 데이터 센터에 복제되어야 합니다. 전역 부하 분산 장치는 사용자가 자신의 데이터가 들어 있는 Azure AD의 가장 근접한 복사본에 액세스하도록 하며 문제가 감지되면 요청을 다른 데이터 센터로 자동으로 다시 라우팅합니다.

## <a name="next-steps"></a>다음 단계

[코드 작성 시작하기](v2-overview.md#getting-started).

[Microsoft id 플랫폼을 사용 하 여 사용자 로그인](authentication-scenarios.md)

