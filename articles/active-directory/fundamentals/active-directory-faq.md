---
title: FAQ(질문과 대답) - Azure Active Directory | Microsoft Docs
description: Azure 및 Azure Active Directory, 암호 관리 및 애플리케이션 액세스에 대한 일반적인 질문과 답변입니다.
services: active-directory
author: ajburnle
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: troubleshooting
ms.date: 11/12/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d88873dafd5a3ffac3f0135d877ff407427ffd6f
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179864"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Active Directory에 대해 자주 묻는 질문과 대답
Azure Active Directory(Azure AD)는 ID, 액세스 관리 및 보안의 모든 측면에 걸쳐있는 포괄적인 IDaaS(Identity as a Service) 솔루션입니다.

자세한 내용은 [Azure Active Directory란?](active-directory-whatis.md)을 참조하세요.


## <a name="access-azure-and-azure-active-directory"></a>Azure 및 Azure Active Directory 액세스
**Q: Azure Portal에서 Azure AD에 액세스하려고 할 때 “구독을 찾을 수 없음”이 표시되는 이유는 무엇인가요?**

**A:** Azure Portal에 액세스하려면 각 사용자에게 Azure 구독을 통한 권한이 필요합니다. 유료 Office 365 또는 Azure AD 구독이 없는 경우 무료 [Azure 계정](https://azure.microsoft.com/free/
) 또는 유료 구독을 활성화해야 합니다.

자세한 내용은 다음을 참조하세요.

* [Azure 구독과 Azure Active Directory의 연관 관계](active-directory-how-subscriptions-associated-directory.md)

---
**Q: Azure AD, Office 365와 Azure 간에는 어떤 관계가 있나요?**

**A:** Azure AD는 모든 웹 서비스에 공통 ID 및 액세스 기능을 제공합니다. Office 365, Microsoft Azure, Intune 또는 기타 제품을 사용하든지 이러한 모든 서비스에 대해 로그온 및 액세스 관리 설정을 지원하는 데 Azure AD를 이미 사용 중입니다.

웹 서비스를 사용하도록 설정된 모든 사용자는 하나 이상의 Azure AD 인스턴스에 사용자 계정으로 정의되어 있습니다. 클라우드 애플리케이션 액세스와 같은 무료 Azure AD 기능에 이러한 계정을 설정할 수 있습니다.

Enterprise Mobility + Security와 같은 Azure AD 유료 서비스는 포괄적인 엔터프라이즈 규모 관리 및 보안 솔루션을 통해 Office 365 및 Microsoft Azure와 같은 기타 웹 서비스를 보완합니다.

---

**Q:  소유자와 글로벌 관리자의 차이점은 무엇인가요?**

**A:** 기본적으로 Azure 구독에 등록하는 사람에게는 Azure 리소스에 대한 소유자 역할이 할당됩니다. 소유자는 Azure 구독이 연결된 디렉터리에서 Microsoft 계정이나 회사 또는 학교 계정을 사용할 수 있습니다.  이 역할은 Azure Portal에서 서비스를 관리할 권한이 있습니다.

다른 사용자가 동일한 구독을 사용하여 로그인하고 서비스에 액세스해야 하는 경우 적절한 [기본 제공 역할](../../role-based-access-control/built-in-roles.md)로 할당할 수 있습니다. 자세한 내용은 [Azure Portal를 사용 하 여 Azure 역할 할당 추가 또는 제거](../../role-based-access-control/role-assignments-portal.md)를 참조 하세요.

기본적으로 Azure 구독에 등록하는 사람에게는 디렉터리에 대한 글로벌 관리자 역할이 할당됩니다. 글로벌 관리자는 모든 Azure AD 디렉터리 기능에 액세스할 수 있습니다. Azure AD는 디렉터리 및 ID 관련 기능을 관리하는 다른 관리자 역할 집합을 가지고 있습니다. 이러한 관리자는 Azure Portal의 다양한 기능에 대한 액세스 권한을 갖게 됩니다. 관리자의 역할은 사용자 만들기 또는 편집, 다른 사람에게 관리자 역할 할당, 사용자 암호 재설정, 사용자 라이선스 관리 또는 도메인 관리와 같이 관리자가 수행할 수 있는 업무를 결정합니다.  Azure AD 디렉터리 관리자 및 그 역할에 대한 자세한 내용은 [Azure Active Directory에서 관리자 역할에 사용자 할당](active-directory-users-assign-role-azure-portal.md) 및 [Azure Active Directory에서 관리자 역할 할당](../users-groups-roles/directory-assign-admin-roles.md)을 참조하세요.

