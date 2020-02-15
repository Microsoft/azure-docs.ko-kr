---
title: Azure AD 하이브리드 id 솔루션에 대 한 인증
titleSuffix: Active Directory
description: 이 가이드는 CEO, CIO, CISO, CIA(Chief Identity Architects), 엔터프라이즈 설계자 그리고 보안 및 IT 의사 결정자 등, 중소 규모 기업 또는 대규모 기업에서 Azure AD 하이브리드 ID 솔루션의 인증 방법 선택을 담당하는 이들에게 유용합니다.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: 7a13e50e6b5357264e7096fa58e34a4bcaf6ad8b
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77190906"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Azure Active Directory 하이브리드 ID 솔루션에 적합한 인증 방법 선택

올바른 인증 방법을 선택하는 것은 앱에서 클라우드로 이동하려는 조직이 첫 번째로 고려해야 할 사항입니다. 이 결정을 가볍게 여겨서는 안 되는 이유는 다음과 같습니다.

1. 클라우드로 이동하려는 조직에서 내려야 하는 첫 번째 의사 결정입니다.

2. 인증 방법은 클라우드에서 조직의 존재 여부에 매우 중요한 구성 요소이며, 모든 클라우드 데이터와 리소스에 대한 액세스를 제어합니다.

3. Azure AD에서 모든 다른 고급 보안 및 사용자 경험 기능의 기초가 됩니다.

Id는 IT 보안의 새로운 제어 평면 이므로 인증은 새 클라우드 세계에 대 한 조직의 액세스 보호입니다. 조직에서는 보안을 강화하고 클라우드 앱을 침입자들로부터 안전하게 보호하는 ID 제어 평면을 필요로 합니다.

> [!NOTE]
> 인증 방법을 변경 하려면 계획, 테스트 및 잠재적 가동 중지 시간이 필요 합니다. [단계적 출시](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) 는 페더레이션에서 클라우드 인증으로 테스트 하 고 점진적으로 마이그레이션하는 좋은 방법입니다.

### <a name="out-of-scope"></a>범위 외
기존 온-프레미스 디렉터리 공간이 없는 조직은 이 문서의 대상이 아닙니다. 일반적으로 이러한 기업은 클라우드에서만 ID를 생성하므로 하이브리드 ID 솔루션이 필요하지 않습니다. 클라우드 전용 ID는 클라우드에서만 존재하며 해당 온-프레미스 ID와 연결되지 않습니다.

## <a name="authentication-methods"></a>인증 방법
Azure AD 하이브리드 ID 솔루션이 새 제어 평면인 경우 인증은 클라우드 액세스의 기초입니다. 따라서 올바른 인증 방법을 선택하는 것은 Azure AD 하이브리드 ID 솔루션 설정에서 매우 중요한 첫 번째 결정입니다. 클라우드에서 사용자를 프로비저닝하기도 하는 Azure AD Connect를 사용하여 구성되는 인증 방법을 구현하세요.

인증 방법을 선택하려면 시간, 기존 인프라, 복잡성 및 선택을 구현하는 데 드는 비용을 고려해야 합니다. 이러한 요소는 조직마다 다르며 시간이 흐름에 따라 변경될 수 있습니다.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Azure AD에서는 하이브리드 ID 솔루션에 대해 다음과 같은 인증 방법을 지원합니다.

### <a name="cloud-authentication"></a>클라우드 인증
이 인증 방법을 선택하면 Azure AD가 사용자의 로그인 프로세스를 처리합니다. 원활한 SSO(Single Sign-On)와 결합되어 있으므로, 사용자는 자격 증명을 다시 입력하지 않고도 클라우드 앱에 로그인할 수 있습니다. 클라우드 인증을 사용할 경우 다음 두 가지 옵션 중에서 선택할 수 있습니다.

**Azure AD 암호 해시 동기화**. Azure AD에서 온-프레미스 디렉터리 개체에 대한 인증을 사용하는 가장 간단한 방법입니다. 사용자는 추가 인프라를 배포하지 않고도 온-프레미스에서 사용하는 것과 동일한 사용자 이름과 암호를 사용할 수 있습니다. Id 보호 및 [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)같은 Azure AD의 일부 프리미엄 기능에는 선택한 인증 방법에 관계 없이 암호 해시 동기화가 필요 합니다.

