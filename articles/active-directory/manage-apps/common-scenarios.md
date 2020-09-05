---
title: Azure Active Directory의 일반적인 애플리케이션 관리 시나리오 | Microsoft Docs
description: Azure AD를 사용하여 애플리케이션 관리 중앙 집중화
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ce3819ff1f9b0c61f7738f90ff17c2798fe888b
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642115"
---
# <a name="centralize-application-management-with-azure-ad"></a>Azure AD를 사용하여 애플리케이션 관리 중앙 집중화

암호는 전 세계 직원들에게 IT 악몽이자 고충입니다. 이 때문에 클라우드 및 기타 리소스를 위한 Microsoft의 ID 및 액세스 관리 솔루션인 Azure Active Directory로 전환하는 회사가 점점 늘어나고 있습니다. 애플리케이션마다 암호를 입력할 필요 없이 애플리케이션 간에 전환할 수 있습니다. Outlook, Workday, ADP를 빠르고 안전하게 열 수 있듯이, Outlook에서 Workday로, ADP로 신속하게 전환할 수 있습니다. 그런 다음, IT를 호출할 필요 없이 파트너는 물론이고 조직 외부의 다른 사람과 협업할 수 있습니다. 뿐만 아니라 Azure AD는 위치에 관계 없이 신원 확인을 위한 다단계 인증 같은 용도로 사용되는 앱을 보호하고, 의심스러운 로그인을 감지하는 지속적인 적응형 기계 학습 및 보안 인텔리전스를 사용하여 필요한 앱에 대한 안전한 액세스를 제공하는 방식으로 위험을 관리할 수 있습니다. 사용자뿐 아니라 IT에게도 매우 훌륭한 솔루션입니다. Just-In-Time 액세스 검토와 완전한 거버넌스 제품군을 제공하는 Azure AD를 사용하면 규정 준수 상태를 유지하고 정책을 적용하는 데 도움이 됩니다. 여기에 더해, 사용자 계정 프로비저닝을 자동화하면 더욱 간편하게 액세스를 관리할 수 있습니다. 고객이 Azure Active Directory의 애플리케이션 관리 기능을 사용하는 일반적인 시나리오를 살펴보세요.

**일반적인 시나리오**


> [!div class="checklist"]
> * 모든 애플리케이션을 위한 SSO
> * 프로비저닝 및 프로비저닝 해제 자동화 
> * 애플리케이션 보호
> * 애플리케이션 액세스 관리
> * 하이브리드 보안 액세스

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>시나리오 1: 모든 애플리케이션에 SSO 설정

더 이상 암호를 관리할 필요가 없습니다. 회사 자격 증명을 사용하여 필요한 모든 리소스에 안전하게 액세스하세요. 

|기능  | Description | 권장 |
|---------|---------|---------|
|SSO|신뢰할 수 있는 산업 표준을 사용하는 표준 기반 페더레이션 SSO입니다.|애플리케이션에서 [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation)를 지원하는 경우 항상 SAML/OIDC를 사용하여 SSO를 설정합니다.|
|내 앱|사용자에게 모든 애플리케이션을 검색 및 액세스할 수 있는 간단한 허브를 제공합니다. 앱 및 그룹에 대한 액세스를 요청하거나 다른 사용자를 대신하여 리소스에 대한 액세스를 관리하는 등의 셀프 서비스 기능을 통해 사용자 생산성을 높일 수 있습니다.| 앱을 SSO용 Azure AD와 통합한 후에는 조직에 [내 앱](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)을 배포합니다.|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>시나리오 2: 프로비저닝 및 프로비저닝 해제 자동화 


대부분의 애플리케이션은 사용자가 필요한 리소스에 액세스하려면 먼저 사용자를 애플리케이션에 프로비저닝해야 합니다. CSV 파일 또는 복잡 스크립트를 사용하면 비용이 많이 들고 관리가 어려울 수 있습니다. 뿐만 아니라 사용자에게 액세스 권한을 더 이상 허용하면 안 될 때 고객이 계정을 제거해야 합니다. 아래 도구를 활용하여 프로비저닝 및 프로비저닝 해제를 자동화할 수 있습니다. 