또한, Enterprise Mobility + Security와 같은 Azure AD 유료 서비스는 포괄적인 엔터프라이즈 규모 관리 및 보안 솔루션을 통해 Office 365 및 Microsoft Azure와 같은 기타 웹 서비스를 보완합니다.

---
**Q: 보고서에서 내 Azure AD 사용자 라이선스가 만료되는 시기를 표시하나요?**

**A:** 아니요.  현재는 제공되지 않습니다.

---

## <a name="get-started-with-hybrid-azure-ad"></a>하이브리드 Azure AD 시작


**Q: 협력자로 추가된 경우 테넌트를 어떻게 나가나요?**

**A:** 다른 조직의 테넌트에 협력자로 추가된 경우 오른쪽 위의 “테넌트 전환기”를 사용하여 테넌트 사이를 전환할 수 있습니다.  현재는 초대한 조직을 나갈 수 있는 방법이 없으며, 이 기능을 제공하기 위해 준비 중입니다.  이 기능이 제공될 때까지는 테넌트에서 사용자를 제거해 주도록 초대한 조직에게 요청할 수 있습니다.

---
**Q: Azure AD에 온-프레미스 디렉터리를 연결하려면 어떻게 해야 하나요?**

**A:** Azure AD Connect를 사용하여 온-프레미스 디렉터리를 Azure AD에 연결할 수 있습니다.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

---
**Q: 온-프레미스 디렉터리와 클라우드 애플리케이션 간에 SSO를 설정하려면 어떻게 할까요?**

**A:** 온-프레미스 디렉터리와 Azure AD 간에 SSO(Single Sign-On)만 설정하면 됩니다. Azure AD를 통해 클라우드 애플리케이션에 액세스할 수만 있다면 서비스는 온-프레미스 자격 증명으로 올바르게 인증하도록 사용자를 자동으로 유도합니다.

온-프레미스에서 SSO 구현은 AD FS(Active Directory Federation Services)와 같은 페더레이션 솔루션 또는 암호 해시 동기화 구성으로 쉽게 달성할 수 있습니다. 두 옵션 모두 Azure AD Connect 구성 마법사를 사용하여 쉽게 배포할 수 있습니다.

자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](../hybrid/whatis-hybrid-identity.md)을 참조하세요.

---
**Q: Azure AD에서 내 조직의 사용자에 대한 셀프 서비스 포털을 제공하나요?**