> [!NOTE]
> 암호는 일반 텍스트로 저장되거나 Azure AD에서 복원 가능한 알고리즘을 사용하여 암호화되지 않습니다. 암호 해시 동기화의 실제 프로세스에 대한 자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 구현](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)을 참조하세요.

**Azure AD 통과 인증**. 하나 이상의 온-프레미스 서버에서 실행되는 소프트웨어 에이전트를 사용하여 Azure AD 인증 서비스에 대한 간단한 암호 유효성 검사를 제공합니다. 서버가 온-프레미스 Active Directory에서 직접 사용자를 검증하므로 클라우드에서는 암호 유효성 검사가 수행되지 않습니다.

온-프레미스 사용자 계정 상태, 암호 정책 및 로그인 시간을 즉시 적용해야 하는 보안 요구 사항이 있는 회사에서 이 인증 방법을 사용할 수 있습니다. 실제 통과 인증 프로세스에 대한 자세한 내용은 [Azure AD 통과 인증으로 사용자 로그인](../../active-directory/hybrid/how-to-connect-pta.md)을 참조하세요.

### <a name="federated-authentication"></a>페더레이션 인증
이 인증 방법을 선택하면 Azure AD에서 인증 프로세스를 온-프레미스 AD FS(Active Directory Federation Services) 등과 같은 별도의 신뢰할 수 있는 인증 시스템에 넘겨서 사용자 암호의 유효성을 검사합니다.

인증 시스템에서 스마트 카드 기반 인증 또는 타사 다단계 인증 등의 추가 고급 인증 요구 사항을 제공할 수 있습니다. 자세한 내용은 [Active Directory Federation Services 배포](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide)를 참조하세요.

다음 섹션은 의사 결정 트리를 사용하여 적합한 인증 방법을 결정하는 데 도움이 됩니다. 즉, Azure AD 하이브리드 ID 솔루션에 대해 클라우드 인증을 배포할지 또는 페더레이션 인증을 배포할지 결정하는 데 도움이 됩니다.

## <a name="decision-tree"></a>의사 결정 트리

![Azure AD 인증 의사 결정 트리](./media/choose-ad-authn/azure-ad-authn-image1.png)

의사 결정 질문에 대한 세부 정보:

1. Azure AD는 암호를 확인하기 위해 온-프레미스 구성 요소에 의존하지 않고도 사용자 로그인을 처리할 수 있습니다.
2. Azure AD는 Microsoft의 AD FS처럼 신뢰할 수 있는 인증 공급자에게 사용자 로그인을 넘겨줄 수 있습니다.
3. 계정 만료, 계정 사용 중지, 암호 만료, 계정 잠김, 각 사용자 로그인의 로그인 시간 등의 사용자 수준 Active Directory 보안 정책을 적용해야 하는 경우 Azure AD에 몇 가지 온-프레미스 구성 요소가 필요합니다.
4. 다음은 Azure AD에서 기본적으로 지원되지 않는 로그인 기능입니다.
   * 스마트 카드 또는 인증서를 사용하여 로그인.
   * 온-프레미스 MFA 서버를 사용하여 로그인.
   * 타사 인증 솔루션을 사용 하 여 로그인 합니다.
   * 다중 사이트 온-프레미스 인증 솔루션.
5. Azure AD ID 보호는 선택한 로그인 방법에 관계 없이 *자격 증명이 유출된 사용자* 보고서를 제공해야 하므로 암호 해시 동기화가 필요합니다. 조직에서는 기본 로그인 메서드가 실패 하 고 실패 이벤트 이전에 구성 된 경우 암호 해시 동기화로 장애 조치 (failover) 할 수 있습니다.

> [!NOTE]
> Azure AD ID 보호에는 [Azure AD Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) 라이선스가 필요합니다.

## <a name="detailed-considerations"></a>세부 고려 사항

### <a name="cloud-authentication-password-hash-synchronization"></a>클라우드 인증: 암호 해시 동기화

