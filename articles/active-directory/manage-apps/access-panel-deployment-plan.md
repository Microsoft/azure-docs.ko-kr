---
title: 내 앱 배포 Azure Active Directory 계획
description: 내 앱 Azure Active Directory 배포에 대 한 지침
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: kenwith
ms.openlocfilehash: 0bff283b8e9c0c753100c635ecd4451b467c206d
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146626"
---
# <a name="plan-an-azure-active-directory-my-apps-deployment"></a>내 앱 배포 Azure Active Directory 계획

Azure Active Directory (Azure AD) 내 앱은 지원 비용을 절감 하 고, 생산성과 보안을 높이고, 사용자의 불만을 줄이는 데 도움이 되는 웹 기반 포털입니다. 시스템에는 시스템에 액세스할 때 추적 하는 상세 보고가 포함 되며 관리자에 게 오용 또는 남용 알림이 포함 됩니다. 최종 사용자 관점에서 내 앱을 사용 하는 방법에 대해 알아보려면 [내 앱 포털 도움말](../user-help/my-apps-portal-end-user-access.md)을 참조 하세요.

Azure AD 내 앱을 사용 하 여 다음을 수행할 수 있습니다.

* 모든 회사의 Azure AD 연결 리소스 (예: 응용 프로그램)를 검색 하 고 액세스 합니다.
* 새 앱 및 그룹에 대 한 액세스 요청
* 다른 사용자를 위해 이러한 리소스에 대 한 액세스 관리
* 셀프 서비스 암호 재설정 및 Azure Multi-Factor Authentication 설정 관리
* 장치 관리

또한 관리자는 다음을 관리할 수 있습니다.

* 서비스 약관
* 조직
* 액세스 검토


## <a name="benefits-of-azure-ad-my-apps-integration"></a>Azure AD 내 앱 통합의 이점

Azure AD 내 앱은 다음과 같은 방법으로 비즈니스를 활용 합니다.

**직관적인 사용자 환경 제공**: 내 앱은 모든 Azure SINGLE SIGN-ON (SSO) 연결 응용 프로그램에 대 한 단일 플랫폼을 제공 합니다. 추가 된 그룹 관리 및 셀프 서비스 암호 재설정과 같은 기존 설정 및 새로운 기능을 찾을 수 있는 통합 포털이 있습니다. 직관적인 환경을 사용 하면 사용자가 더 빨리 작업 하 고 생산성을 높일 수 있을 뿐 아니라 사용자의 불만을 줄일 수 있습니다.

**생산성 향상**: 내 앱의 모든 사용자 응용 프로그램에서 SSO를 사용 하도록 설정 했습니다. 엔터프라이즈 응용 프로그램 및 Microsoft 365에서 SSO를 사용 하도록 설정 하면 추가 로그인 프롬프트를 줄이거나 제거 하 여 뛰어난 로그인 환경을 만들 수 있습니다. 내 앱은 셀프 서비스 및 동적 멤버 자격을 사용 하 고 id 시스템의 전반적인 보안을 향상 시킵니다. 내 앱은 적절 한 사용자가 응용 프로그램에 대 한 액세스를 관리할 수 있도록 합니다. 내 앱은 신속 하 게 리소스를 찾고 작업을 계속할 수 있는 일관 된 방문 페이지로 사용 됩니다.

**비용 관리**: Azure AD에서 내 앱을 사용 하도록 설정 하면 온-프레미스 인프라의 divestment 도움이 될 수 있습니다. 모든 앱을 찾고, 리소스에 대 한 액세스를 요청 하 고, 계정을 관리 하는 일관 된 포털을 제공 하 여 지원 비용을 절감 합니다.

**유연성 및 보안 강화**: 내 앱은 클라우드 플랫폼에서 제공 하는 보안과 유연성에 대 한 액세스를 제공 합니다. 관리자는 응용 프로그램 및 리소스에 대 한 설정을 쉽게 변경할 수 있으며 사용자에 게 영향을 주지 않고 새로운 보안 요구 사항을 수용할 수 있습니다.