**A:** 예, Azure AD는 사용자 셀프 서비스 및 애플리케이션 액세스를 위해 [Azure AD 액세스 패널](https://myapps.microsoft.com)을 제공합니다. Office 365 고객인 경우 [Office 365 포털](https://portal.office.com)에서 동일한 많은 기능을 찾을 수 있습니다.

자세한 내용은 [액세스 패널 소개](../user-help/active-directory-saas-access-panel-introduction.md)를 참조하세요.

---
**Q: Azure AD를 사용하면 내 온-프레미스 인프라를 관리하는 데 도움이 되나요?**

**A:** 예. Azure AD Premium Edition에는 Azure AD Connect Health가 제공됩니다. Azure AD Connect Health를 사용하면 온-프레미스 ID 인프라 및 동기화 서비스를 모니터링하고 파악할 수 있습니다.  

자세한 내용은 [온-프레미스 ID 인프라 및 클라우드 동기화 서비스 모니터링](../hybrid/whatis-hybrid-identity-health.md)을 참고하세요.  

---
## <a name="password-management"></a>암호 관리
**Q: Azure AD 암호 쓰기 저장을 암호 동기화 없이 사용할 수 있나요? (이 시나리오에서 클라우드에 암호를 저장하기 않고 암호 쓰기 저장을 사용하여 Azure AD SSPR(셀프 서비스 암호 재설정)을 사용할 수 있나요?)**

**A:** 쓰기 저장을 사용하기 위해 Azure AD에 Active Directory 암호를 동기화할 필요가 없습니다. 페더레이션된 환경에서 Azure AD SSO(Single Sign-On)는 온-프레미스 디렉터리에 의존하여 사용자를 인증합니다. 이 시나리오는 Azure AD에서 추적되는 온-프레미스 암호를 필요로 하지 않습니다.

---
**Q: Active Directory 온-프레미스에 암호를 쓰기 저장하는 데 시간이 얼마나 걸리나요?**

**A:** 암호 쓰기 저장은 실시간으로 작동됩니다.

자세한 내용은 [암호 관리 시작](../authentication/quickstart-sspr.md)을 참조하세요.

---
**Q: 관리자가 관리하는 암호로 암호 쓰기 저장을 사용할 수 있나요?**

**A:** 예, 암호 쓰기 저장을 사용하도록 설정하는 경우 관리자가 수행하는 암호 작업이 온-프레미스 환경에 다시 기록됩니다.  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questions"></a>암호와 관련된 질문에 대한 자세한 답변은 [암호 관리 질문과 대답](../authentication/active-directory-passwords-faq.md)을 참조하세요.
---
**Q:  암호 변경을 시도하는 동안 기존 Office 365/Azure AD 암호를 기억할 수 없는 경우 어떻게 해야 하나요?**

**A:** 이러한 상황에는 두 가지 옵션이 있습니다.  SSPR(셀프 서비스 암호 재설정)을 사용할 수 있으면 사용합니다.  SSPR 작동 여부는 구성 방식에 달려 있습니다.  자세한 내용은 [암호 재설정 포털의 작동 원리](../authentication/howto-sspr-deployment.md)를 참조하세요.

Office 365 사용자의 경우 [사용자 암호 다시 설정](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US)에 설명된 단계를 사용하여 관리자가 암호를 재설정할 수 있습니다.

Azure AD 계정의 경우 다음 중 하나를 사용하여 관리자가 암호를 재설정할 수 있습니다.

- [Azure Portal에서 계정 재설정](active-directory-users-reset-password-azure-portal.md)
- [PowerShell 사용](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>보안
**Q: 시도가 일정 횟수 실패하면 계정이 잠기나요 아니면 좀 더 복잡한 전략이 사용되나요?**

좀 더 복잡한 계정 잠금 전략이 사용됩니다.  이 전략은 요청의 IP 주소와 입력된 암호를 기반으로 합니다. 또한 실패한 시도가 공격일 가능성에 따라 잠금 기간이 늘어납니다.  

**Q:  ‘이 암호가 너무 많이 사용되었습니다’라는 메시지와 함께 특정(공통) 암호가 거부되었습니다. 현재 Active Directory에서 사용되는 암호를 말하는 것인가요?**

"Password" 및 "123456"의 변형과 같이 전역에서 일반적인 암호를 말합니다.

**Q: 수상한 소스(봇넷, tor 엔드포인트)의 로그인 요청은 B2C 테넌트에서 차단되나요? 아니면 Basic 또는 Premium Edition 테넌트가 필요한가요?**

요청을 필터링하고 봇넷으로부터 보호하며, 모든 B2C 테넌트에 적용되는 게이트웨이가 있습니다.

## <a name="application-access"></a>애플리케이션 액세스

**Q: Azure AD 및 해당 기능과 미리 통합된 애플리케이션의 목록을 어디에서 찾을 수 있나요?**

**A:** Azure AD에는 Microsoft, 애플리케이션 서비스 공급 기업 및 파트너의 사전 통합된 애플리케이션이 2,600개 넘게 있습니다. 사전 통합된 모든 애플리케이션에서 SSO(Single Sign-On)를 지원합니다. SSO를 사용하면 조직의 자격 증명을 사용하여 앱에 액세스할 수 있습니다. 일부 애플리케이션은 자동화된 프로비전 및 프로비전 해제도 지원합니다.

미리 통합된 애플리케이션의 전체 목록은 [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)를 참조하세요.

---
**Q: 필요한 애플리케이션이 Azure AD 마켓플레이스에 없는 경우 어떻게 하나요?**

**A:** Azure AD Premium에서는 원하는 애플리케이션을 추가하고 구성할 수 있습니다. 애플리케이션의 기능 및 기본 설정에 따라 SSO 및 자동화된 프로비저닝을 구성할 수 있습니다.  

자세한 내용은 다음을 참조하세요.

* [Azure Active Directory 애플리케이션 갤러리에 있지 않은 애플리케이션에 Single Sign-On 구성](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](../app-provisioning/use-scim-to-provision-users-and-groups.md)

---
**Q: 사용자가 Azure AD를 사용하여 애플리케이션에 로그인하려면 어떻게 하나요?**

**A:** Azure AD는 다음과 같이 사용자가 자신의 애플리케이션을 보고 액세스할 수 있는 여러 가지 방법을 제공합니다.

* Azure AD 액세스 패널
* Office 365 애플리케이션 실행 프로그램
* 페더레이션된 앱에 직접 로그인
* 페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 딥 링크

자세한 내용은 [애플리케이션에 대한 최종 사용자 환경](../manage-apps/end-user-experiences.md)을 참조하세요.

---
**Q: Azure AD에서 애플리케이션에 대한 인증 및 Single Sign-On을 설정하는 다른 방법은 무엇인가요?**

**A:** Azure AD는 SAML 2.0, OpenID Connect, OAuth 2.0, WS-Federation 등 인증 및 권한 부여를 위해 여러 표준화된 프로토콜을 지원합니다. 또한 Azure AD는 양식 기반 인증만 지원하는 앱에 대해 암호 보관 및 자동화된 로그인 기능도 지원합니다.  

자세한 내용은 다음을 참조하세요.

* [Azure AD의 인증 시나리오](../develop/authentication-scenarios.md)
* [Active Directory 인증 프로토콜](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Azure AD의 애플리케이션에 대한 Single Sign-On](../manage-apps/what-is-single-sign-on.md)

---
**Q: 온-프레미스를 실행하는 애플리케이션을 추가할 수 있나요?**

**A:** Azure AD 애플리케이션 프록시는 선택한 온-프레미스 웹 애플리케이션에 대해 손쉽고 안전한 액세스를 제공합니다. Azure AD에서 SaaS(Software as a Service) 앱에 액세스하는 것과 동일한 방식으로 이러한 애플리케이션에 액세스할 수 있습니다. 네트워크 인프라 변경이나 VPN이 필요하지 않습니다.  

자세한 내용은 [온-프레미스 애플리케이션에 보안 원격 액세스를 제공하는 방법](../manage-apps/application-proxy.md)을 참조하세요.

---
**Q: 특정 애플리케이션에 액세스하는 사용자의 Multi-Factor Authentication을 어떻게 요청하나요?**

**A:** Azure AD 조건부 액세스에서는 각 애플리케이션에 대한 고유한 액세스 정책을 할당할 수 있습니다. 정책에서 언제든지 또는 사용자가 로컬 네트워크에 연결되지 않은 경우 Multi-Factor Authentication을 요구할 수 있습니다.  

자세한 내용은 [Azure Active Directory에 연결된 Office 365 및 기타 앱에 대한 액세스 보호](../conditional-access/overview.md)를 참조하세요.

---
**Q: SaaS 앱을 위한 자동 사용자 프로비저닝이란?**

**A:** Azure AD를 사용하여 대부분의 널리 사용되는 클라우드 SaaS 앱의 사용자 ID 만들기, 유지 관리 및 제거를 자동화합니다.

자세한 내용은 [Azure Active Directory를 사용하여 SaaS 애플리케이션의 사용자를 자동으로 프로비전 및 프로비전 해제](../app-provisioning/user-provisioning.md)를 참조하세요.

---
**Q:  Azure AD에서 보안 LDAP 연결을 설정할 수 있나요?**

**A:**  아니요. Azure AD는 LDAP(Lightweight Directory Access Protocol) 프로토콜 또는 보안 LDAP를 직접 지원하지 않습니다. 하지만 LDAP 연결을 구현하기 위해 Azure 네트워킹을 통해 네트워크 보안 그룹이 적절히 구성된 Azure AD 테넌트에서 Azure AD DS(Azure AD Domain Services) 인스턴스를 사용하도록 설정할 수 있습니다. 자세한 내용은 [Azure Active Directory Domain Services 관리되는 도메인에 대해 보안 LDAP 구성](../../active-directory-domain-services/tutorial-configure-ldaps.md)을 참조하세요.