|기능  |Description|권장 |
|---------|---------|---------|
|SCIM 프로비저닝|[SCIM](https://aka.ms/SCIMOverview)은 사용자 프로비저닝 자동화에 대한 업계 모범 사례입니다. 모든 SCIM 규격 애플리케이션을 Azure AD와 통합할 수 있습니다. CSV 파일, 사용자 지정 스크립트 또는 온-프레미스 솔루션을 유지할 필요 없이 사용자 계정을 자동으로 만들고, 업데이트하고, 삭제할 수 있습니다.|Azure AD 앱 갤러리에서 지속적으로 새 앱이 추가되고 있는 [사전 통합](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) 앱 목록을 확인하세요.|
|Microsoft Graph|Azure AD의 다양한 데이터를 활용하여 애플리케이션에 필요한 데이터로 애플리케이션을 보강합니다.|[Microsoft Graph](https://developer.microsoft.com/graph/)를 활용하여 Microsoft 에코시스템의 데이터를 가져옵니다. |


## <a name="scenario-3-secure-your-applications"></a>시나리오 3: 애플리케이션 보호
ID는 보안의 핵심입니다. ID가 손상되면 너무 늦지 않게 도미노 효과를 막기가 매우 어렵습니다. 조직에서 ID 손상을 발견할 때까지 평균적으로 100일이 넘게 걸립니다. Azure AD에서 제공하는 도구를 사용하여 애플리케이션의 보안 태세를 강화하세요. 

|기능  |Description| 권장 |
|---------|---------| ---------|
|Azure MFA|Azure Multi-factor Authentication(MFA)은 Microsoft의 2단계 인증 솔루션입니다. 관리자가 승인한 인증 방법을 사용하는 Azure MFA는 간단한 로그인 프로세스에 대한 요구 사항을 충족하는 동시에 데이터와 애플리케이션에 대한 액세스를 보호합니다.| 사용자에게 [MFA를 사용](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)합니다.  |
|조건부 액세스|조건부 액세스를 사용하면 조건에 따라 클라우드 앱에 액세스할 수 있는 사용자를 결정하는 자동 액세스 제어 결정 시스템을 구현할 수 있습니다.| 고객이 사용 중인 [보안 기본값](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 및 [일반 정책](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)을 검토합니다. | 
|ID 보호|Identity Protection은 사용자 보호를 위해 Microsoft가 Azure AD를 사용하는 조직, Microsoft 계정의 소비자 공간 및 Xbox를 이용한 게임 등에서 사용자 위치로부터 습득한 학습을 사용합니다. Microsoft는 하루 6조 5000억 개의 신호를 분석하여 고객을 위협으로부터 보호합니다.|서비스에서 제공하는 [기본 ID 보호 정책](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)을 사용하도록 설정합니다. | 

## <a name="scenario-4-govern-access-to-your-applications"></a>시나리오 4: 애플리케이션 액세스 관리
조직에서는 ID 거버넌스를 통해 생산성(사용자가 조직에 가입할 때처럼 필요한 애플리케이션에 액세스하는 속도)과 보안(고용 상태의 변화처럼 시간에 따른 사용자의 액세스 권한 변화) 간에 적절한 균형을 유지할 수 있습니다. 

|기능  |Description|권장 |
|---------|---------| ---------|
|ELM|Azure AD 권한 관리는 조직 내부 및 외부의 사용자가 애플리케이션에 대한 액세스를 보다 효율적으로 관리하는 데 도움이 됩니다.| 관리자가 아닌 사용자가 [액세스 패키지](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)를 사용하여 애플리케이션 액세스를 관리할 수 있도록 허용합니다.|
|액세스 검토|사용자의 앱 액세스를 정기적으로 검토하여 필요한 사람만 계속 액세스할 수 있게 합니다.| 가장 중요한 애플리케이션에 대한 [액세스를 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)합니다. |
|Log Analytics|누가 어떤 애플리케이션에 액세스하는지에 대한 보고서를 작성하여 원하는 SIEM 도구에 저장하고 시간에 따라 데이터 원본 간에 데이터를 상호 연결합니다.| [로그 분석](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)을 사용하도록 설정하고 애플리케이션과 관련된 중요 이벤트에 대한 경고를 설정합니다. |


## <a name="scenario-5-hybrid-secure-access"></a>시나리오 5: 하이브리드 보안 액세스
클라우드 및 온-프레미스 애플리케이션의 모든 항목을 연결할 수 있는 경우 ID는 반드시 컨트롤 플레인이어야 합니다. Azure AD 및 파트너가 제공하는 도구를 활용하여 레거시 인증 기반 애플리케이션에 대한 액세스를 보호하세요.

|기능  |Description|권장 |
|---------|---------|---------|
|애플리케이션 프록시|요즈음 직원은 어디서나 언제든지 어느 디바이스에서나 생산성을 높이기를 원합니다. 클라우드 및 회사 앱 온-프레미스에서 SaaS 앱에 액세스해야 합니다. Azure AD 애플리케이션 프록시를 사용하면 비싸고 복잡한 VPN(가상 사설망) 또는 DMZ(경계 네트워크) 없이 견고한 액세스가 가능합니다.|온-프레미스 앱에 대한 [원격 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)를 설정합니다. |
|F5, Akamai, Zscaler|기존 네트워킹 및 배달 컨트롤러를 사용하여 비즈니스 프로세스에 여전히 중요 하지만 이전에는 Azure AD로 보호할 수 없었는 레거시 애플리케이션을 쉽게 보호할 수 있습니다. 이러한 애플리케이션을 보호하는 데 필요한 모든 조건은 이미 충족되었을 것입니다.| Akamai, Citrix, F5 또는 Zscaler를 사용하고 있나요? 당사의 [미리 빌드된 솔루션](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)을 살펴보세요. | 

## <a name="related-articles"></a>관련 문서

- [애플리케이션 관리](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [애플리케이션 프로비저닝](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [하이브리드 보안 액세스](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [ID 거버넌스](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft ID 플랫폼](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [ID 보안](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