**강력한 감사 및 사용 추적 사용**: 모든 사용자 기능에 대 한 감사 및 사용 추적을 사용 하면 사용자가 자신의 리소스를 사용 하 고 있는지 확인 하 고 보안을 평가할 수 있습니다.

### <a name="licensing-considerations"></a>라이선스 고려 사항

내 앱은 무료 이며 기본 수준에서 사용할 라이선스가 필요 하지 않습니다. 그러나 디렉터리의 개체 수와 배포 하려는 추가 기능에는 추가 라이선스가 필요할 수 있습니다. 라이선스 요구 사항이 있는 몇 가지 일반적인 Azure AD 시나리오는 다음과 같은 보안 기능을 포함 합니다.

* [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-how-it-works)
* [그룹 기반 구성원 자격](https://docs.microsoft.com/azure/active-directory/active-directory-manage-groups)
* [셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
* [Azure Active Directory ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[AZURE AD에 대 한 전체 라이선스 가이드](https://azure.microsoft.com/pricing/details/active-directory/)를 참조 하세요.

### <a name="prerequisites-for-deploying-azure-ad-my-apps"></a>Azure AD 내 앱을 배포 하기 위한 필수 구성 요소

이 프로젝트를 시작 하기 전에 다음 필수 구성 요소를 완료 하십시오.

* [응용 프로그램 SSO 통합](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-sso-deployment)
* [Azure AD 사용자 및 그룹 인프라 관리](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)

## <a name="plan-azure-ad-my-apps-deployment"></a>Azure AD 내 앱 배포 계획

다음 표에서는 내 앱 배포에 대 한 주요 사용 사례를 간략하게 설명 합니다.

| 영역| 설명 |
| - | - |
| Access| 회사 네트워크 내의 회사 및 개인 장치에서 내 앱 포털에 액세스할 수 있습니다. |
|Access | 회사 네트워크 외부의 회사 장치에서 내 앱 포털에 액세스할 수 있습니다. |
| 감사| 사용 현황 데이터는 최소 29 일 마다 회사 시스템에 다운로드 됩니다. |
| 거버넌스| Azure AD에 연결 된 응용 프로그램 및 그룹에 대 한 사용자 할당의 수명 주기는 정의 되 고 모니터링 됩니다. |
| 보안| 리소스에 대 한 액세스는 사용자 및 그룹 할당을 통해 제어 됩니다. 권한 있는 사용자만 리소스 액세스를 관리할 수 있습니다. |
| 성능| 액세스 할당 전파 타임 라인은 문서화 및 모니터링 됩니다. |
| 사용자 환경| 사용자는 내 앱 기능 및 사용 방법을 알고 있습니다.|
| 사용자 환경| 사용자는 응용 프로그램 및 그룹에 대 한 액세스를 관리할 수 있습니다.|
| 사용자 환경| 사용자는 자신의 계정을 관리할 수 있습니다. |
| 사용자 환경| 사용자는 브라우저 호환성을 인식 합니다. |
| 지원| 사용자는 내 앱 문제에 대 한 지원을 찾을 수 있습니다. |


> [!TIP]
> 응용 프로그램 프록시를 사용 하 여 원격으로 내 앱을 내부 회사 Url과 함께 사용할 수 있습니다. 자세한 내용은 [자습서: 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가 Azure Active Directory](application-proxy-add-on-premises-application.md)를 참조 하세요.

### <a name="best-practices-for-deploying-azure-ad-my-apps"></a>Azure AD 내 앱 배포에 대 한 모범 사례

내 앱의 기능을 점진적으로 사용 하도록 설정할 수 있습니다. 다음 배포 순서를 권장 합니다.

1. 내 앱
   * 앱 시작 관리자
   * 셀프 서비스 앱 관리
   * Microsoft 365 통합

1. 셀프 서비스 앱 검색
   * 셀프 서비스 암호 재설정
   * Multi-Factor Authentication 설정
   * 디바이스 관리
   * 사용 약관
   * 조직 관리

1. 내 그룹
   * 셀프 서비스 그룹 관리
1. 액세스 검토
   * 액세스 검토 관리

내 앱에서 시작 하 여 리소스에 액세스 하는 일반적인 장소로 포털에 사용자를 소개 합니다. 셀프 서비스 응용 프로그램 검색을 추가 하는 것은 내 앱 환경을 기반으로 합니다. 내 그룹 및 액세스 검토는 셀프 서비스 기능을 기반으로 합니다.

### <a name="plan-configurations-for-azure-my-apps"></a>Azure 내 앱에 대 한 구성 계획

다음 표에서는 몇 가지 중요 한 내 앱 구성 및 사용할 수 있는 일반적인 값을 보여 줍니다.

| Configuration| 일반적인 값 |
| - | - |
| 파일럿 그룹 결정| 사용할 Azure AD 보안 그룹을 식별 하 고 모든 파일럿 구성원이 그룹에 속해 있는지 확인 합니다. |
| 프로덕션에 사용할 그룹을 결정 합니다.| 사용할 azure AD 보안 그룹 또는 Azure AD에 동기화 된 Active Directory 그룹을 식별 합니다. 모든 파일럿 구성원이 그룹의 일부 인지 확인 합니다. |
| 사용자가 특정 유형의 응용 프로그램에 SSO를 사용할 수 있도록 허용| 페더레이션된 SSO, OAuth, 암호 SSO, 앱 프록시 |
| 사용자가 셀프 서비스 암호 재설정을 사용 하도록 허용 | 예 |
| 사용자가 Multi-Factor Authentication을 사용할 수 있도록 허용| 예 |
| 사용자가 특정 유형의 그룹에 대해 셀프 서비스 그룹 관리를 사용 하도록 허용| 보안 그룹, Microsoft 365 그룹 |
| 사용자가 셀프 서비스 앱 관리를 사용할 수 있도록 허용| 예 |
| 사용자가 액세스 검토를 사용할 수 있도록 허용| 예 |

### <a name="plan-consent-strategy"></a>계획 승인 전략

사용자 또는 관리자는 모든 응용 프로그램의 사용 약관 및 개인 정보 취급 방침에 동의 해야 합니다. 가능 하면 비즈니스 규칙에 따라 사용자에 게 더 나은 환경을 제공 하는 관리자 동의를 사용 합니다.

관리자 동의를 사용 하려면 조직의 전역 관리자 여야 하며 응용 프로그램은 다음 중 하나 여야 합니다.

* 조직에 등록 됨
* 다른 Azure AD 조직에 등록 되었으며 이전에 한 명 이상의 사용자가 동의한

자세한 내용은 [Azure Active Directory에서 최종 사용자가 응용 프로그램에 동의 하는 방식 구성](configure-user-consent.md)을 참조 하세요.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생 하는 경우 일반적으로 영향, 결과 및 책임에 대 한 예상치가 일치 하지 않기 때문에이 작업을 수행 합니다. 이러한 문제를 방지 하려면 [올바른 관련자에](../fundamentals/active-directory-deployment-plans.md) 게 참여 하 고 프로젝트의 관련자 역할을 잘 이해 하 고 있어야 합니다.

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자에 게 경험을 언제 어떻게 변경 하 고 필요한 경우 지원을 얻는 지를 사전에 알려 줍니다.

앱은 일반적으로 사용자 문제를 만들지 않지만 준비 하는 것이 중요 합니다. 시작 하기 전에 지원 담당자의 모든 리소스 목록 및 가이드를 만듭니다.

#### <a name="communications-templates"></a>통신 템플릿

Microsoft는 [전자 메일에 대 한 사용자 지정 가능한 템플릿과](https://aka.ms/APTemplates) 내 앱에 대 한 기타 통신을 제공 합니다. 회사 문화에 맞게 다른 통신 채널에서 사용 하기 위해 이러한 자산을 조정할 수 있습니다.

## <a name="plan-your-sso-configuration"></a>SSO 구성 계획

사용자가 응용 프로그램에 로그인 하면 인증 프로세스를 거치 며 사용자를 증명 하는 데 필요 합니다. SSO를 사용 하지 않으면 암호는 응용 프로그램에 저장 되 고 사용자는이 암호를 알고 있어야 합니다. SSO를 사용 하면 사용자 자격 증명이 응용 프로그램에 전달 되므로 각 응용 프로그램에 대 한 암호를 다시 입력할 필요가 없습니다.

내 앱에서 응용 프로그램을 시작 하려면 SSO를 사용 하도록 설정 해야 합니다. Azure AD는 여러 SSO 옵션을 지원 합니다. 자세히 알아보려면 [AZURE AD의 Single sign-on 옵션](sso-options.md)을 참조 하세요.

> [!NOTE]
> 앱에 대 한 id 공급자로 Azure AD를 사용 하는 방법에 대 한 자세한 내용은 [응용 프로그램 관리에](view-applications-portal.md)대 한 빠른 시작 시리즈를 참조 하세요.

내 앱 페이지에서 최상의 경험을 위해 페더레이션된 SSO에 사용할 수 있는 클라우드 응용 프로그램의 통합을 시작 합니다. 페더레이션된 SSO를 사용 하면 사용자가 앱을 시작 하는 일관 된 한 번 클릭 환경을 제공 하 고 구성 제어에서 더욱 강력해 집니다.

암호 기반 SSO 및 ADFS 대신 응용 프로그램에서 지원할 때 Azure AD (Openid connect Connect/SAML)에서 페더레이션된 SSO를 사용 합니다.

SaaS 응용 프로그램을 배포 하 고 구성 하는 방법에 대 한 자세한 내용은 [SAAS SSO 배포 계획](https://aka.ms/deploymentplans/sso)을 참조 하세요.

#### <a name="plan-to-deploy-the-my-apps-browser-extension"></a>My Apps 브라우저 확장 배포 계획

사용자가 암호 기반 SSO 응용 프로그램에 로그인 하는 경우 내 앱 보안 로그인 확장을 설치 하 고 사용 해야 합니다. 확장은 응용 프로그램의 로그인 양식에 암호를 전송 하는 스크립트를 실행 합니다. 사용자가 암호 기반 SSO 응용 프로그램을 처음 시작할 때 확장을 설치 하 라는 메시지가 표시 됩니다. 확장에 대 한 자세한 내용은이 설명서에서 [내 앱 브라우저 확장 설치](access-panel-extension-problem-installing.md)에 나와 있습니다.

암호 기반 SSO 응용 프로그램을 통합 해야 하는 경우 [지원 되는 브라우저](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 사용 하 여 확장을 대규모로 배포 하는 메커니즘을 정의 해야 합니다. 다음 옵션을 사용할 수 있습니다.

* [Internet Explorer에 대 한 그룹 정책](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
* [Internet Explorer에 대 한 Configuration Manager](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
* [Chrome, Firefox, Microsoft Edge 또는 IE에 대 한 사용자 구동 다운로드 및 구성](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

암호 기반 SSO 응용 프로그램을 사용 하지 않는 사용자는 확장의 이점을 누릴 수 있습니다. 이러한 혜택에는 검색 표시줄에서 앱을 시작 하 고 최근에 사용한 응용 프로그램에 대 한 액세스를 찾고 내 앱 페이지에 대 한 링크를 포함 하는 기능이 포함 됩니다.

#### <a name="plan-for-mobile-access"></a>모바일 액세스 계획

모바일 사용자가 암호 기반 SSO 응용 프로그램을 시작 하려면 Intune 정책 (Microsoft Edge 또는 Intune Managed Browser)으로 보호 된 브라우저가 필요 합니다. 정책으로 보호 된 브라우저를 사용 하면 응용 프로그램에 대해 저장 된 암호를 전송할 수 있습니다. Microsoft Edge 또는 managed browser는 웹 데이터 보호 기능 집합을 제공 합니다. IOS 및 Android 장치에서 엔터프라이즈 시나리오용 Microsoft Edge를 사용할 수도 있습니다. Microsoft Edge는 Intune Managed Browser와 동일한 관리 시나리오를 지원 하 고 사용자 환경을 개선 합니다. 자세한 정보: [정책으로 보호 된 Microsoft Intune 브라우저를 사용 하 여 웹 액세스 관리](https://docs.microsoft.com/intune/app-configuration-managed-browser)

## <a name="plan-your-my-apps-deployment"></a>내 앱 배포 계획

내 앱의 기반은 사용자가에서 액세스 하는 응용 프로그램 시작 관리자 포털입니다 [https://myapps.microsoft.com](https://myapps.microsoft.com/) . 내 앱 페이지에서는 사용자에 게 작업을 시작 하 고 필요한 응용 프로그램에 액세스할 수 있는 단일 장소를 제공 합니다. 여기서 사용자는 액세스 Single Sign-On 있는 모든 응용 프로그램의 목록을 찾을 수 있습니다. 

> [!NOTE]
> 동일한 응용 프로그램이 Microsoft 365 앱 시작 관리자에 표시 됩니다.

내 앱 시작 관리자에 응용 프로그램을 추가 하는 순서를 계획 하 고 점진적으로 또는 한꺼번에 롤아웃할 지 여부를 결정 합니다. 이렇게 하려면 인증 유형 및 각 응용 프로그램에 대 한 모든 기존 SSO 통합을 나열 하는 응용 프로그램 인벤토리를 만듭니다.

#### <a name="add-applications-to-the-my-apps-panel"></a>내 앱 패널에 응용 프로그램 추가

모든 Azure AD SSO 사용 응용 프로그램을 My Apps 시작 관리자에 추가할 수 있습니다. 다른 응용 프로그램은 연결 된 SSO 옵션을 사용 하 여 추가 됩니다. 기존 웹 응용 프로그램의 URL에 연결 되는 응용 프로그램 타일을 구성할 수 있습니다. 연결 된 SSO를 사용 하면 모든 응용 프로그램을 Azure AD SSO로 마이그레이션하지 않고도 My Apps 포털로 사용자의 연결을 시작할 수 있습니다. 사용자 환경을 방해 하지 않고 Azure AD SSO 구성 응용 프로그램으로 점진적으로 이동할 수 있습니다.

#### <a name="use-my-apps-collections"></a>내 앱 컬렉션 사용

기본적으로 모든 응용 프로그램은 단일 페이지에 함께 나열 됩니다. 그러나 컬렉션을 사용 하 여 관련 응용 프로그램을 함께 그룹화 하 고 별도의 탭에 표시 하 여 쉽게 찾을 수 있습니다. 예를 들어 컬렉션을 사용 하 여 특정 작업 역할, 작업, 프로젝트 등에 대해 응용 프로그램의 논리적 그룹을 만들 수 있습니다. 자세한 내용은 [내 앱 컬렉션을 사용 하는 방법](access-panel-collections.md)을 참조 하세요. 

#### <a name="plan-whether-to-use-my-apps-or-an-existing-portal"></a>내 앱 또는 기존 포털의 사용 여부 계획

사용자에 게 내 앱이 아닌 응용 프로그램이 나 포털이 이미 있을 수 있습니다. 그렇다면 두 포털을 모두 지원 하거나 하나만 사용할지 결정 합니다.

사용자의 시작 지점으로 기존 포털을 이미 사용 하 고 있는 경우 사용자 액세스 Url을 사용 하 여 내 앱 기능을 통합할 수 있습니다. 사용자 액세스 Url은 내 앱 포털에서 사용할 수 있는 응용 프로그램에 대 한 직접 링크 처럼 작동 합니다. 이러한 Url은 기존 웹 사이트에 포함 될 수 있습니다. 사용자가 링크를 선택 하면 내 앱 포털에서 응용 프로그램이 열립니다.

사용자 액세스 URL 속성은 Azure Portal 응용 프로그램의 **속성** 영역에서 찾을 수 있습니다.

![앱 패널의 스크린샷](media/access-panel-deployment-plan/ap-dp-user-access-url.png)


## <a name="plan-self-service-application-discovery-and-access"></a>셀프 서비스 응용 프로그램 검색 및 액세스 계획

응용 프로그램의 핵심 집합이 사용자의 내 앱 페이지에 배포 되 면 셀프 서비스 앱 관리 기능을 사용 하도록 설정 해야 합니다. 셀프 서비스 앱 검색을 통해 사용자는 다음을 수행할 수 있습니다.

* 내 앱에 추가할 새 앱을 찾습니다. 
* 설치 중에 필요 하지 않을 수 있는 선택적 앱을 추가 합니다.

승인 워크플로를 사용 하 여 응용 프로그램에 대 한 액세스를 명시적으로 승인할 수 있습니다. 승인자 인 사용자는 응용 프로그램에 대 한 액세스 요청을 보류 중인 경우 내 앱 포털 내에서 알림을 받게 됩니다.

## <a name="plan-self-service-group-membership"></a>셀프 서비스 그룹 구성원 자격 계획 

사용자가 Azure AD에서 자신의 보안 그룹 또는 Microsoft 365 그룹을 만들고 관리할 수 있도록 설정할 수 있습니다. 그룹 소유자는 멤버 자격 요청을 승인 또는 거부 하 고 그룹 멤버 자격에 대 한 제어를 위임할 수 있습니다. 메일 사용이 가능한 보안 그룹이 나 메일 그룹에는 셀프 서비스 그룹 관리 기능을 사용할 수 없습니다.

셀프 서비스 그룹 멤버 자격에 대 한 계획을 수립 하려면 조직의 모든 사용자가 그룹을 만들고 관리할 수 있도록 허용할지 또는 사용자의 하위 집합만 허용할지 결정 합니다. 사용자의 하위 집합을 허용 하는 경우 해당 사용자가 추가 되는 그룹을 설정 해야 합니다. 이러한 시나리오를 사용 하는 방법에 대 한 자세한 내용은 [Azure Active Directory의 셀프 서비스 그룹 관리 설정](../users-groups-roles/groups-self-service-management.md) 을 참조 하세요.

## <a name="plan-reporting-and-auditing"></a>보고 및 감사 계획

Azure AD [는 기술 및 비즈니스 정보를 제공 하는 보고서를](../reports-monitoring/overview-reports.md)제공 합니다. 비즈니스 및 기술 응용 프로그램 소유자와 협력 하 여 이러한 보고서의 소유권을 가정 하 고 정기적으로 사용 합니다. 다음 표에서는 일반적인 보고 시나리오에 대 한 몇 가지 예를 제공 합니다.

| 예제 | 위험 관리| 생산성 향상| 거버넌스 및 규정 준수 |
|  - |- | - | - |
| 보고서 유형|  응용 프로그램 사용 권한 및 사용| 계정 프로비전 활동| 응용 프로그램에 액세스 하는 사용자 검토 |
| 잠재적 작업| 감사 액세스 권한 해지| 프로 비전 오류 수정| 액세스 취소 |

Azure AD는 30 일 동안 대부분의 감사 데이터를 유지 합니다. 분석 시스템으로 다운로드 하기 위해 Azure 관리 포털 또는 API를 통해 데이터를 사용할 수 있습니다.

#### <a name="auditing"></a>감사

응용 프로그램 액세스에 대 한 감사 로그는 30 일 동안 사용할 수 있습니다. 엔터프라이즈 내의 보안 감사에 더 긴 보존이 필요한 경우 로그를 SIEM (보안 정보 이벤트 및 관리) 도구 (예: Splunk 또는 ArcSight)로 내보내야 합니다.

감사, 보고 및 재해 복구 백업의 경우 필요한 다운로드 빈도, 대상 시스템 정의 및 각 백업 관리를 담당 하는 사용자를 문서화 합니다. 별도의 감사 및 보고 백업이 필요 하지 않을 수 있습니다. 재해 복구 백업은 별도의 엔터티입니다.

## <a name="deploy-applications-to-users-my-apps-panel"></a>사용자의 내 앱 패널에 응용 프로그램 배포

응용 프로그램이 SSO를 사용 하도록 구성 된 후에는 그룹에 액세스 권한이 할당 됩니다. 할당 된 그룹의 사용자는 액세스 권한을 갖게 되 고 내 앱 및 Microsoft 365 앱 시작 관리자에 응용 프로그램이 표시 됩니다.

[Active Directory에서 응용 프로그램에 사용자 및 그룹 할당을](methods-for-assigning-users-and-groups.md)참조 하세요.

테스트 또는 배포 중에 그룹을 추가 하려고 하지만 응용 프로그램이 내 앱에 표시 되는 것을 허용 하지 않는 경우 [Azure Active Directory의 사용자 환경에서 응용 프로그램 숨기기](hide-application-from-user-portal.md)를 참조 하세요.

### <a name="deploy-microsoft-365-applications-to-my-apps"></a>내 앱에 Microsoft 365 응용 프로그램 배포

Microsoft 365 응용 프로그램의 경우 사용자는 할당 된 라이선스에 따라 Office 복사본을 받습니다. Office 응용 프로그램에 액세스 하기 위한 필수 구성 요소는 Office 응용 프로그램에 연결 된 올바른 라이선스를 사용자에 게 할당 하는 것입니다. 사용자에 게 라이선스를 할당 하면 해당 라이선스에 연결 된 응용 프로그램이 내 앱 페이지 및 Microsoft 365 앱 시작 관리자에서 자동으로 표시 됩니다.

사용자의 Office 응용 프로그램 집합을 숨기려면 내 앱 포털에서 앱을 숨기고 Microsoft 365 포털에서 계속 액세스할 수 있도록 하는 옵션이 있습니다. 자세한 정보: [Azure Active Directory의 사용자 환경에서 응용 프로그램을 숨깁니다](hide-application-from-user-portal.md).

### <a name="deploy-application-self-service-capabilities"></a>응용 프로그램 셀프 서비스 기능 배포

셀프 서비스 응용 프로그램 액세스를 통해 사용자는 응용 프로그램에 대 한 액세스를 직접 검색 하 고 요청할 수 있습니다. 사용자는 액세스를 요청할 때마다 IT 그룹을 거치지 않고 필요한 앱에 자유롭게 액세스할 수 있습니다. 사용자가 액세스를 요청 하 고 앱 소유자가 자동으로 또는 수동으로 승인 하는 경우 백 엔드의 그룹에 추가 됩니다. 보고는 액세스를 요청, 승인 또는 제거 하는 사용자에 게 사용 하도록 설정 되며, 할당 된 역할 관리에 대 한 제어를 제공 합니다.

비즈니스 승인자에 게 응용 프로그램 액세스 요청에 대 한 승인을 위임할 수 있습니다. 비즈니스 승인자는 비즈니스 승인자의 내 앱 페이지에서 앱 액세스 암호를 설정할 수 있습니다.

자세한 정보: [셀프 서비스 응용 프로그램 액세스를 사용 하는 방법](access-panel-manage-self-service-access.md)

## <a name="validate-your-deployment"></a>배포 유효성 검사

내 앱 배포가 철저 하 게 테스트 되었고 롤백 계획이 준비 되어 있는지 확인 합니다.

회사 소유 장치와 개인 장치 모두에서 다음 테스트를 수행 해야 합니다. 이러한 테스트 사례에도 비즈니스 사용 사례가 반영 되어야 합니다. 다음은이 문서의 샘플 비즈니스 요구 사항과 일반적인 기술 시나리오를 기반으로 하는 몇 가지 사례입니다. 필요에 따라 다른 사용자를 추가 합니다.

#### <a name="application-sso-access-test-case-examples"></a>응용 프로그램 SSO 액세스 테스트 사례 예:


| 비즈니스 사례| 예상된 결과 |
| - | -|
| 사용자가 내 앱 포털에 로그인| 사용자가 로그인 하 여 응용 프로그램을 볼 수 있음 |
| 사용자가 페더레이션된 SSO 응용 프로그램을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 처음으로 암호 SSO 응용 프로그램을 시작 합니다.| 사용자가 내 앱 확장을 설치 해야 함 |
| 사용자가 다음 번에 암호 SSO 응용 프로그램을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 Microsoft 365 포털에서 앱을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |
| 사용자가 Managed Browser에서 앱을 시작 합니다.| 사용자가 응용 프로그램에 자동으로 로그인 됩니다. |


#### <a name="application-self-service-capabilities-test-case-examples"></a>응용 프로그램 셀프 서비스 기능 테스트 사례 예제

| 비즈니스 사례| 예상된 결과 |
| - | - |
| 사용자가 응용 프로그램에 대 한 멤버 자격을 관리할 수 있음| 사용자는 앱에 대 한 액세스 권한이 있는 구성원을 추가/제거할 수 있습니다. |
| 사용자가 응용 프로그램을 편집할 수 있습니다.| 사용자는 암호 SSO 응용 프로그램에 대 한 응용 프로그램의 설명 및 자격 증명을 편집할 수 있습니다. |

### <a name="rollback-steps"></a>롤백 단계

배포가 계획대로 진행되지 않는 경우 수행할 작업을 계획하는 것이 중요합니다. 배포 하는 동안 SSO 구성이 실패할 경우 [sso 문제를 해결](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sso) 하 고 사용자에 대 한 영향을 줄이는 방법을 이해 해야 합니다. 극단적인 경우 [SSO를 롤백해야](../manage-apps/plan-sso-deployment.md#rollback-process)할 수 있습니다.


## <a name="manage-your-implementation"></a>구현 관리

최소 권한 역할을 사용 하 여 Azure Active Directory 내에서 필요한 작업을 수행 합니다. [사용 가능한 다른 역할을 검토](../users-groups-roles/directory-assign-admin-roles.md) 하 고 올바른 역할을 선택 하 여이 응용 프로그램의 각 가상 사용자에 대 한 요구 사항을 해결 합니다. 일부 역할은 배포를 완료 한 후 임시로 적용 해야 할 수도 있습니다.

| 가상 사용자| 역할| Azure AD 역할  |
| - | -| -|
| 기술 지원팀 관리자| 계층 1 지원| 없음 |
| Id 관리| 문제가 Azure AD에 영향을 주는 경우 구성 및 디버그| 글로벌 관리자 |
| 응용 프로그램 관리자| 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |
| 인프라 관리자| 인증서 롤오버 소유자| 글로벌 관리자 |
| 비즈니스 소유자/관련자| 응용 프로그램의 사용자 증명, 권한이 있는 사용자의 구성| 없음 |

[Privileged Identity Management](../privileged-identity-management/pim-configure.md) 를 사용 하 여 디렉터리 권한이 있는 사용자에 게 추가 감사, 제어 및 액세스 검토를 제공할 역할을 관리할 수 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure Multi-Factor Authentication 배포 계획](https://aka.ms/deploymentplans/mfa)
