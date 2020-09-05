---
title: Active Directory Azure Active Directory 비교
description: 이 문서에서는 Active Directory Domain Services (추가)를 Azure Active Directory (AD)와 비교 합니다. 두 id 솔루션의 주요 개념에 대해 간략하게 설명 하 고 다른 개념을 설명 합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 02/26/2020
ms.author: martinco
ms.openlocfilehash: 21c9f585c123dcf838442f128e07afd00db30856
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177723"
---
# <a name="compare-active-directory-to-azure-active-directory"></a>Active Directory Azure Active Directory 비교

Azure Active Directory은 클라우드에 대 한 id 및 액세스 관리 솔루션의 다음 진화입니다. Microsoft는 조직에 사용자 당 단일 id를 사용 하 여 여러 온-프레미스 인프라 구성 요소 및 시스템을 관리할 수 있는 기능을 제공 하기 위해 Windows 2000에 Active Directory Domain Services를 도입 했습니다.

Azure AD는 클라우드 및 온-프레미스에서 모든 앱에 대 한 IDaaS (Identity as a Service) 솔루션을 조직에 제공 하 여이 접근 방식을 다음 수준으로 사용 합니다.

대부분의 IT 관리자는 Active Directory Domain Services 개념을 잘 알고 있습니다. 다음 표에서는 Active Directory 개념과 Azure Active Directory 간의 차이점과 유사성에 대해 간략하게 설명 합니다.