* **활동**. 암호 해시 동기화는 배포, 유지 관리 및 인프라와 관련한 최소한의 활동을 필요로 합니다.  이 활동 수준은 일반적으로 사용자가 Office 365, SaaS 앱 및 기타 Azure AD 기반 리소스에 로그인만 하면 되는 조직에 적용됩니다. 설정된 경우, 암호 해시 동기화는 Azure AD Connect 동기화 프로세스의 일부로 2분마다 실행됩니다.

* **사용자 환경**. 사용자의 로그인 환경을 개선하려면 암호 해시 동기화를 사용하는 원활한 SSO를 배포합니다. 원활한 SSO는 사용자가 로그인한 후 불필요한 프롬프트를 방지합니다.

* **고급 시나리오**. 조직에서 원하는 경우, Azure AD Premium P2가 있으면 손실된 자격 증명 보고서 등의 Azure AD ID 보호 보고서에서 ID의 정보를 사용할 수 있습니다. 비즈니스용 Windows Hello에는 [암호 해시 동기화를 사용 하는 경우 특정 요구 사항이](https://docs.microsoft.com/windows/access-protection/hello-for-business/hello-identity-verification)있습니다. [Azure AD Domain Services](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md) 는 관리 되는 도메인에서 사용자를 회사 자격 증명으로 프로 비전 하기 위해 암호 해시 동기화가 필요 합니다.

    암호 해시 동기화를 사용 하는 다단계 인증을 필요로 하는 조직은 Azure AD 다단계 인증 또는 [조건부 액세스 사용자 지정 컨트롤](../../active-directory/conditional-access/controls.md#custom-controls-preview)을 사용 해야 합니다. 이러한 조직에서는 페더레이션에 의존 하는 타사 또는 온-프레미스 다단계 인증 방법을 사용할 수 없습니다.

> [!NOTE]
> Azure AD 조건부 액세스를 사용 하려면 [Azure AD Premium P1](https://azure.microsoft.com/pricing/details/active-directory/) 라이선스가 필요 합니다.

* **비즈니스 연속성**. 클라우드 인증과 함께 암호 해시 동기화를 사용하면 기본적으로 모든 Microsoft 데이터 센터로 확장되는 클라우드 서비스만큼 가용성이 높습니다. 연장된 기간 동안 암호 해시 동기화가 계속 작동하도록 하려면 보조 Azure AD Connect 서버를 대기 구성으로 준비 모드에서 배포하는 것이 좋습니다.

* **고려 사항**. 암호 해시 동기화는 현재 온-프레미스 계정 상태에서 변경 내용을 즉시 적용하지 않습니다. 이 경우 사용자는 사용자 계정 상태가 Azure AD와 동기화될 때까지 클라우드 앱에 대한 액세스 권한을 가집니다. 조직에서는 관리자가 계정을 사용하지 않도록 설정하는 등 온-프레미스 사용자 계정 상태에 대한 대량 업데이트를 수행한 후 새 동기화 주기를 실행하여 이 제한을 해결하려고 할 수 있습니다.

> [!NOTE]
> 암호 만료 및 계정 잠김 상태는 현재, Azure AD Connect를 사용하여 Azure AD에 동기화되지 않습니다. 사용자 암호를 변경 하 고 *사용자가 다음 로그온 할 때 반드시 암호 변경* 플래그를 설정 하는 경우 암호 해시는 사용자가 암호를 변경할 때까지 Azure AD Connect를 사용 하 여 Azure AD와 동기화 되지 않습니다.

배포 단계에 대해서는 [암호 해시 동기화 구현](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)을 참조하세요.

### <a name="cloud-authentication-pass-through-authentication"></a>클라우드 인증: 통과 인증  

* **활동**. 통과 인증에서는 하나 이상(3개 권장)의 경량 에이전트가 기존 서버에 설치되어 있어야 합니다. 이러한 에이전트는 온-프레미스 AD 도메인 컨트롤러를 포함하여 온-프레미스 Active Directory Domain Services에 대한 액세스 권한이 있어야 하며, 인터넷에 대한 아웃바운드 액세스 및 사용자 도메인 컨트롤러에 대한 액세스가 필요합니다. 이러한 이유로 경계 네트워크에 에이전트를 배포하는 것은 지원되지 않습니다.

    통과 인증에는 도메인 컨트롤러에 대한 무제한 네트워크 액세스가 필요합니다. 모든 네트워크 트래픽은 암호화되고 인증 요청을 제한합니다. 이 프로세스에 대한 자세한 내용은 통과 인증에 대한 [보안 심층 분석](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md)을 참조하세요.

* **사용자 환경**. 사용자의 로그인 환경을 개선하려면 통과 인증을 사용하는 원활한 SSO를 배포합니다. 원활한 SSO는 사용자가 로그인한 후 불필요한 프롬프트를 방지합니다.

* **고급 시나리오**. 통과 인증은 로그인 시 온-프레미스 계정 정책을 적용 합니다. 예를 들어 온-프레미스 사용자의 계정 상태가 사용 하지 않도록 설정 되었거나, 잠겨 있거나, [암호가 만료](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) 되거나 사용자가 로그인 할 수 있는 시간을 초과 하는 경우 액세스가 거부 됩니다.

    통과 인증을 사용 하는 다단계 인증을 요구 하는 조직은 MFA (Azure Multi-Factor Authentication) 또는 [조건부 액세스 사용자 지정 컨트롤](../../active-directory/conditional-access/controls.md#custom-controls-preview)을 사용 해야 합니다. 이러한 조직에서는 페더레이션을 기반으로 하는 타사 또는 온-프레미스 다단계 인증 방법을 사용할 수 없습니다. ID 보호의 유출된 자격 증명 보고서 등과 같은 고급 기능을 사용하려면 통과 인증 선택 여부에 관계없이 암호 해시 동기화를 배포해야 합니다.

* **비즈니스 연속성**. Azure AD Connect 서버의 첫 번째 에이전트 외에 두 개의 추가적인 통과 인증 에이전트를 배포하는 것이 좋습니다. 이러한 추가 배포는 인증 요청의 높은 가용성을 보장합니다. 세 개의 에이전트를 배포하면 유지 관리를 위해 에이전트 하나가 중지된 경우에도 다른 에이전트가 실패해도 괜찮습니다.

    통과 인증에 추가하여 암호 해시 동기화를 배포했을 때의 또 다른 이점은 더 이상 기본 인증 방법을 사용할 수 없을 때 백업 인증 방법으로 작동한다는 점입니다.

* **고려 사항**. 특정 온-프레미스 오류 때문에 에이전트에서 사용자 자격 증명의 유효성을 검사할 수 없는 경우 암호 해시 동기화를 통과 인증에 대한 백업 인증 방법으로 사용할 수 있습니다. 암호에 대 한 장애 조치 (failover) 해시 동기화가 자동으로 수행 되지 않으므로 Azure AD Connect를 사용 하 여 로그온 방법을 수동으로 전환 해야 합니다.

    대체 ID 지원을 포함하여 통과 인증에 대한 기타 고려 사항은 [질문과 대답](../../active-directory/hybrid/how-to-connect-pta-faq.md)을 참조하세요.

배포 단계에 대해서는 [통과 인증 구현](../../active-directory/hybrid/how-to-connect-pta.md)을 참조하세요.

### <a name="federated-authentication"></a>페더레이션 인증

* **활동**. 페더레이션 인증 시스템은 신뢰할 수 있는 외부 시스템을 통해 사용자를 인증합니다. 일부 기업은 Azure AD 하이브리드 ID 솔루션과 함께 자사의 기존 페더레이션 시스템 투자를 재사용하려고 합니다. 페더레이션 시스템의 유지 및 관리는 Azure AD의 제어를 벗어납니다. 페더레이션 시스템이 안전하게 배포되어 인증 부하를 처리할 수 있도록 하는 것은 해당 페더레이션 시스템을 사용하는 조직의 책임입니다.

* **사용자 환경**. 페더레이션 인증의 사용자 환경은 기능, 토폴로지 및 페더레이션 팜 구성의 구현 방식에 따라 달라집니다. 일부 조직에서는 페더레이션 팜에 대한 액세스를 보안 요구 사항에 맞춰 적용 및 구성할 수 있는 유연성이 필요합니다. 예를 들어 자격 증명을 확인하지 않고 사용자가 자동으로 로그인되도록 내부적으로 연결된 사용자 및 디바이스를 구성할 수 있는데, 이 구성은 해당 사용자는 이미 디바이스에 로그인되었기 때문에 작동합니다. 필요한 경우 일부 고급 보안 기능을 사용하면 사용자의 로그인 프로세스가 더 어려워집니다.

* **고급 시나리오**. 페더레이션 인증 솔루션은 일반적으로 고객에게 Azure AD에서 기본적으로 지원되지 않는 인증 요구 사항이 있는 경우 필요합니다. 자세한 내용은 [올바른 로그인 옵션 선택](https://blogs.msdn.microsoft.com/samueld/2017/06/13/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365/)을 참조하세요. 고려할 일반적인 요구 사항은 다음과 같습니다.

  * 스마트 카드 또는 인증서를 요구하는 인증
  * 페더레이션된 id 공급자를 필요로 하는 온-프레미스 MFA 서버 또는 타사 다단계 공급자
  * 타사 인증 솔루션을 사용하는 인증. [Azure AD 페더레이션 호환성 목록](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)을 참조하세요.
  * UPN (사용자 계정 이름) 대신 사용자 계정 이름 (예: user@domain.com) 대신 sAMAccountName (예: DOMAIN\username)을 요구 하는 로그인입니다.

* **비즈니스 연속성**. 페더레이션 시스템에는 일반적으로 부하 분산된 서버 배열(팜이라고도 함)이 필요합니다. 이 팜은 인증 요청에 대한 고가용성을 보장하기 위해 내부 네트워크와 경계 네트워크 토폴로지에 구성됩니다.

    암호 해시 동기화는 온-프레미스 서버를 사용할 수 없는 경우 등 더 이상 기본 인증 방법을 사용할 수 없을 때 백업 인증 방법으로 페더레이션 인증과 함께 배포할 수 있습니다. 일부 대기업 조직에서는 대기 시간이 짧은 인증 요청에 대한 지리적 DNS를 사용하여 구성된 여러 인터넷 수신 지점을 지원하기 위해 페더레이션 솔루션이 필요합니다.

* **고려 사항**. 페더레이션 시스템에는 일반적으로 온-프레미스 인프라에 더 많은 투자를 해야 합니다. 대부분의 조직은 온-프레미스 페더레이션 투자가 이미 되어 있고 단일 ID 공급자를 사용해야 하는 강력한 비즈니스 요구 사항이 있는 경우에 이 옵션을 선택합니다. 페더레이션은 클라우드 인증 솔루션에 비해 작동 및 문제 해결이 더 복잡합니다.

Azure AD에서 확인할 수 없는 라우팅할 수 없는 도메인의 경우 사용자 ID 로그인을 구현 하기 위한 추가 구성이 필요 합니다. 이 요구 사항을 대체 로그인 ID 지원이라고 합니다. 제한 사항 및 요구 사항에 대해서는 [대체 로그인 ID 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)을 참조하세요. 페더레이션에 타사 다단계 인증 공급자를 사용하도록 선택하는 경우, 디바이스에서 Azure AD에 가입할 수 있도록 공급자가 WS-Trust를 지원하는지 확인합니다.

배포 단계에 대해서는 [페더레이션 서버 배포](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/deploying-federation-servers)를 참조하세요.

> [!NOTE]
> Azure AD 하이브리드 ID 솔루션을 배포하는 경우 Azure AD Connect에서 지원되는 토폴로지 중 하나를 구현해야 합니다. [Azure AD Connect에 대한 토폴로지](../../active-directory/hybrid/plan-connect-topologies.md)에서 지원되는 구성 및 지원되지 않는 구성을 알아보세요.

## <a name="architecture-diagrams"></a>아키텍처 다이어그램

다음 다이어그램에서는 Azure AD 하이브리드 ID 솔루션에서 사용할 수 있는 각 인증 방법에 대해 필요한 상위 수준 아키텍처 구성 요소를 간략하게 설명하며, 솔루션 간의 차이점을 비교하는 데 유용한 개요를 제공합니다.

* 암호 해시 동기화 솔루션의 단순성:

    ![Azure AD 하이브리드 ID와 암호 해시 동기화](./media/choose-ad-authn/azure-ad-authn-image2.png)

* 중복성을 위해 두 개의 에이전트를 사용 하는 통과 인증의 에이전트 요구 사항:

    ![Azure AD 하이브리드 ID와 통과 인증](./media/choose-ad-authn/azure-ad-authn-image3.png)

* 조직의 경계 및 내부 네트워크에서 페더레이션에 필요한 구성 요소:

    ![Azure AD 하이브리드 ID와 페더레이션 인증](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>방법 비교

|고려 사항|암호 해시 동기화 + 원활한 SSO|통과 인증 + 원활한 SSO|AD FS로 페더레이션|
|:-----|:-----|:-----|:-----|
|인증은 어디서 수행되나요?|클라우드|클라우드에서 온-프레미스 인증 에이전트와 보안 암호 확인을 교환한 후|온-프레미스|
|프로비저닝 시스템(Azure AD Connect) 이외의 온-프레미스 서버 요구 사항은 무엇인가요?|None|각 추가 인증 에이전트마다 서버 1개|둘 이상의 AD FS 서버<br><br>경계/DMZ 네트워크에 둘 이상의 WAP 서버|
|프로비저닝 시스템 이외의 온-프레미스 인터넷 및 네트워킹 요구 사항은 무엇인가요?|None|인증 에이전트를 실행하는 서버의[아웃바운드 인터넷 액세스](../../active-directory/hybrid/how-to-connect-pta-quick-start.md)|경계에 있는 WAP 서버에 대한 [인바운드 인터넷 액세스](https://docs.microsoft.com/windows-server/identity/ad-fs/overview/ad-fs-requirements)<br><br>경계에 있는 WAP 서버에서 AD FS 서버로의 인바운드 네트워크 액세스<br><br>네트워크 부하 분산|
|SSL 인증서 요구 사항이 있나요?|예|예|yes|
|상태 모니터링 솔루션이 있나요?|필요하지 않음|[Azure Active Directory 관리 센터](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)에서 제공한 에이전트 상태|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|사용자가 회사 네트워크 내의 도메인 가입 디바이스에서 Single Sign-On 방식으로 클라우드 리소스에 액세스할 수 있나요?|[원활한 SSO](../../active-directory/hybrid/how-to-connect-sso.md)의 경우 예|[원활한 SSO](../../active-directory/hybrid/how-to-connect-sso.md)의 경우 예|yes|
|지원되는 로그인 유형은 무엇인가요?|UserPrincipalName + 암호<br><br>[원활한 SSO](../../active-directory/hybrid/how-to-connect-sso.md) 를 사용 하 여 Windows 통합 인증<br><br>[대체 로그인 ID](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + 암호<br><br>[원활한 SSO](../../active-directory/hybrid/how-to-connect-sso.md) 를 사용 하 여 Windows 통합 인증<br><br>[대체 로그인 ID](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + 암호<br><br>sAMAccountName + 암호<br><br>Windows 통합 인증<br><br>[인증서 및 스마트 카드 인증](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[대체 로그인 ID](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|비즈니스용 Windows Hello가 지원되나요?|[키 신뢰 모델](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[키 신뢰 모델](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Windows Server 2016 도메인 기능 수준 필요*|[키 신뢰 모델](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[인증서 신뢰 모델](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|다단계 인증 옵션은 무엇인가요?|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[조건부 액세스를 사용 하는 사용자 지정 컨트롤 *](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[조건부 액세스를 사용 하는 사용자 지정 컨트롤 *](../../active-directory/conditional-access/controls.md)|[Azure MFA](https://docs.microsoft.com/azure/multi-factor-authentication/)<br><br>[Azure MFA 서버](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[타사 MFA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[조건부 액세스를 사용 하는 사용자 지정 컨트롤 *](../../active-directory/conditional-access/controls.md)|
|지원되는 사용자 계정 상태는 무엇인가요?|비활성화된 계정<br>(최대 30분 지연)|비활성화된 계정<br><br>계정 잠김<br><br>계정이 만료됨<br><br>암호 만료됨<br><br>로그인 시간|비활성화된 계정<br><br>계정 잠김<br><br>계정이 만료됨<br><br>암호 만료됨<br><br>로그인 시간|
|조건부 액세스 옵션은 무엇 인가요?|[Azure AD 조건부 액세스, Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Azure AD 조건부 액세스, Azure AD Premium](../../active-directory/conditional-access/overview.md)|[Azure AD 조건부 액세스, Azure AD Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS 클레임 규칙](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|레거시 프로토콜 차단이 지원되나요?|[예](../../active-directory/conditional-access/overview.md)|[예](../../active-directory/conditional-access/overview.md)|[예](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|로그인 페이지에서 로고, 이미지 및 설명을 사용자 지정할 수 있나요?|[예(Azure AD Premium의 경우)](../../active-directory/fundamentals/customize-branding.md)|[예(Azure AD Premium의 경우)](../../active-directory/fundamentals/customize-branding.md)|[예](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|지원되는 고급 시나리오는 무엇인가요?|[스마트 암호 잠금](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[손실된 자격 증명 보고서, Azure AD Premium P2 사용](../../active-directory/reports-monitoring/concept-risk-events.md)|[스마트 암호 잠금](../../active-directory/authentication/howto-password-smart-lockout.md)|다중 사이트 낮은 대기 시간 인증 시스템<br><br>[AD FS 엑스트라넷 잠금](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[타사 ID 시스템과 통합](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Azure AD 조건부 액세스의 사용자 지정 컨트롤은 현재 장치 등록을 지원 하지 않습니다.

## <a name="recommendations"></a>권장 사항
ID 시스템은 마이그레이션하여 클라우드에서 사용할 수 있도록 만든 클라우드 앱 및 LOB(기간 업무) 앱에 사용자가 액세스할 수 있도록 해줍니다. 권한 있는 사용자가 생산성을 유지하고 악의적인 행위자가 조직의 중요한 데이터에 접근하지 못하도록 하기 위해 인증은 앱에 대한 액세스를 제어합니다.

다음과 같은 이유로, 선택한 인증 방법에 관계없이 암호 해시 동기화를 사용하거나 사용하도록 설정합니다.

1. **고가용성 및 재해 복구**. 통과 인증 및 페더레이션은 온-프레미스 인프라에 의존합니다. 통과 인증의 경우, 통과 인증 에이전트에 필요한 서버 하드웨어 및 네트워크가 온-프레미스 공간에 포함됩니다. 페더레이션의 경우, 경계 네트워크의 서버에서 인증 요청 및 내부 페더레이션 서버를 프록시해야 하므로 온-프레미스 공간이 훨씬 커집니다.

    단일 실패 지점이 발생 하지 않도록 하려면 중복 서버를 배포 합니다. 그러면 어떤 구성 요소가 실패한 경우에도 인증 요청이 항상 처리됩니다. 또한 통과 인증과 페더레이션 둘 다에서 도메인 컨트롤러를 사용하여 인증 요청에 응답하며, 실패할 수도 있습니다. 이러한 많은 구성 요소를 정상적인 상태로 유지하려면 유지 관리가 필요하며, 유지 관리가 제대로 계획 및 구현되지 않은 경우 중단이 발생할 가능성이 커집니다. Microsoft Azure AD 클라우드 인증 서비스는 전 세계로 확장되어 있으며 항상 사용할 수 있기 때문에 암호 해시 동기화를 사용하면 중단을 방지할 수 있습니다.

2. **온-프레미스 중단에서 생존**.  사이버 공격이나 재해로 인한 온-프레미스 중단은 브랜드 이미지 손상부터 해당 공격을 처리할 수 없을 정도의 조직 마비에 이르기까지 막대한 영향을 줄 수 있습니다. 최근 대부분의 조직에서는 온-프레미스 서버 작동이 중단 된 대상 랜 섬 웨어를 비롯 한 맬웨어 공격을 교착 상태가 발생 했습니다. 고객이 이러한 종류의 공격을 처리할 수 있도록 지원하면서 Microsoft는 두 가지 범주의 조직이 있음을 알았습니다.

   * 이전에 암호 해시 동기화를 설정한 조직은 암호 해시 동기화를 사용하도록 인증 방법을 변경하여 몇 시간 만에 다시 온라인 상태가 되었습니다. Office 365를 통한 이메일 액세스를 사용하여 문제를 해결하고 다른 클라우드 기반 워크로드에 액세스할 수 있었습니다.

   * 이전에 암호 해시 동기화를 사용 하도록 설정 하지 않은 조직은 통신을 위해 신뢰할 수 없는 외부 소비자 메일 시스템을 사용 하 여 문제를 해결 해야 했습니다. 이러한 경우, 사용자가 클라우드 기반 앱에 다시 로그인 할 수 있기 전에 온-프레미스 id 인프라를 복원 하는 데 몇 주가 걸렸습니다.

3. **ID 보호**. Azure AD Premium P2가 있는 Azure AD Identity Protection은 클라우드에서 사용자를 보호하는 가장 좋은 방법 중 하나입니다. Microsoft는 악의적인 행위자가 판매하고 다크 웹에서 사용할 수 있도록 만드는 사용자 및 암호 목록에 대해 인터넷을 지속적으로 검사합니다. Azure AD는 이 정보를 사용하여 사용자 조직의 사용자 이름 및 암호가 훼손되었는지 확인할 수 있습니다. 따라서 사용 하는 인증 방법 (페더레이션 또는 통과 인증)에 관계 없이 암호 해시 동기화를 사용 하도록 설정 하는 것이 중요 합니다. 유출된 자격 증명은 보고서로 제공되므로 사용자가 유출된 암호로 로그인하려고 할 때 이 정보를 사용하여 해당 사용자를 차단하거나 유출된 암호를 변경하도록 할 수 있습니다.

## <a name="conclusion"></a>결론

이 문서에서는 조직에서 클라우드 앱에 대한 액세스를 지원하기 위해 구성하여 배포할 수 있는 다양한 인증 옵션을 설명합니다. 다양한 비즈니스, 보안 및 기술적 요구 사항을 충족하기 위해 조직은 암호 해시 동기화, 통과 인증 및 페더레이션 중에서 선택할 수 있습니다.

비즈니스 요구 사항이 솔루션을 배포 하는 데 필요한 작업을 수행 하 고 로그인 프로세스의 사용자 경험으로 비즈니스 요구 사항을 해결 하나요? 또한 조직에 각 인증 방법의 고급 시나리오 및 비즈니스 연속성 기능이 필요한지 여부도 평가해야 합니다. 마지막으로, 각 인증 방법에서 고려할 사항을 평가하여 선택한 방법을 구현하지 못하도록 하는 요소가 있는지 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

오늘날의 세계에는 하루 24시간 어디에나 위협이 존재합니다. 올바른 인증 방법을 구현하면 보안 위험을 완화하고 사용자의 ID를 보호하는 데 도움이 됩니다.

Azure AD로 [시작](../../active-directory/fundamentals/get-started-azure-ad.md)하여 조직에 맞는 올바른 인증 솔루션을 배포하세요.

페더레이션 인증에서 클라우드 인증으로 마이그레이션을 고려하는 경우 [로그인 방법 변경](../../active-directory/hybrid/plan-connect-user-signin.md)에 대해 자세히 알아보세요. 마이그레이션을 계획 하 고 구현 하는 데 도움이 되도록 [이러한 프로젝트 배포 계획](https://aka.ms/deploymentplans) 을 사용 하거나 새로운 [준비 된 롤아웃](../../active-directory/hybrid/how-to-connect-staged-rollout.md) 기능을 사용 하 여 준비 된 방법으로 페더레이션된 사용자를 클라우드 인증 사용으로 마이그레이션해야 합니다.
