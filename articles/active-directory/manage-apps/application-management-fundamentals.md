---
title: '응용 프로그램 관리: 모범 사례 및 권장 사항 | Microsoft Docs'
description: Azure Active Directory에서 응용 프로그램을 관리 하기 위한 모범 사례 및 권장 사항을 알아봅니다. 응용 프로그램 프록시를 사용 하 여 온-프레미스 앱을 자동으로 프로 비전 및 게시 하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: d32728c1f388e9013b922d1f60d30e65d350bbc1
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642438"
---
# <a name="application-management-best-practices"></a>애플리케이션 관리 모범 사례

이 문서에는 Azure Active Directory (Azure AD)에서 응용 프로그램을 관리 하 고, 자동 프로 비전을 사용 하 고, 응용 프로그램 프록시를 통해 온-프레미스 앱을 게시 하는 방법에 대 한 권장

## <a name="cloud-app-and-single-sign-on-recommendations"></a>클라우드 앱 및 Single Sign-On 권장 사항
| 권장 | 주석 |
| --- | --- |
| 앱에 대 한 Azure AD 응용 프로그램 갤러리 확인  | Azure AD에는 SSO (Enterprise Single Sign-On)를 사용 하 여 사용할 수 있는 수천 개의 사전 통합 된 응용 프로그램이 포함 된 갤러리가 있습니다. 앱 별 설치 지침은 [SaaS 앱 자습서 목록](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)을 참조 하세요.  | 
| 페더레이션된 SAML 기반 SSO 사용  | 응용 프로그램에서 지 원하는 경우 암호 기반 SSO 및 ADFS 대신 Azure AD에서 페더레이션된 SAML 기반 SSO를 사용 합니다.  | 
| 인증서 서명에 SHA-256 사용  | Azure AD에서는 기본적으로 SHA-256 알고리즘을 사용 하 여 SAML 응답에 서명 합니다. 응용 프로그램에 SHA-1이 필요 하지 않으면 SHA-256을 사용 합니다 ( [인증서 서명 옵션](certificate-signing-options.md) 및 [응용 프로그램 로그인 문제](application-sign-in-problem-application-error.md)참조).  | 
| 사용자 할당 필요  | 기본적으로 사용자는 할당 되지 않은 엔터프라이즈 응용 프로그램에 액세스할 수 있습니다. 그러나 응용 프로그램이 역할을 노출 하거나 사용자의 내 앱에 응용 프로그램을 표시 하려는 경우에는 사용자 할당이 필요 합니다. [응용 프로그램 통합을 위한 개발자 가이드](developer-guidance-for-integrating-applications.md)를 참조 하세요.  | 
| 내 앱을 사용자에 게 배포 | 에서 [내 앱](end-user-experiences.md) `https://myapps.microsoft.com` 은 사용자에 게 할당 된 클라우드 기반 응용 프로그램에 대 한 단일 진입점을 제공 하는 웹 기반 포털입니다. 그룹 관리 및 셀프 서비스 암호 재설정과 같은 추가 기능을 추가 하면 사용자가 내 앱에서 해당 기능을 찾을 수 있습니다. [내 앱 배포 계획](access-panel-deployment-plan.md)을 참조 하세요.
| 그룹 할당 사용  | 구독에 포함 된 경우 그룹 소유자에 게 지속적인 액세스 관리를 위임할 수 있도록 응용 프로그램에 그룹을 할당 합니다. [응용 프로그램 통합을 위한 개발자 가이드](developer-guidance-for-integrating-applications.md)를 참조 하세요.   | 
| 인증서를 관리 하는 프로세스를 설정 합니다. | 서명 인증서의 최대 수명은 3 년입니다. 인증서가 만료 되어 가동 중단을 방지 하거나 최소화 하려면 역할 및 전자 메일 그룹을 사용 하 여 인증서 관련 변경 알림이 면밀 하 게 모니터링 되는지 확인 합니다. |