|개념|AD(Active Directory)|Azure Active Directory |
|:-|:-|:-|
|**사용자**|||
|프로 비전: 사용자 | 조직에서는 내부 사용자를 수동으로 만들거나 사내 또는 자동 프로비저닝 시스템 (예: Microsoft Identity Manager)을 사용 하 여 HR 시스템과 통합할 수 있습니다.|기존 AD 조직은 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) 를 사용 하 여 id를 클라우드에 동기화 합니다.</br> Azure AD는 [클라우드 HR 시스템](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)에서 사용자를 자동으로 만들 수 있도록 지원 합니다. </br>Azure AD는 [Scim 지원](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) SaaS 앱에서 id를 프로 비전 하 여 사용자에 대 한 액세스를 허용 하는 데 필요한 세부 정보를 앱에 자동으로 제공 합니다. |
|프로 비전: 외부 id| 조직에서는 외부 사용자를 전용 외부 AD 포리스트에 일반 사용자로 직접 만들어 외부 id (게스트 사용자)의 수명 주기를 관리 하는 관리 오버 헤드를 생성 합니다.| Azure AD는 외부 id를 지 원하는 특별 한 id 클래스를 제공 합니다. [AZURE AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) 는 외부 사용자 id에 대 한 링크를 관리 하 여 유효한 지 확인 합니다. |
| 권한 관리 및 그룹| 관리자가 사용자를 그룹 구성원으로 만듭니다. 그러면 앱 및 리소스 소유자가 그룹에 앱 또는 리소스에 대 한 액세스 권한을 부여 합니다.| [그룹](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) 은 Azure AD 에서도 사용할 수 있으며, 관리자는 그룹을 사용 하 여 리소스에 대 한 권한을 부여할 수도 있습니다. Azure AD에서 관리자는 그룹에 멤버 자격을 수동으로 할당 하거나 쿼리를 사용 하 여 그룹에 사용자를 동적으로 포함할 수 있습니다. </br> 관리자는 Azure AD에서 [자격 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) 를 사용 하 여 워크플로 및 필요한 경우 시간 기반 조건을 사용 하 여 앱 및 리소스 컬렉션에 대 한 액세스 권한을 사용자에 게 제공할 수 있습니다. |
| 관리 관리|조직에서는 AD의 도메인, 조직 구성 단위 및 그룹 조합을 사용 하 여 제어 하는 디렉터리 및 리소스를 관리 하는 관리 권한을 위임 합니다.| Azure ad는 azure ad RBAC (역할 기반 액세스 제어) 시스템에 [기본 제공 역할](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) 을 제공 하며, id 시스템, 앱 및 it에서 제어 하는 리소스에 대 한 권한 있는 액세스를 위임 하는 [사용자 지정 역할을 만드는](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview) 기능을 제한적으로 지원 합니다.</br>권한 있는 역할에 대 한 just-in-time, 제한 시간 또는 워크플로 기반 액세스를 제공 하도록 [Privileged Identity Management (PIM)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 을 사용 하 여 역할 관리를 향상 시킬 수 있습니다. |
| 자격 증명 관리| Active Directory 자격 증명은 암호, 인증서 인증 및 스마트 카드 인증을 기반으로 합니다. 암호 길이, 만료 및 복잡성을 기반으로 하는 암호 정책을 사용 하 여 암호를 관리 합니다.|Azure AD는 클라우드 및 온-프레미스에 대 한 지능형 [암호 보호](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad) 를 사용 합니다. 보호에는 스마트 잠금과 사용자 지정 암호 문구 및 대체가 차단 됩니다. </br>Azure AD는 FIDO2 같은 [multi-factor authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) 및 [암호 없는](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-passwordless) 기술을 통해 보안을 크게 강화 합니다. </br>Azure AD는 사용자에 게 [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks) 시스템을 제공 하 여 지원 비용을 절감 합니다. |
| **앱**|||
| 인프라 앱|Active Directory은 많은 인프라 온-프레미스 구성 요소 (예: DNS, DHCP, IPSec, WiFi, NPS 및 VPN 액세스)의 기반을 형성 합니다.|새로운 클라우드 세계에서 Azure AD는 응용 프로그램에 액세스 하 고 네트워킹 제어에 의존 하는 새로운 제어 평면입니다. 사용자가 인증[하는 경우 CA (조건부 액세스)](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)는 필요한 조건에서 어떤 앱에 액세스할 수 있는 사용자를 제어 합니다.|
| 기존 앱 및 레거시 앱| 대부분의 온-프레미스 앱은 LDAP, Windows 통합 인증 (NTLM 및 Kerberos) 또는 헤더 기반 인증을 사용 하 여 사용자에 대 한 액세스를 제어 합니다.| Azure AD는 온-프레미스에서 실행 되는 [AZURE ad 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) 에이전트를 사용 하 여 이러한 유형의 온-프레미스 앱에 대 한 액세스를 제공할 수 있습니다. 이 방법을 사용 하는 경우 Azure AD는 마이그레이션하는 동안 Kerberos를 사용 하 여 온-프레미스에서 사용자 Active Directory 인증할 수 있으며 레거시 앱과 함께 사용 해야 합니다. |
| SaaS 앱|Active Directory는 SaaS 앱을 기본적으로 지원 하지 않으며 AD FS와 같은 페더레이션 시스템이 필요 합니다.|OAuth2, SAML 및 WS 인증을 지 원하는 SaaS 앱 \* 을 통합 하 여 인증을 위해 AZURE AD를 사용할 수 있습니다. |
| 최신 인증을 사용 하는 LOB (기간 업무) 앱|조직에서는 Active Directory와 함께 AD FS를 사용 하 여 최신 인증을 요구 하는 LOB 앱을 지원할 수 있습니다.| 인증을 위해 Azure AD를 사용 하도록 최신 인증을 요구 하는 LOB 앱을 구성할 수 있습니다. |
| 중간 계층/디먼 서비스|온-프레미스 환경에서 실행 되는 서비스는 일반적으로 AD 서비스 계정 또는 gMSA (그룹 관리 서비스 계정)를 사용 하 여 실행 합니다. 이러한 앱은 서비스 계정의 사용 권한을 상속 합니다.| Azure AD는 클라우드에서 다른 작업을 실행 하기 위해 [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) 를 제공 합니다. 이러한 id의 수명 주기는 Azure AD에서 관리 되 고, 리소스 공급자에 연결 되어 백도어 액세스를 얻는 다른 용도로 사용할 수 없습니다.|
| **디바이스**|||
| 모바일|Active Directory는 타사 솔루션이 없는 모바일 장치를 기본적으로 지원 하지 않습니다.| Microsoft의 모바일 장치 관리 솔루션 Microsoft Intune는 Azure AD와 통합 됩니다. Microsoft Intune는 인증 중에 평가할 id 시스템에 장치 상태 정보를 제공 합니다. |
| Windows 데스크톱|Active Directory 그룹 정책, System Center Configuration Manager 또는 기타 타사 솔루션을 사용 하 여 Windows 장치를 관리 하는 도메인 가입 기능을 제공 합니다.|Windows 장치 [를 AZURE AD에 조인할](https://docs.microsoft.com/azure/active-directory/devices/)수 있습니다. 조건부 액세스는 장치가 인증 프로세스의 일부로 Azure AD에 가입 되어 있는지 확인할 수 있습니다. [Microsoft Intune](https://docs.microsoft.com/intune/what-is-intune)를 사용 하 여 Windows 장치를 관리할 수도 있습니다. 이 경우 조건부 액세스는 앱에 대 한 액세스를 허용 하기 전에 장치가 호환 되는지 (예: 최신 보안 패치와 바이러스 서명)를 고려 합니다.|
| Windows 서버| Active Directory은 그룹 정책 또는 기타 관리 솔루션을 사용 하 여 온-프레미스 Windows server에 대 한 강력한 관리 기능을 제공 합니다.| Azure의 Windows server 가상 머신은 [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/)로 관리할 수 있습니다. [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) 는 vm이 id 시스템 디렉터리 또는 리소스에 액세스 해야 하는 경우에 사용할 수 있습니다.|
| Linux/Unix 워크 로드|Active Directory는 타사 솔루션 없이는 기본적으로 Windows를 지원 하지 않습니다. 하지만 Active Directory Kerberos 영역으로 인증 하도록 Linux 컴퓨터를 구성할 수 있습니다.|Linux/Unix Vm은 [관리 되는 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/index) 를 사용 하 여 id 시스템 또는 리소스에 액세스할 수 있습니다. 일부 조직에서는 이러한 워크 로드를 클라우드 컨테이너 기술로 마이그레이션하고 관리 id를 사용할 수도 있습니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory란?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)
- [자체 관리형 Active Directory Domain Services, Azure Active Directory 및 관리형 Azure Active Directory Domain Services 비교](https://docs.microsoft.com/azure/active-directory-domain-services/compare-identity-solutions)
- [Azure Active Directory에 대해 자주 묻는 질문과 대답](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-faq)
- [Azure Active Directory의 새로운 기능](https://docs.microsoft.com/azure/active-directory/fundamentals/whats-new)