## <a name="provisioning-recommendations"></a>프로 비전 권장 사항
| 권장 | 주석 |
| --- | --- |
| 자습서를 사용 하 여 클라우드 앱으로 프로 비전 설정 | 추가 하려는 갤러리 앱의 프로 비전을 구성 하는 방법에 대 한 단계별 지침은 [SaaS 앱 자습서 목록을](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) 확인 하세요. |
| 프로 비전 로그 (미리 보기)를 사용 하 여 상태 모니터링 | [프로 비전 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 는 개별 사용자의 상태를 포함 하 여 프로 비전 서비스에서 수행 하는 모든 작업에 대 한 세부 정보를 제공 합니다. |
| 프로 비전 알림 전자 메일에 메일 그룹 할당 | 프로 비전 서비스에서 보낸 중요 한 경고의 표시 유형을 높이려면 메일 그룹을 알림 전자 메일 설정에 할당 합니다. |


## <a name="application-proxy-recommendations"></a>응용 프로그램 프록시 권장 사항
| 권장 | 주석 |
| --- | --- |
| 내부 리소스에 대 한 원격 액세스에 응용 프로그램 프록시 사용 | 응용 프로그램 프록시는 원격 사용자에 게 내부 리소스에 대 한 액세스 권한을 부여 하 여 VPN 또는 역방향 프록시에 대 한 필요성을 대체 하는 데 권장 됩니다. 대기 시간이 길어질 수 있으므로 회사 네트워크 내에서 리소스에 액세스 하기 위한 것이 아닙니다.
| 사용자 지정 도메인 사용 | 응용 프로그램에 대 한 사용자 지정 도메인을 설정 합니다 (사용자 [지정 도메인 구성](application-proxy-configure-custom-domain.md)참조). 사용자 및 응용 프로그램 간 url은 네트워크 내부 또는 외부에서 작동 합니다. 또한 브랜딩을 제어 하 고 Url을 사용자 지정할 수 있습니다.  사용자 지정 도메인 이름을 사용 하는 경우 Microsoft가 아닌 신뢰할 수 있는 인증 기관에서 공용 인증서를 획득 하도록 계획 합니다. Azure 애플리케이션 프록시는 표준, ([와일드 카드](application-proxy-wildcard.md)) 또는 SAN 기반 인증서를 지원 합니다. [응용 프로그램 프록시 계획](application-proxy-deployment-plan.md)을 참조 하세요. |
| 응용 프로그램 프록시를 배포 하기 전에 사용자 동기화 | 응용 프로그램 프록시를 배포 하기 전에 온-프레미스 디렉터리에서 사용자 id를 동기화 하거나 Azure AD에서 직접 만들어야 합니다. ID 동기화를 사용 하면 Azure AD에서 앱 프록시 게시 된 응용 프로그램에 대 한 액세스 권한을 부여 하기 전에 사용자를 사전 인증할 수 있습니다. 또한 SSO (Single Sign-On)를 수행 하는 데 필요한 사용자 id 정보를 제공 합니다. [응용 프로그램 프록시 계획](application-proxy-deployment-plan.md)을 참조 하세요. |
| 고가용성 및 부하 분산에 대 한 팁을 따르세요. | 사용자, 응용 프로그램 프록시 커넥터 및 백 엔드 앱 서버 간에 트래픽이 흐르는 방법을 알아보고 성능 및 부하 분산을 최적화 하기 위한 팁을 얻으려면 [응용 프로그램 프록시 커넥터 및 응용 프로그램의 고가용성 및 부하 분산](application-proxy-high-availability-load-balancing.md)을 참조 하세요. |
| 여러 커넥터 사용 | 복원 력, 가용성 및 확장성을 높이기 위해 둘 이상의 응용 프로그램 프록시 커넥터를 사용 합니다 ( [응용 프로그램 프록시 커넥터](application-proxy-connectors.md)참조). 커넥터 그룹을 만들고 각 커넥터 그룹에 두 개 이상의 커넥터가 있는지 확인 합니다 (3 개의 커넥터가 최적). |
| 응용 프로그램 서버에 가까운 커넥터 서버를 찾아 동일한 도메인에 있는지 확인 합니다. | 성능을 최적화 하려면 응용 프로그램 서버와 가까운 커넥터 서버를 물리적으로 찾습니다 ( [네트워크 토폴로지 고려 사항](application-proxy-network-topology.md)참조). 또한 커넥터 서버와 웹 응용 프로그램 서버는 동일한 Active Directory 도메인에 속하거나 트러스팅 도메인에 걸쳐 있어야 합니다. 이 구성은 IWA (Windows 통합 인증) 및 KCD (Kerberos 제한 위임)를 사용 하는 SSO에 필요 합니다. 서버가 서로 다른 도메인에 있는 경우 SSO에 대 한 리소스 기반 위임을 사용 해야 합니다 ( [응용 프로그램 프록시를 사용 하는 Single Sign-On의 경우 Kcd](application-proxy-configure-single-sign-on-with-kcd.md)참조). |
| 커넥터에 대 한 자동 업데이트 사용 | 최신 기능 및 버그 수정을 위해 커넥터에 대해 자동 업데이트를 사용 하도록 설정 합니다. Microsoft에서는 최신 커넥터 버전 및 이전 버전을 직접 지원 합니다. [응용 프로그램 프록시 릴리스 버전 기록](application-proxy-release-version-history.md)을 참조 하세요. |
| 온-프레미스 프록시 바이패스 | 더 쉽게 유지 관리 하려면 온-프레미스 프록시를 바이패스 하 여 Azure 서비스에 직접 연결 하도록 커넥터를 구성 합니다. [응용 프로그램 프록시 커넥터 및 프록시 서버](application-proxy-configure-connectors-with-proxy-servers.md)를 참조 하세요. |
| 웹 응용 프로그램 프록시를 통해 Azure AD 응용 프로그램 프록시 사용 | 대부분의 온-프레미스 시나리오에 Azure AD 응용 프로그램 프록시를 사용 합니다. 웹 응용 프로그램 프록시는 AD FS에 프록시 서버가 필요 하 고 Azure Active Directory에서 사용자 지정 도메인을 사용할 수 없는 시나리오 에서만 기본 설정 됩니다. [응용 프로그램 프록시 마이그레이션](application-proxy-migration.md)을 참조 하세요. |
